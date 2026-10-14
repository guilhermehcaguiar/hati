# Segurança e runtime do HATI — PROPOSTA

> **Status: PROPOSTA (Prompt 03).** Processos, isolamento, autorização, segredos e
> transporte remoto. Deriva de Q2/Q7/Q8/Q9 do TECHNICAL_STUDY; ADRs 002/006/007/008.
> Princípio declarado (CROSS_REVIEW/AG-03): **guardas de UI são facilitação, não sandbox
> absoluto** — o HATI declara limites honestamente e empilha enforcement real onde existe.

- **Data:** 15/10/2026 · **Autora:** Athena

## 1. Modelo de ameaças (resumo honesto)

| # | Ameaça | Superfície | Postura proposta |
|---|---|---|---|
| A1 | Agente com shell livre contorna guardas da UI | CLIs no PTY | Inegável; mitigação em camadas: permission modes/sandboxes das CLIs (enforcement real), worktrees isolam escrita (F3), policies HX-21 avisam; UI declara o limite (PL-02-style) |
| A2 | Código de plugin não confiável | PluginHost | Child webviews + sidecars com capabilities; storage segregado; nada na WebView principal (Q8) |
| A3 | Vazamento de segredos em eventos/logs/backups | Emissão de eventos, diagnósticos, export | Sanitização no emissor; keyring único dono; export nunca inclui keyring (AX-20); logs sanitizados (AX-25) |
| A4 | Transporte remoto desprotegido | Mobile | RC-04 **descartado**: TLS obrigatório em qualquer modo (Q9) |
| A5 | Dispositivo pareado comprometido | RemoteGateway | Escopos mínimos (read-only default — RC-02/03), expiração, revogação imediata, lockout, auditoria (RC-06) |
| A6 | Git automatizado (commit/push/merge por agente) | GitService | Ações de escrita **fora do contrato automatizável**; só existem como fluxo humano auditado (HX-22/DT-06) |
| A7 | Órfãos/processos zumbis após crash/logout | PTYs | cgroup scopes + sweep por identidade no boot + journal do systemd (Q2) |
| A8 | Update malicioso | Distribuição | Assinatura (minisign/ed25519) + **opt-in explícito** por update (AX-23); rejeição de binário adulterado testada |
| A9 | Corrupção de estado | hati.db | WAL + backups rotativos + export validado; crash = estado explícito "perdido", nunca silêncio |

## 2. Processos e ciclo de vida (Q2/ADR-002)

- **`hatid`** (Rust): unidade systemd `--user` `hatid.service`; `Restart=on-failure`;
  journal integrado; **linger oferecido no onboarding** (explicado; nunca ligado
  silenciosamente — AX-24).
- **PTYs/jobs:** `systemd-run --user --scope` por unidade de execução com
  `hati.job.id=<uuid>`; identidade comprovada por cgroup (TM-11: nunca por nome/PID).
- **Casos explícitos (DT-05):**
  - *Fechar janela:* daemon segue; notificações nativas continuam (AX-31); reabrir =
    reattach por cursor + tail UTF-8 (TM-04).
  - *Logout:* com linger, daemon e jobs sobrevivem; sem linger, aviso antes do logout
    mostrar o que morreria (AX-30).
  - *Suspensão/hibernação:* processos pausam com o SO; no resume, estados
    `suspenso|perdido` explícitos + reconciliação + `--resume` das CLIs (AG-04).
  - *Desligamento:* encerramento gracioso (SIGTERM→prazo→SIGKILL por scope); no boot,
    sweep marca sobreviventes como órfãos ceifáveis com revisão (TM-25).
  - *Crash do daemon:* systemd reinicia; sweep reconcilia por cgroup; scrollback
    persistido permite reattach visual.
- **Windows (F6):** mesmo contrato; Job Objects assumem o papel de cgroups (TM-10/13).

## 3. Autorização (Q7/ADR-006)

- **JobGate (DT-04):** `Job` nasce `pending_approval`; spawn bloqueado até `Approval`
  humana; a fila unificada (HX-19) é a única porta; aprovação remota apenas com escopo
  `approve` no dispositivo (RC-03).
- **Prévia obrigatória (HX-14):** escopo + permissões efetivas + sandbox + custo estimado
  antes do botão de confirmação.
