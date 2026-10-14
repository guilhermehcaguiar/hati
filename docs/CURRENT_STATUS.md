# CURRENT_STATUS — Estado real verificado do repositório HATI

> Manter este arquivo sempre atualizado. "Verificado" significa inspecionado por ferramenta
> nativa de leitura nesta máquina; o que não foi verificado está declarado como tal.

**Data-base deste registro:** 14/10/2026 (fundação documental — Prompt 02; materialização
física — Prompt 02B); atualizado em 15/10/2026 (Prompt 03 — proposta arquitetural).

## 1. Verificado por inspeção (leitura nativa, sem comandos)

- **16 arquivos físicos** no repositório, todos lidos integralmente:
  `HATI_CONTEXT_INSTRUCOES.md`; `docs/hati-context/README.md`, `MANIFEST.json`,
  `governance/PROVENANCE_AND_PRIORITY.md`, `governance/PRODUCT_PRINCIPLES.md`,
  `governance/DIRECTIVES_DT.md`, `scope/SCOPE_MATRIX.md`, `scope/HX_REQUIREMENTS.md`,
  `scope/DECISIONS.json`, `architecture/CROSS_REVIEW.md`, `architecture/OPEN_QUESTIONS.md`,
  `workflow/CURRENT_STATUS.md`, `workflow/AI_PORTABILITY.md`,
  `workflow/SESSION_HANDOFF_TEMPLATE.md`, `source/ALETHE_AUDIT_ORIGINAL.txt`,
  `assets/brand/RUNA_REFERENCIA_USUARIO.jpeg` (imagem não aberta por leitura de texto).
- **Nenhum código do aplicativo existe.** Nenhuma stack/dependência escolhida.
- **Nenhum README.md/AGENTS.md/CLAUDE.md/docs canônicos pré-existentes** — a fundação
  documental foi criação, sem conflitos de sobreposição.
- **Pacote de contexto íntegro e conferido manualmente:** 187 IDs (161 WS–RC + 26 HX),
  44 MANTER / 112 MELHORAR / 25 ADIAR / 6 DESCARTAR, 10 DT — consistente entre
  `SCOPE_MATRIX.md`, `DECISIONS.json` e `MANIFEST.json`. Conferência manual, sem validação
  automática por script.
- **14 documentos canônicos presentes no disco** (verificados por listagem pós-gravação,
  14/10/2026 — materialização pelo Forge, Prompt 02B): `README.md`, `AGENTS.md`,
  `CLAUDE.md`, `docs/START_HERE.md`, `docs/PRODUCT_VISION.md`, `docs/SCOPE.md`,
  `docs/ROADMAP.md`, `docs/ARCHITECTURE.md`, `docs/DEVELOPMENT.md`,
  `docs/CURRENT_STATUS.md`, `docs/HANDOFF.md`, `docs/decisions/README.md`,
  `docs/reference/SCOPE_MATRIX.md`, `docs/reference/ALETHE_AUDIT.md`.
- **17 arquivos de proposta arquitetural criados em 15/10/2026 (Prompt 03 — Athena,
  sem shell para escrita; ferramenta nativa de escrita; verificados por listagem e grep):**
  `docs/architecture/TECHNICAL_STUDY.md` (14 questões respondidas + apêndices),
  `SYSTEM_DESIGN.md`, `DATA_MODEL.md`, `AGENT_INTEGRATIONS.md`,
  `SECURITY_AND_RUNTIME.md`, `VALIDATION_PLAN.md` (6 arquivos; 1.633 linhas somadas) e
  `docs/decisions/proposals/` (README + ADR-001..010, todos com status **proposta**;
  425 linhas somadas). Total: 2.058 linhas. **Nenhum é decisão aprovada.**
- **Pesquisa externa do Prompt 03 (14–15/10/2026):** documentação oficial consultada por
  fetch direto (OpenCode server/plugins/ACP/v2, Codex app-server/SDK/hooks, Claude Code
  headless/hooks/Agent SDK, Tauri 2, Electron, systemd/loginctl 262, gitoxide, libgit2,
  rusqlite, MCP spec 2026-07-28, Tailscale, xterm.js). Busca web da sessão indisponível
  (cancelada) — ver Apêndice B do TECHNICAL_STUDY. Nenhuma PoC executada (proibida).

