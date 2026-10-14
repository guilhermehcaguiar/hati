# Plano de validação do HATI — PROPOSTA

> **Status: PROPOSTA (Prompt 03).** Critérios de aceitação e PoCs **futuras** — nenhuma foi
> executada nesta etapa (proibição do Prompt 03). Este plano é o gate de saída da Fase 0
> para a Fase 1 e o gabarito de qualidade contínuo. Deriva de Q12/Q13 e dos critérios de
> cada questão do TECHNICAL_STUDY.

- **Data:** 15/10/2026 · **Autora:** Athena

## 1. PoCs priorizadas (pós-aprovação dos ADRs — F0.5)

| # | Nome | Escopo | Critério de aprovação (mensurável) | Risco que mata |
|---|---|---|---|---|
| PoC-01 | **Daemon que sobrevive** | hatid mínimo + PTY + systemd --user + linger + e2e smoke em CI | (1) fechar GUI mantém 3 jobs 10min; (2) logout+login Wayland com linger mantém daemon; (3) kill do daemon = zero órfãos (cgroups vazios); (4) suspend/resume com estados explícitos; (5) e2e mínimo verde em runner Linux Arch-like | R1/DT-05 inviável → repensar ADR-002 antes de construir UI |
| PoC-02 | **OpenCode 1ª classe** | serve + SSE + SDK; contratos v2 | sessões do cwd listadas; sessão criada; `session.idle` recebido; permissão respondida; custo local visível; **contratos v2 conferidos** | R6 sem base real |
| PoC-03 | **Codex via app-server** | stdio JSONL + sandbox + steer/interrupt + aprovações | thread workspaceWrite; steer+interrupt ok; `requestApproval` chegando à fila; schema `generate-ts` compila no CI | approvals/sandbox inexistentes na prática |
| PoC-04 | **Claude headless** | stream-json + resume + custos | run com stream-json; resume por id; custo **rotulado estimado**; hook Stop → notificação | estimativa sem rótulo; protocolo frágil |
| PoC-05 | **Kanban em SQLite** | hati.db + 10k tarefas + migrações + backup | filtros/ordenação <100ms; migração v1→v2 testada; backup+restore idêntico; kill -9 não corrompe (WAL) | R8/Kanban inviável no formato |
| PoC-06 | **Terminal em WebKitGTK** | xterm.js (webgl+canvas) em Tauri/Wayland/Hyprland | 60fps @100k linhas scrollback; IME pt-BR; clipboard; resize; drag&drop; **AppImage roda em Omarchy limpo** | Q1: aciona plano B (Electron) com critério objetivo |
| PoC-07 | **iPhone via tailnet** (F5) | PWA + Tailscale serve + pareamento | iPhone pareado vê Kanban e aprova job; revogação <5s; sem TLS = recusa; nenhuma rota Git de escrita no contrato (teste) | R11/DT-10 |
| PoC-08 | **Eventos sob carga** (com F1) | bus + SSE + replay | 5k eventos/s por 60s sem latência perceptível; reconexão por cursor sem lacunas; replay 100k reconstrói snapshot idêntico | R9 |

**Ordem:** 01→02→03→04→06 (F0.5); 05/08 no arranque da F1; 07 na F5. Cada PoC gera ADR de
resultado (aceito/reprovado) — o "experimento" exigido pela convenção de ADRs.

## 2. Critérios de aceitação por componente (resumo)

| Componente | Critério-chave (alvo inicial) |
|---|---|
| PTY executor | attach tail 512KB UTF-8 íntegro; resize sem deadlock; kill gracioso <2s p/ 95% das árvores; batching 16ms/64KB sem perda |
| Eventos | 100% de transições de job/task viram evento; consumer atrasado reconstrói por cursor; zero segredos (grep automático no payload de teste) |
| Store | migrações reversíveis testadas em CI; backup consistente com base aberta; WAL sob kill -9 |
| Adaptadores | capabilities detectadas por versão; degradação com aviso; sem inventar capacidade ausente (UI mostra "não suportado") |
| PolicyEngine | invariante "sem Approval não há spawn" verificado por teste que inspeciona o fonte (padrão auditoria §14) + e2e do fluxo HX-14 |
| GitService | escrita inexistente no contrato de automação (enumeração de métodos testada); diff de evidência computado pelo HATI |
| GUI | i18n: build falha com chave faltando (AX-16); 60fps no terminal; reattach <1s p/ 500KB |
| Mobile (F5) | DT-06 respeitado (nenhuma rota Git); revogação imediata; expiração funcionando |

