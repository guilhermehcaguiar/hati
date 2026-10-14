# Diretrizes transversais aprovadas — DT-01 a DT-10

Decisões de produto confirmadas pelo usuário e **não passíveis de alteração silenciosa por IA**.

## DT-01 — Estratégia de entregas

**Escolha definitiva:** Fases incrementais.

**Implicação:** Escopo total de 156 itens aprovados não significa primeira versão monolítica.

## DT-02 — Agentes da primeira versão

**Escolha definitiva:** OpenCode + Codex + Claude Code.

**Implicação:** Shell/PTY do sistema é componente técnico básico; agente principal OpenCode com Agent Suite.

## DT-03 — Profundidade inicial da orquestração

**Escolha definitiva:** Primeiro fluxo básico; ampliar em fases.

**Implicação:** Não abandonar recursos OR aprovados; planejar fases e evolução modular.

## DT-04 — Autorização para iniciar tarefas

**Escolha definitiva:** Confirmar início de cada execução.

**Implicação:** Nenhuma ação de mover cartão inicia job. Nova execução demanda autorização. Distinga tarefas internas da execução já autorizada.

## DT-05 — Fechamento da janela

**Escolha definitiva:** Manter serviço e execuções ativos.

**Implicação:** Fechar GUI não mata jobs; desligamento/hibernação/logout e crash precisam ser explicitamente tratados.

## DT-06 — Operações Git pelo iPhone

**Escolha definitiva:** Revisar e aprovar; Git protegido no desktop.

**Implicação:** No mobile, aprovação de revisões não aciona commit, push ou merge protegido.

## DT-07 — Localização dos dados do HATI

**Escolha definitiva:** Dados próprios fora dos repositórios por padrão.

**Implicação:** Não escrever AGENTS.md nos repositórios clonados ou de projetos alheios sem opt-in específico.

## DT-08 — Prioridade do sistema de plugins

**Escolha definitiva:** Projetar API cedo; catálogo em fase posterior.

**Implicação:** Core não depende de plugins de terceiros; isolamento projetado, maturidade faseada.

## DT-09 — Modelo de usuários

**Escolha definitiva:** Usuário local único + dispositivos pareados.

**Implicação:** Pareamento não substitui autenticação ou autorização granular.

## DT-10 — Prioridade do acesso pelo iPhone

**Escolha definitiva:** Logo após o núcleo desktop confiável.

**Implicação:** Não exigir cloud relay; host ligado e serviço ativo.

## Precedência

Quando uma diretriz DT e uma interpretação genérica da auditoria Alethe divergirem, prevalece a decisão HATI DT. Se surgir incompatibilidade técnica comprovada, documentar alternativa e solicitar decisão humana, sem reescrever a decisão antiga.
