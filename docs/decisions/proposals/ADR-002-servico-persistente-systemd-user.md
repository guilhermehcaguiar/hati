# ADR-002 — Serviço persistente: daemon hatid + systemd --user

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** DT-05/R1 — fechar a GUI não encerra serviço/execuções; logout,
  desligamento, suspensão e crash tratados explicitamente (WS-03/20, TM-07/08/09/11,
  AX-30, TM-25); PTYs reanexáveis (TM-03/04); identidade de processos comprovada (TM-11);
  base para mobile (DT-10) e Windows (F6). A auditoria registra que o modelo embutido da
  GUI não cumpre DT-05 e que o Linux do Alethe sofreu com morte de processos (#205).
- **Alternativas consideradas:**
  1. **Daemon separado Rust sob systemd `--user`** — sobrevive à janela e (com linger) ao
     logout; journal/restart de graça; rastreamento de processos por cgroup; a GUI vira
     cliente reanexável (mesma API do mobile). Custo: contrato IPC + dois processos.
  2. **Backend embutido na GUI** — viola DT-05 ou vira "esconder janela"; sem
     supervisionador; reconstrução futura.
  3. **Daemon + tmux por baixo** — reattach maduro, mas camada extra de protocolo e sem
     valor para jobs headless (ADR-005).
  Evidência-chave: `loginctl(1)` (systemd 262, Arch): `enable-linger` mantém o user
  manager após logout; `KillUserProcesses=` governa processos de sessão. Lição #205:
  cgroups > kill de grupo de processos.
- **Decisão (recomendação):** daemon **`hatid`** (Rust) como unidade `hatid.service` de
  systemd `--user`, com **linger opt-in oferecido no onboarding** (não automático);
  IPC por Unix socket local (peer credentials + token); cada PTY/job em **cgroup scope
  próprio** (`systemd-run --user --scope`, propriedade `hati.job.id`); encerramento
  gracioso SIGTERM→prazo→SIGKILL; ceifa de órfãos no boot por identidade (env + cgroup);
  scrollback persistido por PTY. O formato do RPC (JSON-RPC vs gRPC) fica fixado na
  implementação da PoC-01 (ADR-011 futuro).
- **Experimento/PoC:** PoC-01 (gate da Fase 1): janela fechada mantém jobs; logout+login
  com linger; kill do daemon sem órfãos; suspend/resume com estados explícitos; e2e smoke
  em CI Linux.
- **Impacto:** Linux: primeira classe (systemd presente em Arch/Omarchy). Windows (F6):
  mesmo contrato, Job Objects no lugar de cgroups (TM-10/13 adiados lá — sem mudança de
  escopo). Mobile (F5): o RemoteGateway só existe porque o daemon é separado. IDs: todos
  os TM críticos, WS-03/20, AX-30, AG-04. Riscos: complexidade IPC (mitigada por
  contrato versionado); comportamento de suspensão das CLIs (estados explícitos + resume).
- **Consequências:** melhor: DT-05 por construção, auditabilidade (journal), base única
  para desktop/mobile, morte segura de árvores. Pior: mais um contrato para versionar e
  dois processos no sistema. Observar: latência de spawn de scopes em carga e o custo do
  journal em PTYs verbosos.
