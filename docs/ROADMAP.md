# Roadmap do HATI — PROPOSTA de fases

> **Status: PROPOSTA.** Este documento não está aprovado. Ele organiza os 187 IDs já
> decididos em uma sequência sugerida de entregas; **nenhum ID foi reclassificado** e nenhum
> item ADIAR/DESCARTAR entrou nas fases. A ordem interna pode ser refeita pela arquitetura
> (Prompt 03) e por decisões humanas. Fases incrementais são requisito DT-01; "aprovado" na
> matriz nunca significou "primeira versão".

## Princípios de sequenciamento (derivados das DT)

- **DT-02:** OpenCode + Codex + Claude Code na primeira versão; OpenCode é o agente principal.
- **DT-03:** orquestração básica primeiro, ampliada em fases.
- **DT-04:** cada novo job exige confirmação humana — válido em todas as fases.
- **DT-05:** fechar a janela não encerra o serviço — requisito do núcleo, não luxo tardio.
- **DT-08:** API de plugins projetada cedo; catálogo depois.
- **DT-10:** acesso pelo iPhone logo após o núcleo desktop confiável.

## Fase 0 — Fundação (em curso)

Documentação canônica, conferência de escopo, convenções (ADR, handoff) e o estudo de
arquitetura (Prompt 03). **Saída esperada:** decisões arquiteturais aprovadas como ADRs.

## Fase 1 — Núcleo de execução no Linux (proposta)

**Meta:** o fluxo mínimo do produto em Omarchy/Wayland: cadastrar projeto por pasta, abrir
PTYs com os três agentes, retomar sessões, e o serviço sobreviver ao fechamento da janela.

- Projects: WS-01, HX-01 (Hub básico), HX-02 (cadastro inteligente), WS-14, WS-12.
- Terminais: TM-01, TM-02, TM-03, TM-04, TM-05, TM-06, TM-07, TM-09, TM-11, TM-14, TM-15,
  TM-16, TM-17, TM-21, TM-23.
- Ciclo de vida (DT-05 como requisito de fase): WS-03, WS-20, TM-08, AX-30.
- Agentes: AG-01, AG-02, AG-04, AG-05, AG-07, AG-08 (OpenCode de primeira classe), AG-11,
  AG-14.
- Base de UX: LY-01, LY-02, LY-06, AX-16 (i18n pt-BR/EN), AX-17, AX-18, AX-31, WS-18,
  WS-19 (`hati <caminho>`), TM-20.
- Git somente leitura: GT-01 (status), GT-02 (diff), GT-03.
- Saúde e diagnóstico: HX-03, TM-24, TM-25, AX-25.

**Critérios de saída (verificáveis):** (1) projeto criado por pasta abre workspace com
terminais PTY dos 3 agentes; (2) sessão de agente retoma após reinício do app; (3) fechar a
janela mantém jobs rodando (DT-05) com reanexação posterior; (4) nenhuma operação Git de
escrita exposta a automação; (5) i18n com chaves validadas por build.

## Fase 2 — Kanban, tarefas e uso local (proposta)

**Meta:** a tarefa se torna a unidade central do trabalho, com uso/limites locais visíveis.

- Kanban: HX-06, HX-07, HX-08, HX-09, HX-10, HX-12, HX-13, HX-14 (com DT-04), HX-15, AX-03
  (todo integrado, nunca duplicado).
- Layouts com Kanban ao lado: LY-03, LY-04.
- Usage local-first: US-04, US-05, US-09, US-06, US-10, US-11.
- Projeto: HX-05 (perfil de execução por projeto), WS-02, WS-10, WS-11, WS-09, WS-17.
- Continuidade: HX-18, AG-06 (handoff MANTER com pacote revisável), AX-32.

**Critérios de saída:** (1) criar/mover/filtrar tarefas sem efeito de execução; (2) iniciar
agente a partir da tarefa exige confirmação explícita (DT-04); (3) custos/tokens locais do
OpenCode visíveis por sessão/tarefa com fonte declarada; (4) histórico auditável da tarefa.

## Fase 3 — Jobs, worktrees e revisão humana (proposta)

**Meta:** execuções paralelas isoladas, evidências e a fila de revisão — o "cockpit" completo.

