# Integração de agentes — adaptadores e capacidades — PROPOSTA

> **Status: PROPOSTA (Prompt 03).** Matriz de capacidades sustentada por documentação
> oficial (fetch 14–15/10/2026 — Apêndice A do TECHNICAL_STUDY). **Sem paridade fictícia**
> (R6/AG-01): o que o provedor não tem, o HATI declara ausente. ADR-005 é o registro
> correspondente. Nenhuma PoC foi executada nesta etapa.

- **Data:** 15/10/2026 · **Autora:** Athena · **Agentes da v1 (DT-02):** OpenCode, Codex,
  Claude Code — OpenCode de primeira classe (AG-08).

## 1. Arquitetura da camada de adaptadores

```mermaid
flowchart TB
    subgraph hatid
        REG[AgentRegistry\ndetecta CLIs (AG-02) + versões + capabilities]
        NORM[Normalizador de eventos\n→ EventEnvelope (Q4)]
        GATE[JobGate / fila de revisão (HX-19)]
    end
    OC[OpenCodeAdapter\nserve HTTP + SSE + SDK\n+ TUI via /tui + PTY fallback]
    CX[CodexAdapter\napp-server JSON-RPC (stdio|unix)\n+ PTY fallback]
    CL[ClaudeAdapter\nAgent SDK / headless stream-json\n+ hooks + PTY fallback]
    CUSTOM[CustomAdapter\nPTY puro (AG-01)]
    REG --> OC & CX & CL & CUSTOM
    OC & CX & CL & CUSTOM --> NORM
    NORM --> GATE
    OC -.->|http://127.0.0.1:PORT\nauth env| SRV[opencode serve]
    CX -.->|stdio JSONL| AS[codex app-server]
    CL -.->|spawn binário + stream-json| CC[claude -p / SDK]
```

- **Capability flags por instância** (detectadas na descoberta, guardadas com a faixa de
  versão testada): `pty`, `resume`, `fork`, `headless`, `events`, `permissions`,
  `approval_gate`, `steer`, `interrupt`, `cost_verified`, `cost_estimated`,
  `sessions_by_cwd`, `sandbox_policy`, `diff`, `worktree_native`, `tui_control`.
- **Versionamento:** cada adaptador declara faixas de versão da CLI com as quais foi
  testado; fora da faixa → modo degradado com aviso (nunca comportamento inventado).
- **Feature detection em runtime:** Claude `system/init.capabilities[]`; Codex
  `app-server generate-ts/json-schema` por versão; OpenCode OpenAPI 3.1 (`/doc`) —
  contratos consultados, não presumidos.

## 2. Matriz de capacidades (evidência: docs oficiais, out/2026)

| Capacidade | OpenCode | Codex | Claude Code |
|---|---|---|---|
| PTY interativo (TUI) | ✅ | ✅ | ✅ |
| Sessões headless dirigíveis | ✅ serve (`/session/:id/message`, `prompt_async`) | ✅ app-server (`turn/start`) | ✅ `claude -p` (stream-json) |
| Retomar sessão | ✅ `session` persistida; reattach por id | ✅ `thread/resume` (por cwd!) | ✅ `--resume <id>` / `.jsonl` |
| Fork de conversa | ✅ `POST /session/:id/fork` | ✅ `thread/fork` (lastTurnId) | ✅ SDK sessions fork |
| Listar sessões por projeto (cwd) | ✅ `GET /project`, `GET /session` | ✅ `thread/list` filtro `cwd` | ⚠️ indireto: scan de `~/.claude/projects` (padrão AG-05) |
| Eventos em tempo real | ✅ SSE `/event` + plugins (`session.idle`…) | ✅ notificações JSON-RPC (`item/delta`, `turn/completed`) | ✅ stream-json + hooks (`Stop`, `TaskCompleted`…) |
| Pedidos de permissão do agente | ✅ `permission.asked` (plugin) + `/session/:id/permissions/:id` | ✅ `item/permissions/requestApproval` (server→client) | ✅ hooks `PermissionRequest` + `--permission-prompt-tool` |
| Steer (redirecionar no meio) | ✅ nova mensagem em sessão | ✅ `turn/steer` | ⚠️ parcial: interromper (SIGINT/SDK `interrupt`) + novo prompt |
| Cancelar/interrupt | ✅ `POST /session/:id/abort` | ✅ `turn/interrupt` | ✅ SIGINT / SDK interrupt (SIGTERM descarta turno — exit 143) |
| Sandbox/permissões configuráveis | ✅ permissions/policies por config | ✅ `sandboxPolicy` (readOnly/workspaceWrite/externalSandbox + roots + rede) + `approvalPolicy` | ✅ `--permission-mode` (auto/dontAsk/acceptEdits) + `--allowedTools` (rules) |
| Diff do que mudou | ✅ `GET /session/:id/diff` | ✅ itens file-change | ⚠️ via evidência externa (HATI computa `git diff` — GT-02) |
| Custo/tokens | ✅ local (DB próprio / server) | ⚠️ conforme dados do app-server (verificar) | ✅ `total_cost_usd` **estimado client-side** (marcar!) |
| Comandos/shell assistido na sessão | ✅ `POST /session/:id/shell` | ✅ `command/exec` (PTY-backed) | ⚠️ fora do headless; PTY serve |
| Worktrees nativos | — (não anuncia) | ✅ ambientes Git worktrees (docs) | ✅ `--worktree` + hooks WorktreeCreate/Remove |
| Health/estado | ✅ `/global/health` | ✅ `/readyz` `/healthz` | ✅ `system/init` |
| Transporte | HTTP loopback (auth por env `OPENCODE_SERVER_PASSWORD`) | stdio JSONL / unix socket (**WS experimental — não usar**) | subprocess (stdin/stdout) |
| Protocolo alternativo | ACP (`opencode acp`) — JSON-RPC stdio | SDK TS/Python (empacotam app-server) | Agent SDK Python/TS |

