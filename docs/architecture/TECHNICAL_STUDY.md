# Estudo técnico comparativo do HATI — PROPOSTA

> **Status: PROPOSTA (Prompt 03).** Nada neste documento é decisão aprovada. Este estudo
> compara alternativas e recomenda, com evidências, caminhos arquiteturais para avaliação
> humana. Nenhuma DT (DT-01 a DT-10) e nenhum status da matriz de 187 IDs foi alterado.
> ADRs correspondentes estão em `docs/decisions/proposals/` com status "proposta".

- **Data:** 15/10/2026 · **Autora:** Athena · **Fase:** arquitetura (pré-implementação)
- **Método:** leitura integral da documentação canônica (`docs/hati-context/` + 14 canônicos)
  + investigação em documentação oficial das tecnologias citadas (fetch direto, sem shell,
  sem PoCs — proibidas nesta etapa).
- **Fontes de evidência:** listadas por questão e consolidadas no Apêndice A; limitações da
  pesquisa no Apêndice B.

## Sumário executivo — recomendações por questão

| # | Questão | Recomendação (proposta) | ADR |
|---|---|---|---|
| Q1 | Shell de desktop | **Tauri 2** (WebKitGTK no Linux) + frontend React/TS; Electron como plano B documentado | ADR-001, ADR-010 |
| Q2 | Serviço persistente | **Daemon Rust separado** gerenciado pelo systemd `--user` (linger opcional); PTYs, jobs e estado fora da janela | ADR-002 |
| Q3 | Persistência | **SQLite (WAL, rusqlite)** para dados estruturados + arquivos de scrollback + JSON versionado para preferências | ADR-003 |
| Q4 | Eventos | **Log de eventos append-only versionado** no SQLite, streaming SSE/WS para clientes, snapshots materializados e replay | ADR-004 |
| Q5 | Adaptadores de agentes | Camada com **capacidades declaradas por provedor**: OpenCode HTTP server/SDK (1ª classe), Codex app-server (JSON-RPC), Claude Agent SDK/headless; PTY como fallback universal | ADR-005 |
| Q6 | Modelo de entidades | Projeto, tarefa, job, terminal, sessão de CLI, worktree, evidência, revisão/aprovação, dispositivo — **nunca 1:1**; detalhe em `DATA_MODEL.md` | — |
| Q7 | Autorizações | **PolicyEngine no daemon**: todo novo job exige aprovação humana (DT-04); Git de escrita fora da superfície automatizável; honestidade sobre limites do shell livre | ADR-006 |
| Q8 | Plugins | Manifesto + capacidades + aprovação; **isolamento real** (child webviews para UI, processos separados para backend); contratos desde cedo, catálogo depois (DT-08) | ADR-007 |
| Q9 | Mobile | **PWA servida pelo daemon sobre TLS/WSS**; pareamento QR + registro de dispositivos com revogação; transporte: LAN TLS ou `tailscale serve`; RC-04 descartado | ADR-008 |
| Q10 | Usage | **Local-first**: OpenCode (server/DB local), Claude (custos estimados do headless, marcados), Codex (app-server); consentimento default off (US-09) | ADR-009 |
| Q11 | Graphify | **Adiar** para fase tardia com estudo próprio; extensões MCP (Tasks/Skills) cobrem parte da necessidade | — |
| Q12 | Qualidade | e2e Linux real (WebdriverIO+Tauri), CI com contêiner Arch, suíte de regressão de PTYs, testes de invariantes, anti-doc-drift | — |
| Q13 | Roadmap técnico | Manter fases 0–6; ajustes recomendados em seção própria (daemon-first, eventos desde a Fase 1, PoCs como saída da Fase 0) — **sem reclassificar IDs** | — |
| Q14 | Empacotamento | **AppImage + AUR** (Arch 1ª classe); dados em XDG; update assinado opt-in (AX-23); backup zip validado; migração por export/import | — |

Cada questão abaixo segue o formato exigido: problema/requisitos, alternativas, prós/contras,
evidências, impactos, riscos/incertezas, recomendação, critérios de validação, necessidade de
PoC e o que depende de aprovação humana.

---

## Q1 — Shell de desktop e frontend

**Problema e requisitos (DT/IDs):** escolher a shell que hospedará a GUI do HATI em
Linux/Wayland/Hyprland como plataforma de primeira classe (R5, TM-21), com atualização
assinada (AX-23), empacotamento Linux e caminho para Windows futuro (Fase 6). A frontend
(estado, componentes, layouts) deve ser avaliada sem assumir React como decisão definitiva.

**Alternativas tecnicamente viáveis:**

