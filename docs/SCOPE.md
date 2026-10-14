# Escopo aprovado do HATI — 187 IDs + 10 DT

> Resumo operacional. As fontes canônicas são:
> `docs/hati-context/scope/SCOPE_MATRIX.md` (status e **intenção específica** de cada ID),
> `docs/hati-context/scope/HX_REQUIREMENTS.md` (definições HX),
> `docs/hati-context/scope/DECISIONS.json` (dados estruturados) e
> `docs/hati-context/governance/DIRECTIVES_DT.md` (diretrizes imutáveis).
> Este arquivo resume; **nunca substitui** a matriz. Em caso de divergência, a matriz e as
> DT prevalecem.

## Como ler os status

- **MANTER** — preservar a capacidade (com a intenção descrita na matriz).
- **MELHORAR** — aprovado **com refinamento específico**: a coluna "Intenção específica
  para HATI" da matriz é parte da especificação; código de status e nome não bastam.
- **ADIAR** — fora das entregas iniciais; **não é descartado** e pode voltar por decisão humana.
- **DESCARTAR** — excluído conforme a delimitação escrita do ID (atenção: alguns descartes
  são pontuais e protegem capacidades vizinhas — ver §6).

**Aprovado ≠ MVP ≠ implementado.** As 156 capacidades MANTER/MELHORAR definem o **escopo do
produto completo**; a primeira entrega será um subconjunto proposto em `docs/ROADMAP.md`.

## 1. Visão geral (conferida manualmente em 14/10/2026)

| Módulo | MANTER | MELHORAR | ADIAR | DESCARTAR | Total |
|---|---:|---:|---:|---:|---:|
| WS — Workspace e Projects | 11 | 6 | 3 | 1 | 21 |
| LY — Layouts | 4 | 1 | 1 | 0 | 6 |
| TM — Terminais e PTYs | 10 | 11 | 4 | 1 | 26 |
| AG — Agentes de Programação | 3 | 10 | 1 | 2 | 16 |
| US — Usage e Limites | 2 | 7 | 2 | 0 | 11 |
| OR — Orquestração | 1 | 13 | 1 | 1 | 16 |
| MC — MCPs e Skills | 1 | 8 | 0 | 0 | 9 |
| PL — Plugins | 1 | 7 | 0 | 0 | 8 |
| GT — Git | 1 | 7 | 1 | 0 | 9 |
| AX — Recursos Complementares | 3 | 20 | 9 | 0 | 32 |
| RC — Acesso Remoto e Mobile | 0 | 6 | 0 | 1 | 7 |
| HX — Exclusivas do HATI | 7 | 16 | 3 | 0 | 26 |
| **TOTAL** | **44** | **112** | **25** | **6** | **187** |

Origem: 161 IDs WS–RC avaliados a partir da auditoria do Alethe + 26 HX exclusivos = 187.

## 2. IDs por status (referência rápida)

**MANTER (44):**
WS-02, WS-04, WS-05, WS-07, WS-08, WS-10, WS-11, WS-12, WS-14, WS-18, WS-19 ·
LY-01, LY-02, LY-03, LY-06 ·
TM-04, TM-05, TM-06, TM-14, TM-15, TM-16, TM-17, TM-18, TM-21, TM-23 ·
AG-06, AG-11, AG-14 · US-07, US-09 · OR-10 · MC-06 · PL-04 · GT-03 ·
AX-06, AX-15, AX-18 ·
HX-03, HX-08, HX-09, HX-12, HX-18, HX-20, HX-23

