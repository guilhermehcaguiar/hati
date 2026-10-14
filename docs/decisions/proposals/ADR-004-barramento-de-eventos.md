# ADR-004 — Barramento de eventos append-only normalizado

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** R9 — eventos confiáveis e normalizados por agente para estado vivo de
  cartões (HX-15), usage (US-*), alertas (AX-31) e clientes distintos desktop/mobile
  (AX-31/RC-07/HX-24 — CROSS_REVIEW: eventos compartilhados com clientes distintos);
  schema versionado, snapshots, reconciliação, replay, backpressure (OPEN_QUESTIONS 4).
  Os três provedores emitem eventos, cada um no seu formato (AGENT_INTEGRATIONS §2).
- **Alternativas consideradas:**
  1. **EventEnvelope append-only no SQLite + streaming SSE/WS + snapshots materializados** —
     eventos são fatos imutáveis com `schema_version`; consumers por cursor; snapshots
     consultáveis sem replay; mesma base do store (transacional com entidades).
  2. **Eventos efêmeros em memória** — perde auditoria (HX-12), replay e reconexão de
     clientes (mobile).
  3. **Event sourcing completo** (estado 100% derivado) — custo alto sem benefício: o
     Kanban é mutável e consultável por natureza; snapshots materializados bastam.
  Evidência de padrão: batching 16ms/64KB + mpsc comprovado na auditoria §8; SSE nativo do
     OpenCode; notificações JSON-RPC do Codex; stream-json/hooks do Claude.
- **Decisão (recomendação):** todos os fatos de domínio (job/task/terminal/evidence/
  approval/usage/policy/device) viram `EventEnvelope(id, stream, type, schema_version,
  payload, occurred_at, source, correlation_id)` em `hati.db` (append-only); o daemon
  publica por SSE/WS com **cursor por stream**; snapshots materializados em tabelas
  consultáveis; reconciliação = replay do cursor; backpressure = coalescing de lotes
  (16ms/64KB) + descarte de deltas intermediários (mantém último); byte-stream de PTY
  **não** é evento de domínio (vai a scrollback/lotes de render); emissão **sanitizada**
  (HX-23: sem segredos); mudanças de payload = novo `schema_version` com teste de replay
  compatível.
- **Experimento/PoC:** PoC-08: 5k eventos/s por 60s sem latência perceptível; reconexão
  por cursor sem lacunas; replay de 100k eventos reconstrói snapshot idêntico.
- **Impacto:** Linux/Windows: idêntico. Mobile: reconexão barata por cursor (sem replay
  total). IDs: HX-15, AG-07, AX-31, RC-07, US-06, HX-24, OR-06. Riscos: volume de
  eventos (mitigado por coalescing e prune opcional); versionamento de payload (testes
  de compatibilidade em CI).
- **Consequências:** melhor: uma fonte viva, clientes consistentes (desktop/mobile),
  auditoria gratuita, progresso real vs. inferido (HX-15). Pior: disciplina de schema
  versionado. Observar: custo de escrita em rajadas e o tamanho do log em uso intenso.