Legenda: ✅ suportado (documentado) · ⚠️ parcial/indireto (o HATI supre com mecanismo próprio
ou declara limitação) · — não disponível.

**Regra de honestidade (AG-01/DT-02):** a UI mostra por agente apenas o que a coluna acima
sustenta; capacidades ausentes ficam visíveis como "não suportado por este agente".

## 3. OpenCodeAdapter — primeira classe (AG-08)

- **Canal principal:** `opencode serve` dedicado por projeto (porta efêmera loopback,
  `OPENCODE_SERVER_PASSWORD` gerenciado no keyring) + SSE de eventos.
- **O que habilita:** Projects Hub vê sessões do projeto; Kanban retoma conversas por
  tarefa; `permission.asked` vira `ReviewRequest` na fila (HX-19); `session.idle` atualiza
  cartão (HX-15); diff por sessão vira `Evidence` (HX-17); custo local alimenta
  UsageService (US-04).
- **Interativo:** PTY com a TUI; endpoints `/tui/*` permitem pré-configurar prompt e
  comandos na aba (a doc oficial os destina a integrações de IDE — o HATI segue o mesmo
  uso); plugins HATI **opcionais** (opt-in, nunca auto-write) para eventos extras.
- **Pendência v2:** re-verificar contratos da OpenCode v2 antes da implementação (PoC-02).

## 4. CodexAdapter

- **Canal:** `codex app-server` (stdio JSONL) por sessão de trabalho; unix socket quando
  múltiplos consumidores. **Não usar WebSocket** (docs: experimental, não suportado em
  produção).
- **O que habilita:** threads listadas por cwd (Recent Chats — AG-05); steer/interrupt de
  jobs (OR-02); `sandboxPolicy`+`approvalPolicy` mapeados no perfil de execução (HX-05) e
  mostrados na prévia (HX-14); aprovações do Codex entram na fila unificada; schemas
  gerados por versão (`generate-ts`) validados em CI (contratos fixados).
- **Hooks:** `hooks.json`/`config.toml` — o HATI **não escreve** sem fluxo MC-03
  (preview/backup/atomic); trust-review por hash é respeitado (nada de bypass).

## 5. ClaudeAdapter

- **Canal de trabalho:** SDK/headless (`claude -p`, stream-json) para jobs dirigidos; PTY
  para o modo interativo.
- **O que habilita:** resume/fork por session id; `--allowedTools` + permission modes
  mapeados do perfil; custos **estimados** capturados do result payload (rotulados
  "estimativa client-side" — US-05); hooks (command/http) para eventos do ciclo; SIGTERM =
  descartar turno, SIGINT/`interrupt()` = interromper graciosamente (usar o correto);
  `system/init.capabilities[]` para feature detection por versão.
- **Guardrails de marca/termos (docs oficiais):** a UI usa "Claude Agent" (nunca "Claude
  Code" como identidade do HATI); autenticação de terceiros por API key (não embutir login
  claude.ai) — registrado como **decisão de conformidade**, não técnica.

## 6. CustomAdapter e detecção (AG-01/02)

- Qualquer CLI = PTY puro com `pty` capability apenas; flags permissivas (AG-03) **nunca
  automáticas** — opt-in por perfil com aviso de shell livre.
- Detecção: PATH/resolvedores comuns; **nunca instala/update sem consentimento** (AG-02);
  versão guardada por instância detectada.

## 7. Falhas, fallback e degradação

1. Protocolo nativo falha/versão fora da faixa → **PTY interativo permanece** (fallback
   universal); capacidades avançadas aparecem "indisponíveis nesta versão" (US-06-style).
2. CLI ausente → projeto abre sem o agente; onboarding sugere instalação manual (AX-24).
3. Evento ausente do provedor → cartão mostra "estado inferido (sem eventos)" (HX-15
   exige distinguir inferido de confirmado).
4. Custo indisponível → "indisponível", nunca zero (US-06).

## 8. Critérios de validação (PoCs 02/03/04 — VALIDATION_PLAN)

- PoC-02 OpenCode: detectar CLI; subir serve; listar sessões do cwd; criar sessão;
  receber `session.idle`; responder uma permissão; custo local visível.
- PoC-03 Codex: inicializar app-server (stdio); thread com sandbox `workspaceWrite`;
  steer + interrupt; aprovação chegando como request; schema TS gerado compila.
- PoC-04 Claude: headless com stream-json; resume por id; custo estimado rotulado;
  hook de Stop acionando notificação AX-31.

**Aprovação humana necessária:** ADR-005 + confirmação de que a v1 suporta **exatamente**
esses três provedores via essa arquitetura (DT-02 já fixa o "quem"; o ADR fixa o "como").
