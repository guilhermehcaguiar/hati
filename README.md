# HATI ᚼᛅᛏᛁ

**HATI** é um workspace desktop independente para desenvolvimento assistido por múltiplos
agentes de IA: projetos locais, terminais reais (PTYs), Kanban nativo por projeto, agentes
OpenCode, Codex e Claude Code, monitoramento de uso e limites, worktrees, revisão humana
explícita e — em fase futura — acesso seguro pelo iPhone.

Não é uma extensão de IDE, não é uma IDE tradicional e **não é um fork do Alethe**. O Alethe
(commit `f09c597…`, v1.7.0, auditado em 13/10/2026) é **referência funcional e factual**,
preservada em `docs/hati-context/source/ALETHE_AUDIT_ORIGINAL.txt` — nunca autorização para
copiar código, arte ou identidade (licença AGPL-3.0-or-later do Alethe; o HATI é obra original).

## Status real do repositório (outubro de 2026)

- **Nenhuma linha de código do aplicativo existe.** Nenhuma stack, framework, linguagem ou
  banco de dados foi escolhido — arquitetura será estudada na fase "Prompt 03".
- **Escopo de produto decidido e registrado:** 187 IDs (161 da auditoria do Alethe + 26
  exclusivos do HATI), classificados em 44 MANTER · 112 MELHORAR · 25 ADIAR · 6 DESCARTAR,
  mais 10 diretrizes definitivas (DT-01 a DT-10). Detalhe em `docs/SCOPE.md`.
- **Aprovado ≠ MVP ≠ implementado.** As 156 capacidades mantidas/melhoradas são o escopo do
  produto completo; as primeiras entregas serão um subconjunto proposto em `docs/ROADMAP.md`.
- O estado momento-a-momento vive em `docs/CURRENT_STATUS.md` — leia-o antes de afirmar
  qualquer coisa sobre o repositório.
- O repositório carrega apenas documentação e o pacote de contexto. Estado Git: verificado
  apenas por inspeção de arquivos; nenhum comando foi executado por agentes nesta fase.

## Índice da documentação

| Caminho | Conteúdo |
|---|---|
| `docs/START_HERE.md` | Sequência mínima de leitura para qualquer IA ou pessoa nova |
| `docs/PRODUCT_VISION.md` | Visão, princípios e diferenciação do produto |
| `docs/SCOPE.md` | Escopo aprovado (187 IDs + 10 DT) e como lê-lo |
| `docs/ROADMAP.md` | **Proposta** de fases de entrega (sem prazos fictícios) |
| `docs/ARCHITECTURE.md` | Requisitos, alternativas e questões em aberto (pré-decisão de stack) |
| `docs/DEVELOPMENT.md` | Trabalho multi-PC/multi-IA, portabilidade, segurança |
| `docs/CURRENT_STATUS.md` | Estado verificado, pendências e próximo passo |
| `docs/HANDOFF.md` | Protocolo de continuidade de sessões entre máquinas e IAs |
| `docs/decisions/README.md` | Convenção para futuros ADRs (decision records) |
| `docs/reference/SCOPE_MATRIX.md` | Índice para a matriz de 187 decisões |
| `docs/reference/ALETHE_AUDIT.md` | Índice para a auditoria original do Alethe |
| `docs/hati-context/` | Pacote de contexto imutável (proveniência, hashes em `MANIFEST.json`) |

## Regras que valem para qualquer agente ou pessoa (resumo)

1. **Comece por `docs/START_HERE.md`** e pela ordem de leitura definida em
   `HATI_CONTEXT_INSTRUCOES.md`.
2. **Fontes de verdade, em ordem:** decisões humanas registradas (DT e matriz) > interpretação
   da auditoria > sugestões de IA. `AGENTS.md` detalha a precedência.
3. **Cada nova execução (job) exige confirmação humana** (DT-04). Mover cartão Kanban nunca
   inicia código.
4. **Git de escrita é humano:** nenhum agente executa commit, push, merge, rebase, reset ou
   publicação automaticamente.
5. **Fechar a janela do desktop não deverá encerrar o serviço e as execuções** (DT-05);
   viabilidade técnica será estudada na fase de arquitetura.
6. **Dados do HATI ficam fora dos repositórios dos projetos por padrão** (DT-07). Segredos
   nunca são versionados.

## Identidade

Estética alvo: escura, minimalista, técnica, densa e funcional (linha Omarchy). A referência
de marca é a runa do usuário, registrada em `docs/hati-context/assets/brand/RUNA_REFERENCIA_USUARIO.jpeg`.
**Não gerar lobo nem inventar runas** como se fossem a marca aprovada; tipografia, logo
definitivo e tokens visuais ainda não foram decididos.

## Idioma

Documentação e comunicação do projeto em **português do Brasil**; interface planejada em
pt-BR e EN desde a arquitetura (AX-16).

## Licença

Ainda não definida para o HATI (decisão humana futura). A auditoria de referência é factual;
código do Alethe (AGPL-3.0-or-later) não pode ser incorporado sem análise de licença.