| Alternativa | Prós | Contras |
|---|---|---|
| **Tauri 2** (WebView nativa via WRY/TAO; backend Rust) | Binário ~600KB; usa WebKitGTK no Linux; segurança auditada (Radically Open Security); plugins oficiais (updater, single-instance, notification, dialog); distribuição Linux completa (AppImage, AUR, deb, RPM, Snap, Flatpak); Rust compartilhado com o daemon | WebKitGTK tem quirks próprios (DMABUF, IME, WebGL) — a auditoria do Alethe registra workaround `WEBKIT_DISABLE_DMABUF` e issues Linux reais (#205/#192/#111); menor uniformidade entre plataformas que Chromium |
| **Electron** (Chromium + Node embutidos) | Motor idêntico em todo OS — menos quirks de renderização; ecossistema gigante; Node no backend | ~150–250MB e centenas de MB de RAM extras; binário pesado para um app denso de terminais; contraria o princípio de leveza Omarchy |
| **GTK4/Adwaita nativo** (Rust) | Integração Linux/Wayland máxima; zero WebView | Custo de UI muito maior para terminal (sem xterm.js), Kanban drag&drop, markdown/mermaid; ecossistema de componentes menor; desenvolvimento mais lento |
| **Flutter desktop** | UI consistente | Motor próprio não usa WebView nativa (menor integração Linux); ecossistema de terminal/Kanban fraco; Dart fora do repertório da suite |

**Evidências (fetch 14–15/10/2026):** Tauri 2 — `v2.tauri.app/start` ("minimal Tauri app
can be less than 600KB", TAO/WRY, audit report Radically Open Security) e
`v2.tauri.app/distribute` (AppImage, AUR, Debian, RPM, Snap, Flatpak + signing Linux);
Electron — `electronjs.org/docs/latest` (v44.7.2, "embedding Chromium and Node.js into its
binary"); quirks WebKitGTK e issues Linux do Alethe —
`docs/hati-context/source/ALETHE_AUDIT_ORIGINAL.txt` §3, §13 e notas do módulo TM; xterm.js
roda em WebView padrão de mercado (`xtermjs.org`, usado por VS Code/Theia).

**Impactos:** desempenho — Tauri consome bem menos RAM/tempo de início que Electron
(prioridade §8 do Prompt 03); segurança — CSP estrito + IPC tipado do Tauri servem a R2/R3;
manutenção — quirks WebKitGTK exigem suíte e2e Linux real (R5) e PoC cedo; o risco de
renderização é o principal argumento pró-Electron e deve ser testado antes de fixar a
escolha.

**Riscos e incertezas:** comportamento WebGL do xterm.js em WebKitGTK sob Hyprland não
verificado nesta etapa (fallback canvas/ATLAS existe no próprio xterm.js); IME/clipboard e
drag&drop com Wayland precisam de validação; a auditoria do Alethe (Tauri 2) mostra que o
Linux era sua plataforma *menos* validada — o HATI inverte isso com CI/e2e próprios, mas
herda o dever de prova.

**Recomendação (proposta):** **Tauri 2** com frontend **React + TypeScript** (Vite), por:
alinhamento ao perfil de consumo Omarchy; backend Rust compartilhando crates com o daemon
(Q2); contratos TS dos três provedores (OpenCode/Codex/Claude têm SDKs e schemas TS) e
xterm.js como ativo direto. React é recomendado pelo ecossistema (terminais, virtualização
de listas Kanban, dnd) e pela afinidade da Agent Suite (Pixel) — **não por presunção**: o
contrato real da GUI é a API do daemon; o framework é reversível com custo moderado (ADR-010
documenta o trade-off vs. Svelte/Solid). Electron permanece como **plano B documentado**,
acionável se a PoC-06 revelar defeitos WebKitGTK intransponíveis (critério objetivo no
ADR-001).

**Critérios de validação:** ver `VALIDATION_PLAN.md` — PoC-06 (xterm.js WebGL+canvas em
WebKitGTK/Wayland: 60fps com scrollback de 100k linhas, IME pt-BR, clipboard, resize);
empacotamento AppImage/AUR produzido por CI; janela com decorations corretas em Hyprland.

**PoC necessária?** **Sim — PoC-06 é gate da Fase 1** (e critério de ativação do plano B).

**Depende de aprovação humana:** **Sim** — ADR-001 (Tauri 2) e ADR-010 (React/TS), cada um
registrado separadamente.

---

## Q2 — Serviço persistente, PTYs e ciclo de vida

**Problema e requisitos:** R1/DT-05 — serviço de execução (PTYs, jobs, sessões) independente
da janela; fechar a GUI não mata nada; logout, desligamento, suspensão e crash tratados
explicitamente (WS-03, WS-20, TM-07/08/09/11, AX-30, TM-25); reanexação de terminais
(TM-03/04/14); encerramento gracioso antes de forçar; identificação de processos por
identidade comprovada (nunca por nome/PID ambíguo — TM-11); portabilidade Windows futura.

**Alternativas:**

| Alternativa | Prós | Contras |
|---|---|---|
| **Daemon separado (Rust) + systemd `--user`** | Cumpre DT-05 por construção; sobrevive ao fechamento da GUI e (com linger) ao logout; supervisionável pelo systemd (journal, restart); PTYs fora do processo da GUI; a GUI vira cliente reanexável — mesmo modelo do mobile (Q9) | Dois processos para instalar/gerenciar; mais um contrato (IPC) para versionar |
| **Backend embutido na GUI** (estilo Alethe) | Um processo só; IPC interno mais simples | Fechar a janela mataria tudo (viola DT-05) ou exigiria truques de "esconder janela"; órfãos/crash sem supervisionador; reconstrução para o mobile depois |
| **Daemon + tmux/screen por baixo** | Reattach maduro de terminal | Camada extra com protocolo próprio a parsear; perde controle fino de scrollback/estados; agentes headless (Q5) não precisam de terminal para serem dirigidos; dependência externa a mais |

**Evidências:** `man.archlinux.org/man/loginctl.1` (systemd 262, Arch core): `enable-linger`
— "a user manager is spawned for the user at boot and kept around after logouts. This allows
users who are not logged in to run long-running services"; `KillUserProcesses=` em
logind.conf controla processos de sessão no logout — com linger, o user manager permanece.
Auditoria do Alethe: no Linux sem equivalente do Windows Job Object, usa setsid + kill de
grupo + ceifa de órfãos no boot, e registra o bug #205 (encerrar processos derrubou a sessão
KDE inteira) — lição direta: **rastrear processos por cgroup do systemd é mais robusto que
kill de grupo de processos**.

**Impactos:** desempenho — daemon Rust/tokio com PTYs em threads dedicadas e batching de
stream (padrão comprovado 16ms/64KB, auditoria §8); segurança — fronteira de autorização
física no daemon (R2): a UI nunca é a única barreira; manutenção — systemd dá
restart/journal/healthcheck sem custo; Windows futuro — a separação daemon↔cliente é
exatamente o que a Fase 6 precisa (Job Objects no lugar de cgroups, mesmo contrato de API).

**Riscos e incertezas:** suspensão do sistema pode matar processos de agentes (necessário
estado "suspenso/perdido" explícito + reattach visual + `--resume` da CLI — modelo da
auditoria §9); linger é opt-in do usuário (o HATI pergunta e explica, nunca liga
silenciosamente — AX-24); crash do daemon exige journal + recuperação de estado pelo SQLite +
reconciliação de órfãos por identidade (env `HATI_JOB_ID` + cgroup).

**Recomendação (proposta):** daemon **`hatid`** em Rust gerenciado por unidade **systemd
`--user`** (`hatid.service`, com linger oferecido no onboarding), comunicando-se com a GUI
por **Unix socket local autenticado por peer credentials + token**; cada PTY/job em **scope
próprio** (`systemd-run --user --scope` com propriedade `hati.job.id=<uuid>`) para
rastreamento por cgroup; encerramento gracioso SIGTERM→prazo→SIGKILL; ceifa de órfãos no boot
comparando cgroups marcados; scrollback em arquivos incrementais por terminal (TM-03/04).
Detalhe em `SYSTEM_DESIGN.md` §3 e `SECURITY_AND_RUNTIME.md` §2.

**Critérios de validação:** PoC-01 — fechar a GUI mantém 3 jobs por 10min e reanexa com
scrollback íntegro; logout+login Wayland com linger mantém o daemon; kill do daemon não
deixa órfãos (cgroup vazio); suspend/resume do SO resultam em estados explícitos na UI,
nunca em zumbis silenciosos.

**PoC necessária?** **Sim — PoC-01 é gate da Fase 1** (DT-05 é requisito do núcleo).

**Depende de aprovação humana:** **Sim** — ADR-002 (daemon + systemd `--user`), incluindo a
política de linger opt-in. O uso de `systemd-run --scope` por processo é detalhe de design
documentado, não decisão de produto.

---

## Q3 — Persistência, migrações e backups

**Problema e requisitos:** armazenar projetos, Kanban (fonte única de tarefas — R8), jobs,
histórico auditável (HX-12), evidências, approvals, dispositivos, usage — com transações,
migrações versionadas e backups consistentes (AX-20); dados do HATI fora dos repositórios dos
projetos por padrão (DT-07/R4); segredos nunca versionados nem exportados por acidente
(R4, MC-05).

**Alternativas:**

| Alternativa | Prós | Contras |
|---|---|---|
| **SQLite (WAL) para dados estruturados + arquivos para scrollback + JSON versionado para preferências** | Transações reais para o Kanban; consultas/filter/sort de tarefas e usage; integridade referencial n-n (HX-13); backup online consistente; migrações versionadas em um lugar; o log de eventos (Q4) usa a mesma base | Um binário de banco a manter; precisa rotina de VACUUM/limpeza |
| **JSON versionado integral** (modelo Alethe) | Simples, legível, diff humano | Serialização da árvore inteira por mutação (custo conhecido da auditoria §11); sem transações nem consultas; Kanban n-n e histórico auditável viram loops manuais frágeis |
| **SQLite para tudo** (inclusive preferências/scrollback) | Uma fonte só | Scrollback e blobs grandes pioram o SQLite; preferências pedem edição manual ocasional — JSON legível é melhor ali |

**Evidências:** `github.com/rusqlite/rusqlite` (15/10/2026) — v0.40.1, bundled SQLite 3.53.2,
feature `backup` (API de backup online), `hooks`, `session`, `sqlcipher` (criptografia),
MIT; a própria auditoria do Alethe (§25, decisão futura) recomenda SQLite para o caso do
HATI: "para o HATI com Kanban + métricas + histórico de sessões, um banco local (SQLite)
tende a servir melhor que JSON integral reescrito" — e registra que o padrão de migrações
versionadas do Alethe (v9 com testes) "permanece válido em qualquer formato".

**Impactos:** desempenho — WAL suporta GUI lendo + daemon escrevendo sem bloqueio; backups
consistentes via `VACUUM INTO`/backup API durante operação; segurança — o banco fica em
XDG_DATA_HOME (`~/.local/share/hati/`), longe dos repositórios (DT-07), com modo 0600;
manutenção — migrações versionadas executadas pelo daemon no boot (schema_version), testadas
em CI.

**Riscos e incertezas:** corrupção de arquivo (mitigada por WAL + backups rotativos);
concorrência multi-cliente (daemon é o único escritor — GUI e PWA são clientes; sem
escritores concorrentes); export de backup jamais inclui segredos do keyring (MC-05/AX-20).

**Recomendação (proposta):** **híbrido com espinha SQLite**: `hati.db` (WAL, via rusqlite
bundled) para projetos, grupos, tarefas, jobs, eventos, approvals, dispositivos, usage e
registro de plugins; scrollback como arquivos append-only por terminal (padrão da auditoria
— cap e compactação configuráveis, TM-03); preferências/temas como JSON versionado com
escrita atômica; segredos **fora** do banco — em keyring do OS (Secret Service no Linux),
nunca em arquivos ou eventos (R4). Detalhe em `DATA_MODEL.md` §6–7.

**Critérios de validação:** PoC-05 — 10k tarefas com filtros/ordenação <100ms; migração
v1→v2 testada com dados sintéticos em CI; backup+restore de um perfil completo
reconhecível; kill -9 durante escrita não corrompe (WAL recupera).

**PoC necessária?** **Sim — PoC-05**, com ênfase em migrações e backup consistente.

**Depende de aprovação humana:** **Sim** — ADR-003 (formato e localização), incluindo o
compromisso de que nenhum dado do HATI é escrito nos repositórios dos projetos (DT-07
reforçado, não alterado).

---

## Q4 — Eventos, snapshots, replay e sincronização

**Problema e requisitos:** R9 — modelo de eventos confiável e normalizado por agente para
estado vivo de cartões (HX-15), usage e alertas (AX-31); schema versionado; reconciliação;
replay; backpressure; sincronização desktop/mobile (RC-07, AX-31/HX-24 compartilham eventos
com clientes distintos — CROSS_REVIEW).

**Alternativas:**

| Alternativa | Prós | Contras |
|---|---|---|
| **Log de eventos append-only no SQLite + streaming SSE/WS + snapshots materializados** | Eventos são a fonte da verdade; replay/reconciliação triviais; um stream alimenta GUI e PWA (mesma query por cursor); integridade transacional com as entidades na mesma base | Disciplina de design (event = fato imutável + schema_version) |
| **Eventos efêmeros em memória** (emitir e esquecer) | Mais leve | Perde histórico auditável (HX-12), replay e sincronização de clientes atrasados (mobile reconectado) |
| **Event sourcing completo** (estado 100% derivado) | Elegante | Custo alto demais para o caso: a maior parte do estado é mutável e consultável (Kanban); snapshots materializados já cobrem o necessário |

**Evidências:** padrão comprovado na auditoria do Alethe (§8): batching mpsc 1024 → 16ms/64KB
→ 1 evento por lote + scrollback — sem custo perceptível de UI; OpenCode publica eventos
globais por SSE (`GET /global/event`, docs/server 14/10/2026) e pluginsassinam eventos de
sessão (`session.idle`, `session.error`, `message.part.updated` — docs/plugins); Codex
app-server emite notificações JSON-RPC (`thread/status/changed`, `item/agentMessage/delta`,
`turn/completed` — docs/app-server); Claude Code expõe stream-json e hooks de ciclo
(docs/headless, docs/hooks). Ou seja: os três provedores dão matéria-prima de eventos, mas
cada um com formato próprio — daí a camada de normalização (Q5).

**Impactos:** desempenho — append-only sequencial é barato; consumidores (GUI) recebem
lotes coalescidos, e a PWA futura reconecta por cursor (sem re-enviar histórico); segurança —
eventos **nunca contêm segredos** (HX-23) — sanitização no ponto de emissão; manutenção —
schema_version por tipo de evento + testes de compatibilidade de replay.

**Riscos e incertezas:** volume de eventos de PTY (mitigação: eventos de terminal são
byte-stream para scrollback, não "eventos de domínio"; apenas transições de estado viram
eventos); mudanças de formato dos provedores exigem normalizador versionado (Q5).

**Recomendação (proposta):** **EventEnvelope append-only** no SQLite: `(id monotônico,
stream, type, schema_version, payload JSON, occurred_at, source, correlation_id)`; consumers
GUI/PWA por SSE/WS com cursor por stream; snapshots = tabelas materializadas pelo daemon
(projetos/tarefas/jobs consultáveis sem replay); reconciliação = reprocessar eventos do
cursor após queda; backpressure = coalescing de lotes (padrão 16ms/64KB) + descarte de
deltas intermediários de progresso (mantém o último). Detalhe em `SYSTEM_DESIGN.md` §5.

**Critérios de validação:** PoC-08 — 5k eventos/s sustentados por 60s sem crescimento de
latência perceptível na GUI; cliente reconectado por cursor recebe exatamente o que perdeu;
replay de 100k eventos reconstrói snapshot idêntico ao original (teste de igualdade).

**PoC necessária?** **Sim — PoC-08** (pode ser combinada com a PoC-01 em um spike de
daemon).

**Depende de aprovação humana:** **Sim** — ADR-004 (escolha do modelo de eventos), pois
condiciona Kanban (Fase 2) e mobile (Fase 5).

---

## Q5 — Adaptadores de agentes (OpenCode, Codex, Claude Code)

**Problema e requisitos:** R6/DT-02 — OpenCode + Codex + Claude Code na primeira versão,
com OpenCode de primeira classe (AG-08), integração honesta e sem paridade fictícia (AG-01,
AG-13, OR-02/OR-15); eventos normalizados (AG-07); retomada de sessões (AG-04/05); aprovações
do agente visíveis no HATI (OR-04); versionamento e fallback seguro (questão 5 do
OPEN_QUESTIONS).

**Alternativas (por provedor, integradas por uma camada comum):**

| Caminho | Prós | Contras |
|---|---|---|
| **PTY apenas** (spawn TUI + parse heurístico) | Universal, funciona com qualquer CLI | Sem eventos estruturados, sem aprovações programáticas, estado por inferência (HX-15 pede eventos reais); a auditoria registra que a heurística genérica "não funciona" para OpenCode |
| **Protocolo nativo de cada CLI, com PTY como fallback** | Cada provedor no seu melhor: eventos, permissões, steer/interrupt, custos; honestidade por capacidades declaradas | Três integrações para manter (lição da auditoria: "protocolos mudam rápido e divergem da documentação") |
| **ACP para todos** | Um protocolo único | Apenas OpenCode anuncia ACP hoje; Codex/Claude não — paridade fictícia, exatamente o que R6 proíbe |

**Evidências (documentação oficial, fetch 14–15/10/2026):**

- **OpenCode** (`opencode.ai/docs/server`, `/docs/plugins`, `/docs/acp`, `/v2/docs`):
  `opencode serve` expõe HTTP+OpenAPI 3.1 na porta 4096 (auth básica por env), SSE em
  `/global/event` e `/event`; CRUD completo de sessões com **fork, abort, share, diff,
  summarize, revert e resposta a permissões** (`POST /session/:id/permissions/:permissionID`);
  mensagens síncronas/assíncronas, comandos e até shell por sessão; files/find/symbols;
  SDK gerado da spec; plugins JS/TS com hooks de eventos ricos (`session.idle`,
  `session.error`, `permission.asked`, `tool.execute.before/after`, `message.part.updated`)
  e contexto com SDK client; ACP via `opencode acp` (JSON-RPC stdio) para editores; v2
  (2.0.0) distribui binários nativos (glibc/musl), Desktop e `opencode pair` (web com auth).
- **Codex** (`developers.openai.com/codex/app-server.md`, `/codex-sdk.md`, `/hooks.md`):
  `codex app-server` — JSON-RPC 2.0 por **stdio (JSONL, default) ou unix socket**
  (WebSocket experimental e não suportado em produção); primitivas thread/turn/item;
  `thread/start|resume|fork|list` (filtros por **cwd**, archived, pinned, searchTerm);
  `turn/start|steer|interrupt`; **sandbox policies** (`readOnly`, `workspaceWrite`,
  `externalSandbox` com writableRoots/readOnlyAccess/networkAccess); `approvalPolicy`;
  aprovações server→client (`item/permissions/requestApproval`); `command/exec` PTY-backed;
  model/list; skills/hooks; config read/write; `externalAgentConfig/detect|import`;
  geração de schemas TS/JSON por versão; health probes; SDK TS `@openai/codex-sdk` e Python
  `openai-codex` (controlam o app-server local); hooks `hooks.json`/`config.toml` com
  **trust review por hash** e eventos PreToolUse/PermissionRequest/PostToolUse/Stop etc.;
  compat com `CLAUDE_PLUGIN_ROOT/DATA` nos plugins.
- **Claude Code** (`code.claude.com/docs/en/headless`, `/en/hooks`, `/en/sdk/sdk-headless`):
  Agent SDK (Python/TS) roda o binário com ferramentas/hooks/subagentes/sessões
  (resume/fork)/permissões; modo headless `claude -p` com `--output-format
  json|stream-json`, `--json-schema` (saída estruturada), `--bare`, `--continue/--resume`,
  `--permission-mode (auto|dontAsk|acceptEdits)`, `--permission-prompts none`,
  `--allowedTools` com permission rules; SIGTERM→exit 143, SIGINT interrompe turno;
  `system/init` com **array `capabilities`** (feature detection por versão);
  `total_cost_usd` + custo por modelo (**estimativas client-side**); transcripts `.jsonl`;
  hooks com 5 tipos de handler (**command, http, mcp_tool, prompt, agent**) e eventos
  (SessionStart/End, PreToolUse, PermissionRequest, PostToolUse, Stop, TaskCreated/Completed,
  WorktreeCreate/Remove, FileChanged, TeammateIdle…), allowlist de URLs HTTP
  (`allowedHttpHookUrls`); worktrees nativos (`--worktree`, `isolation: "worktree"`);
  **diretrizes de marca**: produtos de terceiros usam "Claude Agent", não "Claude Code".

**Impactos:** desempenho — OpenCode server por processo `opencode serve` reutilizável por
projeto; Codex/Claude por job/turno; PTY sempre disponível como plano universal; segurança —
permissões e sandboxes das CLIs são enforcement real (diferente de guardas de UI — Q7);
manutenção — cada adaptador declara `capabilities` e a faixa de versões testadas; feature
detection (ex.: array `capabilities` do Claude, schemas gerados do Codex) reduz quebras.

**Riscos e incertezas:** os três protocolos evoluem rápido (a própria OpenCode está
publicando v2 — a doc detalhada de Server/Plugins lida é da linha atual, mas os contratos v2
exigem verificação na PoC); o WebSocket do Codex é explícito "experimental, not supported" —
usar stdio/unix; custos do Claude são estimativas client-side (marcar como tal — US-05).

**Recomendação (proposta):** camada de **AgentAdapter** no daemon com capacidades declaradas
por instância (`spawn_pty`, `headless`, `events`, `permissions`, `steer`, `interrupt`,
`fork`, `resume`, `cost_report`, `sandbox_policy`, `session_list_by_cwd`…):
(1) **OpenCodeAdapter** — via `opencode serve` + SDK + SSE (cidadão de primeira classe: lista
de sessões por projeto, eventos de idle/permissão, diffs, custo local — AG-08), com PTY TUI
para o modo interativo e ACP como alternativa futura;
(2) **CodexAdapter** — via app-server stdio/unix socket (threads por cwd, steer/interrupt,
approvals, sandbox policies, schema gerado por versão);
(3) **ClaudeAdapter** — via Agent SDK/headless stream-json (resume/fork, permissões,
custos estimados, hooks command/http para eventos), PTY TUI para o interativo.
Nenhum adaptador promete o que o provedor não tem (R6); o PTY é o denominador comum e o
fallback de todos. Detalhe por provedor em `AGENT_INTEGRATIONS.md`.

**Critérios de validação:** PoC-02 (OpenCode: listar sessões de um cwd real, criar sessão,
observar `session.idle` e permissão; custo local visível); PoC-03 (Codex: thread com
sandbox `workspaceWrite`, steer e interrupt; aprovação chegando ao HATI); PoC-04 (Claude:
headless com stream-json, resume por session id, custo estimado marcado como estimado).

**PoC necessária?** **Sim — PoCs 02/03/04 são gate da Fase 1** (DT-02 exige os três agentes
na primeira versão; os contratos reais definem a matriz de capacidades honesta).

**Depende de aprovação humana:** **Sim** — ADR-005 (a camada e as escolhas de protocolo por
provedor) + a **verificação dos contratos OpenCode v2** antes da implementação (pendência
registarda em `HANDOFF.md`).

---

## Q6 — Modelo de entidades

**Problema e requisitos:** formalizar projeto, tarefa, job, terminal, sessão de CLI,
worktree, review, approval, evidência e dispositivo (R7, HX-13, CROSS_REVIEW): tarefa ≠ job,
vínculos n-para-n, **não equiparar** fim de processo ≠ sucesso técnico ≠ critérios cumpridos
≠ aprovação humana ≠ commit Git.

**Alternativas:** (a) entidades planas com lógica espalhada na UI (risco de estado duplicado
— o risco nº 1 do CROSS_REVIEW); (b) modelo formal no daemon com estados e transições
explícitas (recomendado); (c) espelhar o modelo interno de cada CLI (impossível — cada
ferramenta tem o seu; o HATI mantém o seu e *referencia* o nativo por ID).

**Evidências:** CROSS_REVIEW "Entidades de produto" e "Não equiparar"; HX-13/14/15/16/17;
auditoria §25 (tarefa fora do projects.json monolítico, relação n-para-n tarefa↔terminal/
sessão); Codex/Claude/OpenCode expõem IDs de sessão próprios (thread ids, session ids,
`.jsonl`) — o HATI os referencia, não os reinterpreta.

**Impactos:** desempenho — n-n com índices é barato no SQLite; segurança — approvals são
entidades auditáveis (HX-23) e **não contêm segredos**; manutenção — máquinas de estado
explícitas evitam "concluído por engano" (HX-10: conclusão de job não aprova tarefa).

**Riscos e incertezas:** tentação de 1:1 tarefa↔job↔sessão (proibida por HX-13 — cada
tentativa é um job novo; uma sessão pode servir a vários jobs por resume; um job pode rodar
sem sessão nativa); diretórios movidos (HX-02) — o projeto tem id estável + path atual +
path anterior detectável.

**Recomendação (proposta):** entidades formalizadas no daemon (tabelas + máquinas de
estado), documentadas integralmente em `DATA_MODEL.md`: Project, Group, Task (+Subtask,
+Dependency), Job (tentativa), TerminalSession (PTY), AgentSession (sessão nativa
referenciada), Worktree, Evidence (com fonte/data/confiabilidade — HX-17), ReviewRequest,
Approval (auditável, HX-19/20/23), PairedDevice (Q9), UsageRecord (Q10), PolicyProfile (Q7),
PluginRegistration (Q8). Vínculos sempre n-n por tabelas de link; o Kanban lê tarefas;
o Orchestration Board lê jobs (R8 — nunca a mesma fonte).

**Critérios de validação:** revisão por pares do modelo (humano + futuramente Sentinel);
testes de invariantes (job sem approval não executa; tarefa não é aprovada por fim de job;
evidência sem fonte é rejeitada; subtask não cria ciclo — HX-08).

**PoC necessária?** Não como PoC isolada — é validada pela PoC-05 (persistência) e pelos
testes de invariantes. **Revisão humana do modelo conceitual é obrigatória antes do schema
físico.**

**Depende de aprovação humana:** **Sim** — o modelo conceitual (`DATA_MODEL.md`) deve ser
revisado e aprovado explicitamente; mudanças posteriores seguem a convenção ADR.

---

## Q7 — Autorizações, sandbox e Git protegido

**Problema e requisitos:** R2/DT-04 — cada novo job exige confirmação humana e a UI nunca é a
única barreira; diferenciar "job autorizado" de tool calls internos (HX-21: combinar regra do
projeto, executor, sandbox e dispositivo); sandbox real (AG-16 — execução isolada sem
reintroduzir WS-16); Git de escrita fora da superfície automatizável (R3/HX-22/DT-06);
aprovações remotas com regras próprias (HX-19, RC-03); auditoria sem segredos (HX-23).

**Alternativas:**

| Alternativa | Prós | Contras |
|---|---|---|
| **PolicyEngine no daemon** (gates por job + sandbox/permission modes das CLIs + auditoria) | Fronteira física fora da UI; auditável; combina níveis (projeto, perfil, dispositivo) | Não é sandbox absoluto — agentes com shell livre podem contornar (declaração obrigatória, AG-03/CROSS_REVIEW) |
| **Sistema de containers/sandbox próprio** (bubblejail/firejail/namespace) | Isolamento real | Superfície e custo grandes; cada CLI reage diferente a namespaces; apropriado apenas para workers isolados (AG-16), não para o núcleo |
| **Confiança total no usuário** (sem gates) | Zero fricção | Viola DT-04/HX-21 — inviável |

**Evidências:** as CLIs já trazem enforcement próprio que o HATI deve orquestrar em vez de
duplicar: Codex app-server tem `sandboxPolicy` (workspaceWrite/readOnly/externalSandbox,
writableRoots, networkAccess) e `approvalPolicy` por thread/turn; Claude tem
`--permission-mode`/`--allowedTools` e hooks `PermissionRequest`; OpenCode tem sistema de
permissions/policies e `permission.asked` em plugins. A auditoria registra o caso inverso
(git_control.rs expõe commit/push como IPC livre — "o HATI deve desenhá-los fora da
superfície automatizável por padrão", §GT) e a tensão AG-03/GT/HX-22: "agentes com shell
irrestrito podem contornar proteção de UI; não prometer bloqueio absoluto".

**Impactos:** segurança — o gate de job é inegociável (DT-04) e vive no daemon; os
permission modes/sandboxes das CLIs são o enforcement real dentro do job; worktrees isolam
escrita de arquivos (Q6/Fase 3); auditoria — cada approval registra autor, ação, projeto,
job, arquivos e instante (HX-23), sem segredos; UX — a fila unificada de revisões (HX-19)
é a superfície única dessas decisões.

**Riscos e incertezas:** sandbox verdadeiro para workers (AG-16) é tema da Fase 3 com PoC
própria (namespaces/bwrap) — não antecipar; Git: o daemon **não expõe** commit/push/merge
como comando automatizável — essas ações existem apenas como fluxo humano na UI com
confirmação e trilha, e a política da Agent Suite (bloqueio nos agentes) é **documentada
como convenção do ecossistema, não garantia do HATI** (os agentes têm shell; o HATI nunca
prometeu bloqueio absoluto — e declara isso na UI, PL-02-style honestidade).

**Recomendação (proposta):** (1) **JobGate no daemon**: spawn de job exige `ApprovalRequest`
pendente resolvida por humano (desktop ou dispositivo pareado permitido — RC-03) — sem
resolução, nada executa; (2) tool calls internos seguem o permission/sandbox da CLI
configurado por perfil de execução (HX-05) + policies HX-21 por tarefa, combinadas em uma
decisão efetiva exibida na prévia antes do spawn (HX-14); (3) **GitService do daemon expõe
apenas operações de leitura** (status/diff/log/worktree-list) para automação; commit/push/
merge existem só como ação humana explícita com trilha de auditoria (HX-22); (4) auditoria
append-only em `Approval`/`EventEnvelope` sem segredos. Detalhe em
`SECURITY_AND_RUNTIME.md` §3–4.

**Critérios de validação:** teste de invariantes (nenhum caminho de código spawna job sem
approval registrado — verificado por teste que inspeciona o fonte, padrão da auditoria §14);
e2e do fluxo completo (tarefa → prévia → aprovação → job → evidência → revisão humana);
pen-test interno da superfície IPC do daemon (Aegis, futuramente).

**PoC necessária?** Não como PoC isolada — é validada pelas PoC-02/03/04 (permissões dos
três provedores) e pelos invariantes. Sandbox de workers (AG-16) terá PoC própria na Fase 3.

**Depende de aprovação humana:** **Sim** — ADR-006 (desenho de autorização e fronteira Git).
A extensão exata do "input remoto permitido" por dispositivo (RC-03) será decidida com a
Q9, mas o gate de job já vale para todas as fases (DT-04).

---

## Q8 — Plugins, extensões e isolamento

**Problema e requisitos:** R10/DT-08 — extensibilidade planejada cedo com core independente;
capacidades explícitas com enforcement no backend (PL-02: "WebView compartilhada não basta");
isolamento real desde o início; storage segregado (PL-05); contribuições views/commands/
themes/panes/providers (PL-06); temas sanitizados (PL-08); catálogo depois (PL-03/DT-08);
import local desabilitado por padrão (PL-04); atualização com integridade (PL-03).

**Alternativas:**

| Alternativa | Prós | Contras |
|---|---|---|
| **Plugins de UI em child webviews isoladas + plugins de backend como processos sidecar** | Isolamento real de código não confiável (PL-02); falha de plugin não derruba o app; comunicação por API restrita e auditável | Complexidade de host; custo de processo por plugin de backend |
| **Plugins na WebView principal** (padrão Alethe) | Simples; acesso direto ao DOM/store | Enforcement vira "guard-rail, não sandbox" — a própria auditoria recomenda decidir isolamento cedo ou declarar o limite |
| **WASM como runtime de plugins** | Sandbox de memória forte; portável | Sem acesso a UI (DOM) — serve para lógica pura, não para views/temas; ecossistema imaturo para UI |

**Evidências:** auditoria §13/§PL-02 ("runs in the same webview — enforcement is
guard-rail, not sandbox"; recomendação: "child-webview/isolamento desde o dia 1, ou aceitar
declaradamente o mesmo limite"); Tauri 2 suporta múltiplas webviews/janelas com esquemas de
protocolo customizados e CSP por webview (docs Tauri); plugins OpenCode/Codex/Claude
mostram o valor de hooks como superfície de extensão de *agentes* (distinto de plugins do
*HATI* — o HATI gerencia os primeiros via MC-08/MC-09 sem duplicar).

**Impactos:** segurança — child webview = plugin de UI só fala com o host por API declarada
no manifesto; backend sidecar = processo com capabilities limitadas e storage próprio
(PL-05); desempenho — custo de processo por plugin é aceitável (plugins são poucos e
opt-in); manutenção — contratos versionados (`hati-plugin-api` vN) permitem evolução sem
quebrar plugins (DT-08: API cedo, catálogo depois).

**Riscos e incertezas:** complexidade pode atrasar o core — mitigação: **contratos definidos
na arquitetura (nomes de capacidades, storage, eventos permitidos), runtime de plugins só na
Fase 4**; temas sanitizados por tokens (PL-08) podem ir antes com custo baixo.

**Recomendação (proposta):** definir **desde a Fase 1** o manifesto de plugin
(`hati-plugin.json`: id, versão, capabilities, contribuições views/commands/themes/panes,
faixa de versão da API); implementar o host na Fase 4 com **child webviews** para UI e
**processos sidecar** para backend, storage segregado por plugin com quota (PL-05) e
capacidades rejeitadas quando excedidas (PL-02); catálogo (PL-03) depois, com SHA-256 e
review de permissões; import local desabilitado por padrão (PL-04). Detalhe em
`SECURITY_AND_RUNTIME.md` §5.

**Critérios de validação:** core 100% funcional sem nenhum plugin (critério de saída da
Fase 4 do roadmap); plugin malicioso de exemplo (e2e) não consegue ler store alheia, nem
spawn de PTY, nem FS fora da quota; manifesto com capability não declarada é rejeitado no
load.

**PoC necessária?** **Sim, na Fase 4** (PoC de host isolado com um plugin de exemplo) — não
agora; os **contratos**, porém, entram na revisão desta proposta (DT-08 manda projetar cedo).

**Depende de aprovação humana:** **Sim** — ADR-007 (modelo de plugins e isolamento) e o
formato do manifesto.

---

## Q9 — Mobile, pareamento e transporte seguro

**Problema e requisitos:** R11/DT-09/DT-10 — acesso pelo iPhone logo após o núcleo desktop
confiável; pareamento não substitui autenticação granular; RC-04 (transporte sem TLS)
**descartado** — nada desprotegido; RC-01/02/03/05/06/07 + HX-25: Kanban, acompanhamento e
aprovações com opt-ins escalonados; dispositivos pareados com revogação/expiração/auditoria;
Git protegido permanece no desktop (DT-06); sem cloud relay obrigatória (DT-10: host ligado
e serviço ativo).

**Alternativas:**

| Alternativa | Prós | Contras |
|---|---|---|
| **PWA servida pelo daemon sobre TLS/WSS** (rede local) + **Tailscale `serve`** para fora de casa | Mesma base de código da GUI-web; TLS automático no tailnet (certs ts.net); zero app-store; o daemon é o único endpoint | Em LAN sem Tailscale, exige cert próprio + pinning (UX de confiança a desenhar) |
| **App nativo iOS** | Integração máxima (push, biometria) | Custo alto; atrasa DT-10; a PWA cobre o aprovado (RC/HX não pedem push nativo) |
| **Proxy público (ngrok/cloudflare)** | Sem setup de rede | Expõe o serviço a terceiros; contraria local-first; RC-05 aprovou Tailscale, não relay público |

**Evidências:** Tailscale — `tailscale.com/kb/1247` (funnel/serve HTTPS; certs automáticos
`*.ts.net`; serve = só tailnet, funnel = público por relay — o HATI usa **serve**);
`opencode pair` (docs v2) mostra o padrão de par local web com auth básica em loopback —
mesma família de solução que o HATI generaliza para dispositivos pareados; auditoria §RC:
o modelo de limites/consentimentos do Alethe (read-mostly, opt-ins escalonados, máx 4
dispositivos, lockout, expiry, revoke) é explicitamente reutilizável como conceito com o
transporte trocado por TLS — "herdar o modelo de pareamento/limites e trocar o transporte".

**Impactos:** segurança — TLS obrigatório (WSS/HTTPS) em ambos os modos; pareamento por QR
com token curto (janela de ~2min, estilo RC-01/RC-06) trocado por token de sessão ligado ao
dispositivo; opt-ins por ação (kanban-write, respond-agent, approve, shell) default
read-only (RC-03); revogação imediata + expiração + auditoria (RC-06); Git protegido
inexistente na superfície mobile (DT-06); desempenho — a PWA consome a mesma API de eventos
da GUI (Q4) com cursor, sem duplicação de estado; manutenção — uma UI web a manter (a PWA
pode reaproveitar componentes React da GUI desktop).

**Riscos e incertezas:** notificações push exigiriam serviço externo (fora do aprovado —
alertas ficam in-app/SSE enquanto o app está aberto; local notifications da PWA quando
instalada); consistência offline do Kanban mobile (fase 5: política simples — sem
escritas offline; escrita exige conexão viva com o daemon, evitando CRDT prematuro).

**Recomendação (proposta):** **PWA** servida pelo próprio `hatid` (mesmo binário, endpoint
`/app` sob TLS) — na LAN com certificado próprio do HATI (CA local opcional, pinning
explícito no pareamento) e fora de casa via **Tailscale serve** no tailnet do usuário
(RC-05 "assistido": o HATI detecta e orienta, nunca obriga). Pareamento: QR com token
efêmero → dispositivo registrado com escopo de permissões (RC-03) e expiração; sessões
revogáveis a qualquer momento (RC-06). Detalhe em `SECURITY_AND_RUNTIME.md` §6.

**Critérios de validação:** PoC-07 — iPhone pareado via tailnet vê Kanban e aprova um job;
revogação derruba a sessão em <5s; sem TLS o endpoint recusa conexão (RC-04 testado como
impossível); nenhuma rota Git de escrita existe no contrato mobile (verificado por teste).

**PoC necessária?** **Sim — PoC-07 na Fase 5** (o transporte define UX de pareamento);
contratos de API/ACL já validados antes (Q4/Q7).

**Depende de aprovação humana:** **Sim** — ADR-008 (PWA + transporte Tailscale/LAN-TLS +
modelo de pareamento). A escolha "PWA e não app nativo" é reversível (a API do daemon é a
mesma), mas o registro é necessário.

---

## Q10 — Usage, limites e custos

**Problema e requisitos:** R13/US-* — local-first; scraping remoto apenas consentido, com
frescor/erros visíveis (US-06); consentimento explícito por provedor, default off e
revogável (US-09); custo/tokens por sessão/job (US-05, OR-06); histórico por projeto/agente/
sessão/job (US-04); não confundir custo local com cota remota (US-04/US-01); estimativas
sempre marcadas (HX-17: texto de agente não é prova); cotas heterogêneas não são números
equivalentes (OR-09).

**Alternativas:**

| Alternativa | Prós | Contras |
|---|---|---|
| **Fontes locais primeiro** (server/DB do OpenCode, custos reportados pelo headless, app-server do Codex) + scraping consentido depois | Auditável, sem fragilidade de APIs internas; alinhado ao perfil OpenCode-nativo (diferencial §30 da auditoria) | Cobertura desigual entre provedores (a matriz de capacidades mostra exatamente o que existe) |
| **Scraping remoto primeiro** (estilo Alethe: OAuth/APIs internas dos provedores) | Dados de cota de assinatura que fontes locais não têm | Frágil (issues #272 da auditoria; headers beta; APIs v1internal); exige consentimento e manutenção constante; US-03 (Antigravity) já foi adiado por isso |
| **Nenhum coletor de uso** | Simples | Viola US-04/US-05/US-10 aprovados |

**Evidências:** OpenCode — o próprio server expõe dados de sessão e há DB local
(`opencode.db`) que o Alethe já lia READ_ONLY (auditoria US-04: "dados locais e auditáveis
— a recomendação técnica é inverter a prioridade: fontes locais primeiro"); Claude Code —
headless reporta `total_cost_usd` e custo por modelo no `--output-format json`
(docs/headless 14/10/2026), com a ressalva oficial: "**client-side estimates** and can
differ from your actual bill" — o HATI marca como estimativa (US-05); Codex — app-server/
SDK expõem thread/turn com dados disponíveis; cota de assinatura ChatGPT requer backend
remoto (US-02: "dados realmente disponíveis, distinguindo assinatura, API e créditos").

**Impactos:** desempenho — coleta por eventos já normalizados (Q4), sem polling agressivo;
cache compartilhado por provedor com cadência única e "última atualização + erros"
visíveis (US-06); segurança — consentimento default off significa **nada lê um provedor
desligado** (US-09, padrão a preservar da auditoria); nenhuma credencial sai do keyring;
manutenção — cada fonte é um `UsageProvider` com capacidades declaradas (o mesmo padrão
dos adaptadores).

**Riscos e incertezas:** os números de cota remota mudam de formato sem aviso (risco
médio do CROSS_REVIEW); preços por token embutidos envelhecem — o HATI versiona tabela de
preços como dado com fonte e data (HX-17); US-03 (Antigravity) permanece ADIAR — não
antecipar.

**Recomendação (proposta):** **UsageService no daemon** com providers plugáveis:
(1) OpenCode local (primeiro, mais rico — por sessão/projeto/agent);
(2) Claude estimado (custos das execuções headless capturados dos result payloads, marcados
"estimado, client-side");
(3) Codex via app-server quando disponível;
(4) scraping remoto (US-01/US-02) **somente consentido, default off, fase posterior**, com
frescor e indisponibilidade visíveis (US-06) e comparabilidade explícita (OR-09: janelas
incomparáveis nunca somadas).
Orçamentos por tarefa/projeto permanecem ADIAR (HX-26) — os custos reais são registrados
desde o início, os limites configuráveis ficam para quando as fontes forem confiáveis.
Detalhe em `SYSTEM_DESIGN.md` §5.6.

**Critérios de validação:** custo/tokens de uma sessão OpenCode visíveis por tarefa com
fonte declarada; consentimento off por padrão = zero acesso a credenciais (teste);
estimativa Claude sempre rotulada; indisponibilidade de fonte exibida como estado (nunca
como zero).

**PoC necessária?** **Parcial** — coberta pelas PoC-02/03/04 (custos vêm dos mesmos
contratos dos adaptadores); nenhuma PoC de scraping nesta fase.

**Depende de aprovação humana:** **Sim** — ADR-009 (estratégia local-first e regras de
consentimento/estimativa).

---

## Q11 — Graphify (grafo de código)

**Problema e requisitos:** AX-08 (MELHORAR) — grafo de código incremental, útil aos agentes
(como MCP), com limites de RAM e isolado por projeto; custo de Graphify é risco **médio**
no CROSS_REVIEW ("custo de Graphify"); a auditoria original o marcava questionável (indexação
de repositórios grandes, RAM, privacidade).

**Alternativas:**

| Alternativa | Prós | Contras |
|---|---|---|
| **Adiar para fase tardia com estudo próprio** (recomendado) | Não compromete o núcleo; MCP (Tasks/Skills) evolui e pode cobrir parte da demanda | Sem o diferencial no primeiro ano |
| **Implementar grafo incremental na Fase 4** | Diferencial de agente | Alto custo (indexação incremental correta é projeto por si); RAM imprevisível em monorepos; AX-08 não condiciona nenhuma entrega anterior |
| **Integração com indexadores existentes** (ctags/tree-sitter/lsif por projeto) | Menos código próprio | Ainda exige camada de serviço + MCP + limites; a decisão de custo fica a mesma |

**Evidências:** AX-08 é o único ID envolvido e é MELHORAR sem dependência para trás;
CROSS_REVIEW lista "custo de Graphify" como risco médio; MCP atual (spec 2026-07-28,
modelcontextprotocol.io) define extensões **Tasks** (operações longas com polling e
handles duráveis) e **Skills over MCP** — superfícies pelas quais um grafo futuro seria
consumido pelos agentes; a auditoria registra o Graphify do Alethe como completo porém
"Q" (questionável) com custo alto.

**Impactos:** desempenho — qualquer grafo exige orçamento de RAM/CPU por projeto e
indexação incremental sob demanda (nunca global); privacidade — o grafo lê código do
usuário: permanece local, sem upload (R4); manutenção — um serviço extra no daemon com
ciclo de vida próprio (ligável/desligável por projeto, AX-08 "isolado por projeto").

**Riscos e incertezas:** benefício real ainda não mensurado para o fluxo do usuário
(evidência a coletar antes de investir — ver VALIDATION_PLAN).

**Recomendação (proposta):** **adiar a implementação**; registrar na arquitetura apenas (a)
o slot do serviço (`CodeGraphService`, desligado por padrão, por projeto, com orçamento de
memória), (b) a decisão de que a superfície de consumo será MCP (reuso da infra de MC-01…09)
e (c) um estudo de viabilidade dedicado na fase em que voltar à pauta — comparando
tree-sitter/lsif/ctags e medindo RAM em repositórios reais do usuário (Chronos, futuramente).

**Critérios de validação:** quando ativado: orçamento de RAM respeitado (kill gracioso do
indexador, nunca do app); nenhum upload; benefício medido (consulta respondida por grafo vs.
grep em tempo e qualidade).

**PoC necessária?** **Sim, quando a feature voltar à pauta** (fase futura) — não nesta
etapa.

**Depende de aprovação humana:** **Sim, sobre o timing** — a proposta é não incluir Graphify
no escopo das fases 1–4 (além do slot arquitetural); AX-08 permanece MELHORAR na matriz
(nenhuma reclassificação é feita aqui).

---

## Q12 — Qualidade, testes e CI

**Problema e requisitos:** R5 — Linux como plataforma de primeira classe com CI e e2e
reais; questão 12 do OPEN_QUESTIONS: e2e Linux real, regressão de PTYs, stress, segurança,
CI, ADRs e riscos; lições da auditoria: Linux foi a plataforma *menos* validada do Alethe
(issues #205/#192/#111), testes de UI baixos, doc drift (PRIVACY vs código) e "formalizar
ADRs desde o início" (§29.8).

**Alternativas:**

| Alternativa | Prós | Contras |
|---|---|---|
| **CI Linux-first com e2e do app real (WebdriverIO/Playwright + Tauri) + contêiner Arch** | Testa o que o usuário usa (Omarchy); pega quirks WebKitGTK/Wayland cedo; regressão de PTYs automatizada | Infra de e2e de desktop é mais cara que unit-only |
| **Unit tests apenas + testes manuais** | Barato | Repetiria o padrão Alethe: Linux subvalidado exatamente onde é a plataforma primária |
| **e2e multi-OS desde o dia 1** | Cobertura ampla | Custa mais que o necessário agora; Windows é Fase 6 |

**Evidências:** WebdriverIO com `@wdio/tauri-service` roda o app Tauri real em CI (usado
pelo Alethe — auditoria §14; documentado em v2.tauri.app/develop/tests); testes de
invariantes que inspecionam o próprio fonte (padrão `include_str!` da auditoria §14)
travam decisões de lock/teardown contra regressões — técnica transferível (não o código);
CI do Tauri distribui runners Linux nativos; doc drift é risco registrado (§29.6) — docs
geradas de configs ou testadas.

**Impactos:** desempenho — suíte de regressão de PTYs (spawn/kill/resize/reanexação sob
carga) protege o núcleo mais frágil; segurança — pipeline roda clippy/tsc/lint como gates;
manutenção — ADRs obrigatórios por decisão (convenção já existe em `docs/decisions/`);
Chronos entra depois para benchmarks (ver §8 do Prompt 03).

**Riscos e incertezas:** e2e de GUI Wayland em runner headless exige wlroots/WebKitGTK
configurados — factível (tauri-driver + WebKitGTK headless), com custo de setup inicial
(no VALIDATION_PLAN).

**Recomendação (proposta):** **pirâmide:** unit (Rust + TS) → integração do daemon (contratos
IPC/HTTP sobre socket real) → e2e do app empacotado (WebdriverIO + tauri-driver) no CI
Linux; **suíte de regressão de PTYs** como categoria primeira (cenários da auditoria: UTF-8
split, flush, resize, kill de árvore, órfãos); testes de invariantes para regras de
segurança (sem job sem approval; sem segredo em evento); clippy + tsc + ESLint como gates
de merge; docs de segurança/privacidade **derivadas de teste** (um teste valida que o que o
doc afirma sobre consentimento é o que o código faz — anti-doc-drift). Detalhe em
`VALIDATION_PLAN.md` §4.

**Critérios de validação:** pipeline verde: unit + integração + e2e mínimo (smoke de
onboarding, abrir projeto, spawn de PTY, fechar janela mantendo job) em runner Linux
Arch-like; regressão de PTYs executando em <5min; cobertura de módulos críticos do daemon
(pty, store, policy) ≥80%.

**PoC necessária?** A própria PoC-01 valida a infra de teste (um e2e de verdade rodando em
CI é entregável da PoC-01).

**Depende de aprovação humana:** **Sim** — ADR de estratégia de testes/CI (parte do
VALIDATION_PLAN, ADR-010 não cobre; entrará como ADR próprio na aprovação da fase).

---

## Q13 — Roadmap técnico (ajustes recomendados)

**Problema e requisitos:** DT-01 (fases incrementais), DT-03 (orquestração básica primeiro),
DT-02 (três agentes na primeira versão), DT-08 (API de plugins cedo, catálogo depois),
DT-10 (iPhone após o núcleo confiável); `docs/ROADMAP.md` é **proposta não aprovada** — o
estudo pode recomendar ajustes de ordem/ênfase **sem reclassificar nenhum ID da matriz**.

**Alternativas:** manter o roadmap como está; ajustar sequência técnica; reordenar por
entrega de valor. O que não é alternativa: reclassificar IDs ou prometer escopo adiado
como inicial.

**Evidências:** as dependências do CROSS_REVIEW (§"Dependências relevantes") apontam:
serviço de PTY fora da janela → retomada/execução em background/mobile; adaptadores e
eventos confiáveis → estado vivo dos cartões/usage/alertas; tarefa/job distintos →
revisão/auditoria/continuidade. Ou seja: **daemon, eventos e adaptadores são a espinha de
tudo** — quanto antes validados, menos retrabalho nas fases seguintes.

**Impactos:** a ordem proposta abaixo não muda escopo — muda **risco**: valida as três
decisões estruturais (Q1/Q2/Q5) com PoCs baratas antes de construir UI extensiva.

**Riscos e incertezas:** adições de PoC podem parecer "atraso" — mas são o oposto: evitam
construir a UI inteira sobre uma shell cujo terminal não renderiza bem (PoC-06) ou um
modelo de serviço que não sobrevive ao logout (PoC-01).

**Recomendação ( proposta) — ajustes ao roadmap, sem tocar na matriz:**

1. **Fase 0 ganha um gate de saída explícito:** aprovação humana dos ADRs propostos + das
   PoCs executáveis (PoC-01/02/03/04/06 priorizadas; 05/08 no início da Fase 1). As PoCs
   **não** foram executadas nesta etapa (proibidas no Prompt 03); são entregáveis da
   transição Fase 0→1.
2. **Fase 1 continua "núcleo de execução"**, com o **daemon antes da GUI** (hatid + contrato
   IPC + PTYs + eventos) — a GUI consome o contrato já estável.
3. **Eventos normalizados entram na Fase 1** (não depois): são o alicerce do Kanban
   (Fase 2), do board de jobs (Fase 3) e do mobile (Fase 5) — Q4.
4. **Profundidade do adaptador OpenCode distribui-se Fase 1 (PTY+server) e Fase 2
   (sessões por projeto + eventos de permissão)**, alinhada a AG-08 sem virar gargalo.
5. **Fases 2–6 seguem como propostas** (Kanban/usage; jobs/worktrees/revisão; plugins/MCP;
   mobile; Windows) — os critérios de saída existentes permanecem.
6. Nenhum ID ADIAR/DESCARTAR entra; nenhum MANTER/MELHORAR sai. Os ajustes são de ordem
   técnica interna e estão registrados aqui — **o ROADMAP.md original permanece intocado**
   até decisão humana.

**Critérios de validação:** aprovação humana explícita dos ajustes; ROADMAP.md atualizado
pelo usuário (ou pela Athena, em etapa autorizada) refletindo somente o aprovado.

**PoC necessária?** Não — é uma recomendação de sequência.

**Depende de aprovação humana:** **Sim** — integralmente (o roadmap é proposta humana;
a Athena apenas recomenda).

---

## Q14 — Empacotamento, atualização e portabilidade

**Problema e requisitos:** R5 (Linux/Arch/Omarchy de primeira classe), AX-23 (update
assinado, opt-in, nunca silencioso), AX-20 (backup/export validado, anti-zip-slip), DT-07
(dados fora dos repositórios), migração entre PCs, compatibilidade com as CLIs
(AG-02: detecção sem alteração do sistema).

**Alternativas (distribuição Linux):**

| Formato | Papel | Observações |
|---|---|---|
| **AppImage** | Portátil universal | Simples de testar em qualquer distro; primeiro artefato de CI |
| **AUR (pacote + binário)** | Primeira classe (Arch/Omarchy) | Instalação nativa pacman; paru/yay; exige mantenedor ou PKGBUILD oficial |
| **Flatpak/Snap** | Posteriores | Sandboxing pode conflitar com execução de CLIs do host (permissões/paths) — só avaliar depois, com custo/benefício |
| **.deb/.rpm** | Posteriores | Fase Windows e outras distros depois do núcleo |

**Evidências:** Tauri distribute (`v2.tauri.app/distribute`, 15/10/2026) documenta
AppImage, AUR, Debian, RPM, Snap e assinatura Linux (gpg) + updater plugin com assinatura
mínisign/ed25519; auditoria §13 registra o problema inverso (update check sem opt-out,
builds não assinados como estado de produção — o HATI inverte: **opt-in explícito** e
assinatura desde a primeira release); DT-07 define onde os dados vivem; a doc do OpenCode v2
mostra o padrão de binários nativos por plataforma (glibc/musl) — o HATI assume CLIs
instaladas pelo usuário e apenas as **detecta** (AG-02), nunca instala sem consentimento.

**Impactos:** desempenho — AppImage/AUR nativo dispensa sandbox pesado; segurança — updater
assinado (ed25519) com chave pública embutida e **check opt-in com preferência visível**
(AX-23); dados do HATI em XDG (`~/.local/share/hati/`, `~/.config/hati/`, keyring do OS) —
nunca dentro de repositórios de projetos (DT-07); backups = export zip validado
(anti-zip-slip no import, seletivo por perfil/projeto, nunca inclui keyring); migração de
PC = export→import + reinstalação das CLIs (o HATI não move credenciais de máquina).

**Riscos e incertezas:** assinatura exige processo de release (chaves) definido pelo
usuário; AUR exige conta/mantenedor — a publicação é decisão humana; CLI instalada via
npm/uv pode variar de versão — a matriz de compatibilidade de adaptadores (Q5) declara
faixas testadas por release do HATI.

**Recomendação (proposta):** CI produz **AppImage + PKGBUILD (AUR) assinados** por release;
updater do Tauri **desativado por padrão** com opt-in explícito por release (AX-23); dados
em XDG conforme DT-07; backup seletivo validado (AX-20); janela "What's New" no primeiro
uso pós-update (AX-24). Windows (Fase 6): mesmo contrato daemon↔GUI com Job Objects
(TM-10/TM-13/TM-19/TM-26 adiados lá, como já proposto).

**Critérios de validação:** AppImage roda em Omarchy limpo (sem dependências extras);
update assinado rejeita binário adulterado (teste com assinatura inválida); backup com
credencial presente nunca exporta o segredo (teste); import rejeita zip com path traversal.

**PoC necessária?** **Parcial** — empacotar o app da PoC-01 como AppImage já valida o
pipeline; assinatura/updater completa é entregável de release engineering na Fase 1.

**Depende de aprovação humana:** **Sim** — escolha de canais de distribuição (AUR público?
AppImage apenas?), política de assinatura e posse das chaves.

---

## Apêndice A — Registro de evidências (fetch direto, 14–15/10/2026)

| Fonte | O que sustenta |
|---|---|
| `opencode.ai/docs/server` (14/10/2026) | Q5/Q10 — API HTTP+OpenAPI 3.1, SSE, sessões (fork/abort/share/diff/summarize/revert/permissions), mensagens, TUI control, auth por env |
| `opencode.ai/docs/plugins` (14/10/2026) | Q5 — hooks de eventos (session.*/permission.*/tool.*/message.*), contexto com SDK, npm/local |
| `opencode.ai/docs/acp` (14/10/2026) | Q5 — ACP `opencode acp` JSON-RPC stdio (Zed/JetBrains) |
| `opencode.ai/v2/docs` (14/10/2026) | Q5/Q14 — OpenCode v2 (2.0.0): binários nativos glibc/musl, Desktop, `opencode pair` |
| `developers.openai.com/codex/app-server.md` (14/10/2026) | Q5/Q7 — JSON-RPC 2.0 stdio/unix (WS experimental), thread/turn/item, resume/fork/list por cwd, steer/interrupt, approvals server→client, sandbox policies, command/exec PTY, schemas gerados, health probes, ws-auth |
| `developers.openai.com/codex/codex-sdk.md` (14/10/2026) | Q5 — SDK TS `@openai/codex-sdk`, Python `openai-codex`, Sandbox presets; `codex mcp-server` removido |
| `developers.openai.com/codex/hooks.md` (14/10/2026) | Q5/Q7 — hooks.json/config.toml, trust por hash, PreToolUse/PermissionRequest/PostToolUse/Stop, mcp_tool hooks, async, spilling |
| `code.claude.com/docs/en/headless` (14/10/2026) | Q5/Q7/Q10 — `claude -p`, stream-json, `--json-schema`, `--bare`, resume/fork, permission modes/prompts, capabilities (feature detection), `total_cost_usd` (estimado), SIGTERM 143 |
| `code.claude.com/docs/en/hooks` (14/10/2026) | Q5/Q7 — 5 tipos de handler (command/http/mcp_tool/prompt/agent), eventos de ciclo completos, allowlist HTTP, worktree hooks |
| `code.claude.com/docs/en/sdk/sdk-headless` (14/10/2026) | Q5/Q10 — Agent SDK Python/TS; branding: "Claude Agent" (não "Claude Code" em produto de terceiros); autenticação por API key |
| `v2.tauri.app/start` + `/distribute` (jul/2026) | Q1/Q14 — Tauri 2 (~600KB, WRY/TAO, audits), AppImage/AUR/deb/rpm/snap/flatpak, signing, updater |
| `electronjs.org/docs/latest` (15/10/2026) | Q1 — Electron 44.7.2 (Chromium+Node embutidos) |
| `man.archlinux.org/man/loginctl.1` (systemd 262) | Q2 — `enable-linger`, KillUserProcesses, user manager no boot |
| `github.com/GitoxideLabs/gitoxide` (15/10/2026) | Q-comparativo Git — 12k stars; gix "usable" (initial development); production-grade só gix-lock/tempfile; clone/fetch/push/status/merge; STABILITY.md |
| `libgit2.org` (15/10/2026) | Q-Git — C portátil, cross-platform, GPLv2+exception, git2-rs, usado por GitHub/Microsoft |
| `github.com/rusqlite/rusqlite` (15/10/2026) | Q3 — v0.40.1, SQLite bundled 3.53.2, backup/hooks/session/SQLCipher, MIT |
| `modelcontextprotocol.io/specification/latest` (2026-07-28) | Q8/Q11 — JSON-RPC 2.0, elicitation, extensões Tasks/Skills/Apps, princípios de consentimento |
| `tailscale.com/kb/1247` (exemplos serve/funnel) | Q9 — HTTPS com certs `*.ts.net`; serve (tailnet) vs funnel (público por relay) |
| `xtermjs.org` (15/10/2026) | Q1 — `@xterm/xterm`, terminal padrão de mercado (VS Code etc.) |
| `docs/hati-context/source/ALETHE_AUDIT_ORIGINAL.txt` | Q1/Q2/Q3/Q4/Q6/Q7/Q8/Q9/Q10/Q12 — padrões comprovados (batching 16ms/64KB, scrollback .bin, gates de memória, ceifa de órfãos, MVC de consentimentos remote, escrita segura de configs, testes de invariantes) e erros a não repetir (segredos plaintext, remote sem TLS, serialização integral, doc drift, #205) |

## Apêndice B — Limitações da pesquisa

1. **Busca web indisponível nesta sessão** (erro de cancelamento da ferramenta): as
   evidências vieram de fetch direto às páginas oficiais conhecidas; artigos/comparativos
   independentes (benchmarks Tauri×Electron, estudos de RAM) **não** foram consultados —
   os números citados são os declarados pela documentação oficial.
2. **OpenCode v2**: a v2 (2.0.0) foi anunciada durante a pesquisa; as páginas detalhadas de
   Server/Plugins lidas são da linha atual de docs e coerentes com a arquitetura v2
   ("TUI é cliente do server"), mas os contratos exatos da v2 devem ser re-verificados na
   PoC-02 antes da implementação do adaptador.
3. **Nenhuma PoC foi executada** (proibição do Prompt 03): todos os critérios listados são
   propostas de validação futura.
4. Versões específicas de crates além de rusqlite (tokio, axum, etc.) não foram conferidas
   — são dependências de implementação a fixar nos ADRs na hora da execução.

