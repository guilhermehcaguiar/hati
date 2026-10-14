# ADR-005 — Camada de adaptadores de agentes com capacidades declaradas

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** DT-02/R6 — OpenCode + Codex + Claude Code na v1, OpenCode de primeira
  classe (AG-08), sem paridade fictícia (AG-01/13), eventos normalizados (AG-07), retomada
  (AG-04/05), aprovações visíveis (OR-04), fallback seguro (OPEN_QUESTIONS 5). A auditoria
  comprova que heurística de PTY não basta (o próprio Alethe admite isso para OpenCode) e
  que os protocolos divergem da documentação (manutenção contínua por integração).
- **Alternativas consideradas:**
  1. **PTY puro p/ todos** — universal, mas sem eventos/permissões/custos estruturados;
     estado por inferência (HX-15 exige eventos reais).
  2. **Protocolo nativo por provedor + PTY como fallback universal** — cada CLI no seu
     melhor; capacidades declaradas por instância/version; degradação honesta.
  3. **ACP p/ todos** — só OpenCode anuncia ACP hoje; unificar seria paridade fictícia.
  Evidências (docs oficiais out/2026): OpenCode serve/SDK/SSE/plugins (opencode.ai);
  Codex app-server JSON-RPC stdio/unix com threads/turns/approvals/sandboxes e schemas
  gerados (developers.openai.com — WS experimental explícito: não usar); Claude Agent
  SDK/headless stream-json com resume/fork/permission modes/custos estimados
  (code.claude.com).
- **Decisão (recomendação):** **AgentRegistry + adaptadores** no daemon com capability
  flags detectadas por instância e faixas de versão testadas; **OpenCodeAdapter** via
  `opencode serve`+SSE+SDK (1ª classe: sessões por projeto, permissões na fila, diff,
  custo local) + TUI dirigível por `/tui/*`; **CodexAdapter** via app-server stdio/unix
  (threads por cwd, steer/interrupt, sandbox/approval policies, schemas gerados por
  versão fixados em CI); **ClaudeAdapter** via SDK/headless (resume/fork, permission
  modes, custos **marcados estimados**, hooks para eventos); **CustomAdapter** = PTY puro.
  Fallback universal: PTY interativo sempre disponível; capacidades ausentes exibidas
  como "não suportado por este agente". Feature detection em runtime (capabilities do
  `system/init` do Claude, OpenAPI do OpenCode, schemas do Codex). **Pendência:** os
  contratos da OpenCode v2 (anunciada) são re-verificados na PoC-02 antes da
  implementação.
- **Experimento/PoC:** PoC-02 (OpenCode), PoC-03 (Codex), PoC-04 (Claude) —
  VALIDATION_PLAN §1, cada uma com ADR de resultado.
- **Impacto:** Linux/Windows: subprocessos idênticos. Mobile: eventos são a matéria-prima
  do follow agents (RC-07). IDs: AG-01..08/10/13/14, OR-02/04/15, US-04/05, TM-18.
  Riscos: churn de protocolos (mitigado por faixas testadas + feature detection +
  fixtures gravadas em CI); custo Claude = estimativa (rotulada — US-05).
- **Consequências:** melhor: estado vivo real, aprovações nativas, custos locais,
  honestidade por capacidade. Pior: três integrações para manter (orçamento declarado).
  Observar: releases das CLIs × CI de contratos; evolução ACP como futura unificação
  honesta (se os três adotarem).
