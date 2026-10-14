# ADR-006 — Autorização no backend e fronteira Git

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** DT-04/R2 — cada novo job exige confirmação humana e a UI nunca é a única
  barreira; HX-21 (políticas combinando projeto, executor, sandbox, dispositivo);
  HX-22/DT-06 — commit/push/merge sob controle humano, Git protegido no desktop;
  HX-19/20/23 — fila unificada, decisão explícita, auditoria sem segredos; OR-04 gates;
  CROSS_REVIEW/AG-03 — agentes com shell livre contornam proteções de UI (não prometer
  bloqueio absoluto).
- **Alternativas consideradas:**
  1. **PolicyEngine no daemon** — JobGate por DT-04 + perfis (HX-05) + policies por tarefa
     (HX-21) + enforcement delegado aos permission modes/sandboxes das CLIs (reais) +
     worktrees isolando escrita (F3) + auditoria append-only.
  2. **Sandbox próprio p/ tudo** (namespaces/bwrap) — enforcement real, mas superfície
     grande; apropriado só p/ workers isolados (AG-16, F3, PoC própria) — não p/ o núcleo.
  3. **Confiança no usuário (sem gates)** — viola DT-04; inviável.
  Nota honesta: nenhuma alternativa elimina A1 (shell livre) — o desenho **declara** o
  limite e empilha as camadas reais existentes.
- **Decisão (recomendação):** (1) `Job` nasce `pending_approval`; o daemon **não possui**
  caminho de spawn sem `Approval` resolvida por humano (desktop ou dispositivo com escopo
  `approve` — RC-03); (2) prévia obrigatória (HX-14) mostrando escopo, permissões efetivas
  (permission mode/sandbox da CLI), worktree e custo estimado; (3) **GitService expõe
  apenas leitura para automação** (status/diff/log/list); commit/push/merge existem só
  como fluxo humano explícito com trilha — e são **inexistentes** no contrato mobile;
  (4) auditoria append-only (HX-23) sem segredos; (5) a UI exibe aviso permanente de que
  guardas são facilitação e o enforcement real vem dos modos de permissão/sandbox das
  CLIs + worktrees (declaração de limite, PL-02-style); (6) flags permissivas de CLI
  (AG-03) nunca aplicadas automaticamente — opt-in por perfil com aviso.
- **Experimento/PoC:** invariante testável "sem Approval não há spawn" (teste que
  inspeciona o fonte — padrão da auditoria §14) + e2e do fluxo HX-14 + enumeração do
  contrato IPC provando ausência de Git de escrita para automação + PoC de sandbox de
  workers na F3 (ADR futuro).
- **Impacto:** Linux/Windows: igual. Mobile: aprovação remota só com escopo explícito
  (RC-03); Git protegido inexistente lá (DT-06). IDs: DT-04/06, HX-14/19..23, OR-04/08,
  GT-04/11, RC-02/03, AG-03/16, MC-05. Riscos: falso senso de segurança (mitigado pela
  declaração de limite visível); fricção de aprovação (mitigada por fila unificada com
  contexto exato — HX-19).
- **Consequências:** melhor: DT-04 cumprido fora da UI; trilha auditável completa;
  expectativa honesta. Pior: uma etapa a mais em cada job (intencional — é o produto).
  Observar: taxa de aprovação remota e clareza da prévia (ajustar UX iterativamente).