**MELHORAR (112):**
WS-01, WS-03, WS-09, WS-13, WS-17, WS-20 · LY-04 ·
TM-01, TM-02, TM-03, TM-07, TM-08, TM-09, TM-11, TM-12, TM-20, TM-24, TM-25 ·
AG-01, AG-02, AG-03, AG-04, AG-05, AG-07, AG-08, AG-10, AG-13, AG-16 ·
US-01, US-02, US-04, US-05, US-06, US-10, US-11 ·
OR-01, OR-02, OR-03, OR-04, OR-05, OR-06, OR-07, OR-08, OR-09, OR-11, OR-13, OR-15, OR-16 ·
MC-01, MC-02, MC-03, MC-04, MC-05, MC-07, MC-08, MC-09 ·
PL-01, PL-02, PL-03, PL-05, PL-06, PL-07, PL-08 ·
GT-01, GT-02, GT-04, GT-05, GT-06, GT-07, GT-09 ·
AX-01, AX-03, AX-04, AX-05, AX-08, AX-14, AX-16, AX-17, AX-19, AX-20, AX-23, AX-24, AX-25,
AX-26, AX-27, AX-28, AX-29, AX-30, AX-31, AX-32 ·
RC-01, RC-02, RC-03, RC-05, RC-06, RC-07 ·
HX-01, HX-02, HX-05, HX-06, HX-07, HX-10, HX-13, HX-14, HX-15, HX-16, HX-17, HX-19, HX-21,
HX-22, HX-24, HX-25

**ADIAR (25):**
WS-06, WS-15, WS-21 · LY-05 · TM-10, TM-13, TM-19, TM-26 · AG-12 · US-03, US-08 · OR-12 ·
GT-08 · AX-02, AX-07, AX-09, AX-10, AX-11, AX-12, AX-13, AX-21, AX-22 · HX-04, HX-11, HX-26

**DESCARTAR (6):**
WS-16, TM-22, AG-09, AG-15, OR-14, RC-04

## 3. Temas transversais dos 112 MELHORAR (a intenção completa vive na matriz)

- **Ciclo de vida de processos e janela (WS-03, WS-20, TM-07, TM-08, TM-09, TM-11, AX-30):**
  distinguir ocultar × pausar × suspender × encerrar, com estados e avisos explícitos;
  encerramento gracioso antes de forçar; órfãos reconciliados por identidade comprovada;
  fechar a janela **não** encerra o serviço (DT-05).
- **Kanban como fonte única de tarefas (HX-06, HX-07, AX-03, PL-07, OR-01):** o quadro é a
  única fonte; plugins estendem, não duplicam; o Orchestration Board mostra **jobs**, não
  tarefas — nunca uma segunda fonte de tarefas.
- **Vínculo tarefa × job × evidência (HX-13, HX-14, HX-15, HX-16, HX-17, OR-05, OR-06, GT-02):**
  não assumir 1:1; cada tentativa é um job; progresso por eventos reais; evidências com
  fonte/data/confiabilidade; **cada nova execução exige confirmação humana** (DT-04).
- **Integração profunda e honesta com agentes (AG-01, AG-04, AG-05, AG-07, AG-08, AG-13,
  OR-02, OR-15):** OpenCode de primeira classe (além do PTY), Codex app-server quando
  viável, eventos normalizados sem writes silenciosos em configs globais; sem paridade
  fictícia entre provedores.
- **Consentimento, segredos e permissões (MC-05, US-09 contexto, HX-19, HX-21, HX-22, HX-23,
  PL-02, RC-03):** segredos ocultos por padrão e tratados no backend; consentimento por
  provedor; políticas por tarefa/execução combinadas com sandbox e dispositivo; trilha de
  auditoria sem segredos em eventos.
- **Transparência de dados de uso (US-01, US-02, US-04, US-05, US-06, US-10, US-11):**
  fontes locais primeiro, frescor e indisponibilidade visíveis, estimativas explícitas,
  consentimento revogável; não confundir custo local com cota remota.
- **Git com controle humano (GT-01, GT-04, GT-05, GT-06, GT-07, GT-09, OR-11):** operações
  explícitas com análise de risco; worktrees com ciclo de vida sem descarte de trabalho sem
  revisão; integração preparada, mas commit/push/merge sempre humanos.
