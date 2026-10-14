# ADR-009 — Usage local-first com consentimento

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** R13/US-* — local-first; scraping remoto apenas consentido com frescor/
  erros visíveis (US-06); consentimento explícito por provedor, default off, revogável
  (US-09); tokens/custo por sessão/job (US-05, OR-06) com fontes/estimativas explícitas;
  histórico por projeto/agente/sessão/job (US-04); custo local ≠ cota remota (US-04);
  cotas heterogéneas não são equivalentes (OR-09); US-03 (Antigravity) ADIAR mantido.
  A auditoria comprova a fragilidade do scraping (issues #272; APIs beta/internas) e
  recomenda inverter a prioridade para fontes locais.
- **Alternativas consideradas:**
  1. **Fontes locais primeiro** (OpenCode server/DB local; custos reportados pelo headless
     do Claude; dados do app-server do Codex) + scraping remoto **consentido** depois —
     auditável, alinhado ao perfil OpenCode-nativo.
  2. **Scraping remoto primeiro** (estilo Alethe) — obtém cotas de assinatura inatingíveis
     localmente, mas frágil e contraria a intenção aprovada de prioridade local.
  3. **Nenhum coletor** — viola US-04/05/10 aprovados.
  Evidências: OpenCode mantém dados de sessão/custo acessíveis localmente (server/DB —
  auditoria US-04); Claude headless reporta `total_cost_usd` por modelo **como estimativa
  client-side** (docs oficiais: "can differ from your actual bill" — rotular!); Codex
  expõe dados por thread/turn no app-server (cobertura a confirmar na PoC-03).
- **Decisão (recomendação):** **UsageService** no daemon com providers plugáveis e
  capacidades: (1) **OpenCode local** (mais rico, primeiro); (2) **Claude estimado**
  (capturado dos resultados headless, sempre rotulado "estimativa client-side" — US-05);
  (3) **Codex** conforme disponível no app-server; (4) **scraping remoto (US-01/02)
  opcional e desligado por default**, fase posterior, com frescor/indisponibilidade
  visíveis (US-06) e comparabilidade explícita (OR-09: janelas incomparáveis nunca
  somadas); indisponibilidade nunca renderiza como zero; tabela de preços versionada
  como dado com fonte/data (HX-17); **orçamentos/limites permanecem ADIAR (HX-26)** —
  custos reais registrados desde o início; consentimento off = **nada** lê o provedor
  (US-09); sem telemetria externa (AX-26).
- **Experimento/PoC:** coberto pelas PoC-02/03/04 (custos vêm dos mesmos contratos);
  e2e: custo visível por tarefa com fonte declarada; consentimento off testado (zero
  acesso a credenciais); estimativa sempre rotulada; indisponível exibido como estado.
- **Impacto:** Linux/Windows: igual. Mobile: usage visível na PWA (read-only). IDs:
  US-01..02/04..07/09..11, OR-06/07/09, HX-26 (ADIAR mantido), AX-26. Riscos: cobertura
  desigual por provedor (exibida honestamente por capacidades); preços embutidos
  envelhecem (versionados com fonte/data).
- **Consequências:** melhor: auditável, barato, resiliente a churn de APIs remotas.
  Pior: cotas de assinatura só com scraping consentido posterior. Observar: qualidade
  dos dados locais do OpenCode v2 na prática e a rotulagem de estimativas na UX.
