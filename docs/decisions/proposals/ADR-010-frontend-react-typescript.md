# ADR-010 — Frontend React + TypeScript

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** Prompt 03 §5A exige avaliar frontend, gerenciamento de estado, sistema de
  componentes e layouts **sem assumir React como decisão definitiva**. A GUI é cliente do
  daemon (ADR-002) — o contrato real é a API IPC + eventos (ADR-004); o framework é uma
  decisão de risco/benefício, não de contrato. Requisitos de UI: terminais xterm.js
  (TM-21), Kanban drag&drop denso (HX-06, WS-09), layouts redimensionáveis (LY-*),
  i18n tipado (AX-16), temas sanitizados (AX-14/15/PL-08), command palette (AX-17).
- **Alternativas consideradas:**
  1. **React 18/19 + TypeScript + Vite** — ecossistema maior para os componentes críticos
     (xterm bindings, dnd-kit, react-resizable-panels, virtualização); contratos TS dos
     três SDKs de agentes (OpenCode SDK, @openai/codex-sdk, Claude Agent SDK TS) e dos
     schemas gerados do Codex compõem direto com o daemon; afinidade da Agent Suite
     (Pixel) e suporte do Tauri documentado. Custo: bundle maior que alternativas finas.
  2. **Svelte 5 / SolidJS** — reatividade fina e bundle menor; ecossistema de
     terminal/drag&drop mais pobre; menos contratos diretos; onda de aprendizado
     desnecessária para o ganho (a GUI não é o gargalo de desempenho — o daemon é).
  3. **Vue 3** — meio-termo maduro; mesmo argumento de ecossistema do item 2.
  Estado: **Zustand** (UI, não-persistido — o domínio vive no daemon) + camada de cache
  do cliente (TanStack Query sobre a API do daemon) vs. Redux Toolkit (mais cerimônia,
  sem ganho aqui) — a decisão de estado é subordinada e reversível.
- **Decisão (recomendação):** **React + TypeScript (Vite)** com **Zustand** para estado de
  UI e cache derivado da API do daemon; componentes base via biblioteca acessível
  (Radix/UI-headless + Tailwind tokens sanitizados — fixar na implementação da F1);
  xterm.js + addons; i18n com chaves validadas por build (`tsc` falha com chave faltando —
  padrão comprovado AX-16). A escolha é reversível com custo moderado **porque o contrato
  é o daemon**; este ADR existe para registrar o trade-off, não para blindar a escolha.
- **Experimento/PoC:** PoC-06 valida a shell+terminal (ADR-001); na F1, spike de 1 semana
  de layout esqueleto (sidebar + workspace + 1 terminal + Kanban estático) para calibrar
  ergonomia antes de generalizar.
- **Impacto:** Linux/Windows: idêntico. Mobile: a PWA (F5) pode reaproveitar
  componentes/lógica de cliente. IDs: TM-02/21, LY-01..06, WS-09/17, AX-14..18, HX-06/07.
  Riscos: re-renders em listas grandes (mitigados por virtualização + seletores finos —
  lição da auditoria §12); bundle (code-splitting por painel, lazy).
- **Consequências:** melhor: velocidade de construção com ecossistema maduro e contratos
  TS prontos. Pior: não é o bundle mínimo absoluto. Observar: desempenho do Kanban com
  10k tarefas (PoC-05 cobre o lado daemon; UI virtualizada cobre o lado cliente).