- **Tool calls internos:** seguem permission modes/sandboxes da CLI (matriz em
  AGENT_INTEGRATIONS §2); perfis HX-05 por projeto; policies HX-21 por tarefa; combinação
  efetiva visível; **nenhum bypass automático de flag permissiva (AG-03)**.
- **Auditoria (HX-23):** `Approval` registra autor/ação/projeto/job/arquivos/instante;
  append-only; sem segredos; exportável na revisão humana.

## 4. Git e worktrees (HX-22/GT-*)

- **Contrato do daemon p/ automação = leitura:** status/diff/log/graph/worktree-list.
- **Commit/push/merge/rebase/reset:** fluxo **humano** na UI com confirmação explícita e
  trilha; **inexistentes** no contrato mobile (DT-06); a política de bloqueio nos
  *agentes* é convenção da Agent Suite que o HATI **documenta e exibe**, não garantia
  técnica do HATI (A1 declarado).
- **Worktrees (F3):** provisionadas por job; remoção exige revisão de pendências (GT-05);
  diff de resultado = `Evidence` com fonte confiável (HX-17: o diff computado pelo HATI,
  não o texto do agente).

## 5. Segredos e dados (R4/DT-07/MC-05)

- Segredos **somente** no keyring do OS; nunca em `hati.db`, eventos, logs, export, config
  versionada. Reveal individual e temporário na UI (MC-05); remoção de keyring no opt-out.
- Dados do HATI em XDG (`~/.local/share/hati`, `~/.config/hati`, `~/.local/state/hati`) —
  **nunca** dentro de repositórios de projetos (DT-07); escrita de arquivos de agente em
  repo (`AGENTS.md`/`CLAUDE.md`/`.mcp.json`) é opt-in explícito por ação (WS-13/MC-03).
- Escrita segura em configs de terceiros (R14, fase MC): parse → guard → preview → backup
  → atomic write → self-check (invariantes herdados como *especificação*, não código —
  auditoria §MC).

## 6. Mobile e transporte (Q9/ADR-008)

- **Somente TLS:** LAN = cert próprio do HATI (CA local + pinning no pareamento);
  fora de casa = **Tailscale serve** no tailnet (certs `*.ts.net`); funnel (público)
  **não usado**.
- **Pareamento:** QR com token efêmero (janela curta) → `PairedDevice` com escopos
  (RC-03), expiração default, lockout por tentativas, revogação imediata (RC-06).
- **Sessão remota:** token de dispositivo + rotação; ACL aplicada no RemoteGateway sobre
  a mesma API do daemon; **Git de escrita fora do contrato**; read-only por default
  (RC-02); input escalonado por opt-in.
- **Sem cloud relay obrigatória** (DT-10): host ligado + daemon ativo.

## 7. Plugins (Q8/ADR-007)

- Manifesto com capabilities; runtime: **child webviews** (UI) + **sidecar process**
  (backend), API reduzida por capability; storage por plugin com quota (PL-05); catálogo
  futuro com SHA-256 + review de permissões (PL-03); import local desabilitado por
  default (PL-04); um plugin **não vê** store/dados de outro; e2e de segurança no
  VALIDATION_PLAN.

## 8. Hardening de superfícies locais

- IPC: UDS + peer credentials + token por instância; nada de TCP sem TLS no daemon.
- OpenCode serve: bind loopback + senha em env gerenciado (nunca em config versionada).
- CSP estrita na WebView (default-src 'self'); IPC tipado com deny-by-default por
  capability (padrão Tauri 2).
- Diagnósticos exportáveis **sanitizados** (AX-25); telemetria local jsonl com cap e
  sem uploader (AX-26); reset seletivo.
- Updater: assinado, opt-in por release, changelog visível (AX-23/24).

## 9. Verificação futura (Aegis — com autorização, em fase própria)

- Revisão da superfície IPC (enumeração de métodos × capabilities).
- Teste A1 prático: agente em PTY tentando `git commit` — expectativa documentada:
  **possível** (shell livre) → o HATI detecta e **exibe** o fato, mas não promete
  bloqueio; relatório explícito para o usuário.
- Fuzz de EventEnvelope (schema_version inválidos) e do parser de scrollback.
- Pen-test do RemoteGateway (revogação, replay de token, TLS mínimo).

**Aprovação humana necessária:** ADRs 002/006/007/008 + postura A1 (declaração de
limites) + política de linger opt-in + escolha de canais de distribuição/assinatura.