- **Extensibilidade com core forte (PL-01, PL-05, PL-06, PL-08, AX-28, HX-24):** API de
  plugins projetada cedo (DT-08), capacidades explícitas, isolamento real planejado
  (WebView compartilhada não basta), temas sanitizados sem código arbitrário.
- **Qualidade de vida do workspace (WS-01, WS-09, WS-13, WS-17, LY-04, TM-02, TM-03, TM-12,
  TM-20, TM-24, TM-25, AX-01, AX-04, AX-05, AX-16, AX-17, AX-19, AX-23, AX-24, AX-25,
  AX-26, AX-27, AX-29, AX-31, RC-01, RC-02, RC-05, RC-06, RC-07, HX-01, HX-02, HX-05,
  HX-10, HX-24, HX-25):** UX densa e acessível, i18n pt-BR/EN validado por tipagem,
  diagnósticos sanitizados, mobile pareado com revogação/auditoria e **nenhum transporte
  desprotegido** (RC-04 descartado).

## 4. Diretrizes DT-01 a DT-10 (imutáveis — texto integral em `governance/DIRECTIVES_DT.md`)

| DT | Tema | Escolha definitiva |
|---|---|---|
| DT-01 | Estratégia de entregas | Fases incrementais |
| DT-02 | Agentes da primeira versão | OpenCode + Codex + Claude Code |
| DT-03 | Profundidade inicial da orquestração | Primeiro fluxo básico; ampliar em fases |
| DT-04 | Autorização para iniciar tarefas | Confirmar início de **cada** execução |
| DT-05 | Fechamento da janela | Manter serviço e execuções ativos |
| DT-06 | Operações Git pelo iPhone | Revisar e aprovar; Git protegido no desktop |
| DT-07 | Localização dos dados | Dados próprios fora dos repositórios por padrão |
| DT-08 | Prioridade do sistema de plugins | Projetar API cedo; catálogo depois |
| DT-09 | Modelo de usuários | Usuário local único + dispositivos pareados |
| DT-10 | Prioridade do acesso pelo iPhone | Logo após o núcleo desktop confiável |

## 5. Divergências preservadas (auditoria × decisão humana)

A recomendação preliminar da auditoria **não** é a decisão. Exemplos onde a escolha humana
diferiu do parecer técnico (todos legítimos e registrados):

- `AG-06` (handoff entre agentes): recomendação "questionável" → decisão **MANTER** com
  pacote revisável e proteção de segredos.
- `OR-01..OR-09, OR-11, OR-13, OR-15, OR-16` (orquestração): recomendados "questionáveis"
  → **MELHORAR**, com confirmação humana por job e OpenCode worker como prioridade.
- `WS-13`: clonagem aprovada, mas **sem** escrever AGENTS.md/CLAUDE.md automaticamente.
- `PL-03` (catálogo de plugins): recomendado opcional → **MELHORAR** (fase futura, DT-08).
- `US-03` (usage Antigravity): → **ADIAR** (evitar APIs internas sem garantia).

## 6. Descartes delimitados (ler a intenção, não só o status)

- **WS-16** descarta o *modo especial de projeto* "agentSandbox" — **não** descarta
  isolamento de workers (que segue em `AG-16` MELHORAR).
- **AG-15** descarta a *POC abandonada* Agent Canvas — o painel de orquestração original
  segue no escopo OR.
- **AG-09** descarta a integração específica *GSD Sync* — não descarta planejamento/Kanban.
- **TM-22** descarta Ghostty nativo macOS — não fecha a porta a renderizações futuras.
- **OR-14** descarta o segundo orquestrador redundante.
- **RC-04** descarta transporte remoto sem TLS — o acesso remoto seguro continua no escopo
  (RC-01, RC-02, RC-03, RC-05, RC-06, RC-07, HX-25).

## 7. Mudanças de escopo

Somente por decisão humana explícita, registrada na matriz (e no `DECISIONS.json`).
Propostas de IA para reclassificar IDs são sugestões e nunca devem ser aplicadas
silenciosamente.