## 3. Cenários de regressão obrigatórios (suítes permanentes)

1. **PTY:** UTF-8 partido em chunk boundary; flush antes de suspend; restart preserva id;
   órfãos de sessão anterior ceifados por identidade (cgroup) — nunca por nome/PID.
2. **Ciclo de vida:** fechar janela/logout/suspend/crash — cada um com estado final
   explícito e verificável (Q2).
3. **Kanban:** mover cartão nunca gera spawn (DT-04) — teste de invariante na UI + daemon.
4. **Não-equiparação:** job concluído não altera status da tarefa (HX-10) — invariante.
5. **Caso extremo do DATA_MODEL §4** (3 jobs/2 sessões/1 worktree/retry) como e2e dourado.
6. **Segurança:** export sem segredos; evento sem segredo; plugin sem capacidade declarada
   rejeitado; update adulterado rejeitado.

## 4. Estratégia de testes e CI (Q12 — proposta)

- **Pirâmide:** unit Rust (cargo test) + unit TS (Vitest) → integração do daemon (IPC
  sobre socket real; adaptadores contra **fixtures gravadas** dos protocolos — sem rede
  em CI) → e2e do app empacotado (WebdriverIO + tauri-driver; WebKitGTK headless no
  runner) → smoke de distribuição (AppImage em contêiner Arch limpo).
- **Gates de merge:** clippy (deny warnings em módulos novos), `tsc --noEmit`, ESLint,
  testes, e2e mínimo, testes de invariantes.
- **Anti-doc-drift:** docs de segurança/privacidade derivadas de fonte único ou
  validadas por teste (ex.: "consentimento off = zero acesso" testado; PRIVACY.md gerado
  de fatos testáveis) — lição auditoria §13/§29.6.
- **Ambientes:** runner Linux Arch-like para e2e; matriz de versões das 3 CLIs
  (faixas declaradas dos adaptadores) validada 1×/release.
- **Benchmarks (Chronos, futuramente, com autorização):** scrollback 100k/1M; 50 PTYs
  ativos; eventos 5k/s; RAM do daemon <150MB com 10 projetos abertos (alvo inicial
  mensurável, ajustável).

## 5. Escolha de bibliotecas — critérios e estado (fetch 15/10/2026)

| Área | Candidato recomendado | Estado verificado | Alternativa |
|---|---|---|---|
| PTY | `portable-pty` (wezterm) | validada em produção pelo Alethe (auditoria §3) | `nix` crate (Unix-only, bloquearia Windows) |
| SQLite | `rusqlite` 0.40.1 (bundled 3.53.2, backup/SQLCipher, MIT) | docs oficiais 15/10/2026 | sqlx (async não necessário — daemon é o único escritor) |
| Git | `git2-rs` (libgit2: produção, cross-platform, GPLv2+exception) | libgit2.org — usado por GitHub/Microsoft | `gix` (gitoxide): rápido, mas gix "usable/initial development" com churn de API (STABILITY.md) — usar seletivamente p/ perf, não como base |
| Terminal UI | `@xterm/xterm` + addons (fit/search/webgl/canvas) | xtermjs.org — padrão de mercado | — |
| Shell desktop | Tauri 2 | v2.tauri.app (jul/2026) — audits, distribuição Linux | Electron (plano B ADR-001) |
| Estado (GUI) | Zustand (+ TanStack Query p/ cache do daemon) | conhecimento estável — fixar em ADR-010 | Redux Toolkit (mais cerimônia) |
| Servidor HTTP interno (se>UDS) | axum/tokio | conhecimento estável — fixar na implementação | — |

## 6. Protocolo de execução das PoCs (transição F0→F1)

1. Usuário aprova ADRs desta proposta (total ou parcialmente).
2. Nova etapa autorizada: PoCs 01/02/03/04/06 como spikes de código **descartáveis**
   (fora do trunk ou em branch experimental), cada uma com ADR de resultado.
3. Falha crítica em PoC → reabrir a questão correspondente (ex.: PoC-06 reprovar
   WebKitGTK ativa o plano B documentado — sem reabertura silenciosa).
4. PoCs aprovadas + ADRs aceitos → Fase 1 começa com o contrato já validado.

**Aprovação humana necessária:** este plano de validação; a autorização das PoCs (cada uma
é uma etapa futura com escopo próprio); os orçamentos/metas numéricos (ajustáveis).