- Execução paralela: HX-16, OR-02, OR-03, GT-05 (worktrees), TM-18 (one-shot), OR-06.
- Evidências: HX-17, OR-05, GT-02 (aprimorado), GT-09.
- Board de jobs (não de tarefas): OR-01, OR-04 (gates), OR-10 (routing trace), OR-13
  (skills compatíveis com Agent Suite, sem duplicar lógica), OR-16 (personas reusando
  Athena/Forge/especialistas).
- Revisão humana: HX-19, HX-20, HX-21, HX-22, HX-23, HX-24.
- PRs e integração: GT-06, GT-07, GT-04 (com análise de risco), OR-11 (preparar/inspecionar;
  commit/push/merge humanos).
- Usage remoto consentido: US-01, US-02, US-07, OR-07, OR-08, OR-09 (consciência de cotas
  com comparabilidade explícita).
- Isolamento de execuções: AG-16, OR-15 (OpenCode worker primeira classe; Antigravity
  condicionado a viabilidade, sem antecipar US-03).

**Critérios de saída:** (1) job autorizado roda em worktree isolada com diff/evidências
vinculadas à tarefa; (2) nenhuma integração de resultado sem revisão humana; (3) fila de
revisões unificada funcional; (4) conclusão técnica de job não aprova tarefa.

## Fase 4 — Extensibilidade, MCP e refinos (proposta)

- Plugins (DT-08 — API primeiro): PL-01, PL-02, PL-05, PL-06, PL-04, PL-08 (temas
  sanitizados), PL-03 (catálogo — depois), PL-07 (sem duplicar Kanban).
- MCP e skills: MC-01, MC-02, MC-03, MC-04, MC-05, MC-07, MC-08, MC-09, MC-06.
- Conteúdo do workspace: AX-01 (Home operacional), AX-04, AX-05, AX-06, AX-08 (Graphify
  original), AX-28 (sidebar direita contextual).
- Robustez e dados: AX-20 (backup), AX-23 (update assinado opt-in), AX-24 (onboarding),
  AX-26 (telemetria local), AX-27 (inspector), AX-29 (resource manager), AX-19, WS-13
  (clone sem writes automáticos), HX-02 completo (reconexão de pastas movidas).

**Critérios de saída:** (1) capacidades de plugin declaradas e rejeitadas quando excedidas,
com core 100% funcional sem plugins; (2) edição de MCP com preview/validação/backup/rollback;
(3) backup/restauração seletiva validada.

## Fase 5 — Acesso seguro pelo iPhone (proposta, DT-10/DT-06)

- RC-01, RC-02, RC-03, RC-05, RC-06, RC-07, HX-25.
- Transporte protegido obrigatório (RC-04 está DESCARTADO); túnel/TLS a definir na
  arquitetura; Git protegido permanece no desktop.

**Critérios de saída:** (1) iPhone pareado vê Kanban/sessões/aprovações com opt-ins
granulares e revogação; (2) nenhuma ação Git protegida acessível pelo celular; (3) sessão
remota autenticada com expiração e auditoria.

## Fase 6 — Windows (proposta)

- TM-10 (Job Objects), TM-13 (prioridade), TM-19/TM-26 (WSL), AX-19 (opacidade de janela) e
  portabilização geral do núcleo.

## Fora das fases (por decisão humana)

- **25 ADIAR:** WS-06, WS-15, WS-21, LY-05, TM-10*, TM-13*, TM-19*, TM-26*, AG-12, US-03,
  US-08, OR-12, GT-08, AX-02, AX-07, AX-09, AX-10, AX-11, AX-12, AX-13, AX-21, AX-22,
  HX-04, HX-11, HX-26 — (* os marcados movem-se para a Fase 6 por serem específicos de
  Windows; os demais permanecem adiados sem prazo).
- **6 DESCARTAR:** WS-16, TM-22, AG-09, AG-15, OR-14, RC-04 — não são reabertos por roadmap.

## Regras deste documento

1. Este roadmap **não aprova nem desaprova** nada além do que a matriz já decidiu.
2. Mudanças de fase exigem decisão humana registrada (e atualização da matriz, se mudar status).
3. Cada fase termina com revisão humana e handoff documentado antes da próxima começar.
4. Nenhum prazo: quando a arquitetura e a realidade indicarem, o usuário decide.