## 2. NÃO verificado (sem shell nesta fase, por decisão do usuário)

- Estado Git do repositório (existência de `.git`, branch, commits, remotos) — a verificação
  e eventual versionamento são ações humanas.
- Hashes SHA-256 dos arquivos do pacote (os valores declarados no `MANIFEST.json` não foram
  recomputados por ferramenta nesta sessão).

## 3. Documentação canônica — situação

Os **14 documentos canônicos** foram redigidos pela Athena em 14/10/2026 (Prompt 02,
entregues na conversa em 2 lotes) e **materializados em disco em 14/10/2026 pelo Forge**
(Prompt 02B), com a ferramenta nativa de escrita (`write`) — o shell permaneceu negado e
não foi usado. A existência física dos 14 foi verificada por listagem pós-gravação.

Checklist de gravação (concluído em 14/10/2026 — Prompt 02B):
- [x] `README.md`
- [x] `AGENTS.md`
- [x] `CLAUDE.md`
- [x] `docs/START_HERE.md`
- [x] `docs/PRODUCT_VISION.md`
- [x] `docs/SCOPE.md`
- [x] `docs/ROADMAP.md`
- [x] `docs/ARCHITECTURE.md`
- [x] `docs/DEVELOPMENT.md`
- [x] `docs/CURRENT_STATUS.md` (este arquivo — ao gravar, ajuste os itens 3 a 5 conforme o real)
- [x] `docs/HANDOFF.md`
- [x] `docs/decisions/README.md`
- [x] `docs/reference/SCOPE_MATRIX.md`
- [x] `docs/reference/ALETHE_AUDIT.md`

## 4. Pendências abertas

1. Gravação dos 14 documentos: **concluída** em 14/10/2026 (Forge, Prompt 02B, via
   ferramenta nativa de escrita; existência verificada por listagem).
2. `HATI_PROMPT_02_ATHENA.md` listado no `MANIFEST.json` está **ausente no disco** — o
   usuário fornecerá o original (não criar substituto: o SHA-256 declarado deve ser
   preservado).
3. Versionamento Git (init/clone/remoto) — decisão e execução humanas; agentes nunca
   executam Git de escrita.
4. Licença do HATI — indefinida; decisão humana futura.
5. Identidade visual final (tipografia, logo, tokens) — apenas a referência da runa existe.
6. Arquitetura (Prompt 03) — **proposta produzida** (15/10/2026): 14 questões abertas
   respondidas em `docs/architecture/TECHNICAL_STUDY.md`, com 10 ADRs propostos em
   `docs/decisions/proposals/` (nenhum aprovado). **Aguarda revisão e decisão humanas.**
   Pendência técnica registrada: contratos da OpenCode v2 a re-verificar na PoC-02.
7. Roadmap em `docs/ROADMAP.md` é **proposta** — requer aprovação/ajuste humanos.

## 5. Próximo passo

1. Revisão humana da fundação documental (14 documentos materializados em disco).
2. Fornecimento do `HATI_PROMPT_02_ATHENA.md` original (ausente no disco).
3. Versionamento Git — decisão e execução humanas.
4. **Revisão humana da proposta arquitetural do Prompt 03**: TECHNICAL_STUDY.md (14
   questões), SYSTEM_DESIGN, DATA_MODEL, AGENT_INTEGRATIONS, SECURITY_AND_RUNTIME,
   VALIDATION_PLAN e ADR-001..010 (propostas). Aprovações por ADR individual; depois,
   autorização das PoCs (PoC-01/02/03/04/06) como etapa futura própria — a transição
   Fase 0→1 está descrita em `docs/architecture/VALIDATION_PLAN.md` §6.

## 6. Regra de manutenção

Toda sessão que alterar o repositório deve atualizar as seções 1, 4 e 5 deste arquivo e
registrar o handoff em `docs/HANDOFF.md`. O que não é verificado é escrito como "não
verificado" — nunca presumido.
