# HATI — 26 funcionalidades exclusivas

**Todos os HX são propostas originais de produto aprovadas, adiadas ou mantidas segundo o status.** Não foram extraídas como código do Alethe.

## HX-01 — Projects Hub — MELHORAR

**Definição:** Tela dedicada que reúne projetos com nome, caminho, stack, Git, tarefas e agentes ativos.

**Interpretação aprovada / refinamento:** Pesquisa, ordenação, fixados e acesso rápido ao último workspace; integração com WS-01 e Home.

## HX-02 — Cadastro inteligente de projetos — MELHORAR

**Definição:** Escolher pasta e detectar Git, stack, configurações de agentes e conflitos de cadastro.

**Interpretação aprovada / refinamento:** Pré-visualização de dados detectados, reconexão após mudança de pasta, nenhuma escrita automática.

## HX-03 — Painel de saúde do projeto — MANTER

**Definição:** Resumo de processos ativos, tarefas pendentes, branches, worktrees e verificações disponíveis.

**Interpretação aprovada / refinamento:** Indicadores agregados por projeto; distinguir estado confirmado e indisponível.

## HX-04 — Projetos com múltiplas raízes — ADIAR

**Definição:** Projeto lógico com múltiplos diretórios ou repositórios relacionados.

**Interpretação aprovada / refinamento:** ADIADO; decisão técnica sobre múltiplas raízes e permissões fica para fase futura.

## HX-05 — Perfil de execução por projeto — MELHORAR

**Definição:** Preferências de agentes, shells, modelos e permissões por projeto.

**Interpretação aprovada / refinamento:** Separar defaults globais e configurações locais; não modificar settings nativos silenciosamente.

## HX-06 — Kanban independente por projeto — MELHORAR

**Definição:** Quadro com colunas configuráveis, cartões e vínculos ao trabalho executado.

**Interpretação aprovada / refinamento:** Drag/drop, persistência, painel ao lado do terminal e única fonte de verdade das tarefas.

## HX-07 — Cartões de tarefas completos — MELHORAR

**Definição:** Título, descrição, critérios de aceite, etiquetas, prioridade, checklist, anexos ou referências.

**Interpretação aprovada / refinamento:** Criação simples com campos progressivos, não formulário extenso.

## HX-08 — Subtarefas e dependências — MANTER

**Definição:** Dividir tarefas e indicar dependências bloqueantes.

**Interpretação aprovada / refinamento:** Evitar ciclos e refletir bloqueios sem presumir conclusão.

## HX-09 — Filtros e múltiplas visualizações — MANTER

**Definição:** Busca/filtros por status, prioridade, agente e alternância Kanban/lista.

**Interpretação aprovada / refinamento:** As visualizações leem as mesmas tarefas, não duplicam dados.

## HX-10 — Workflow personalizável — MELHORAR

**Definição:** Colunas, regras de transição e limites de WIP configuráveis.

**Interpretação aprovada / refinamento:** Separar status do quadro de estado de job; conclusão do job não aprova tarefa automaticamente.

## HX-11 — Épicos, marcos e planejamento de releases — ADIAR

**Definição:** Agrupar tarefas em grandes entregas e versões.

**Interpretação aprovada / refinamento:** ADIADO; não bloquear o Kanban básico.

## HX-12 — Histórico auditável das tarefas — MANTER

**Definição:** Registrar mudanças de status, responsáveis, vínculos e decisões.

**Interpretação aprovada / refinamento:** Trilha cronológica para reconstituir execuções e revisões.

## HX-13 — Vínculo tarefa ↔ sessão ↔ execução — MELHORAR

**Definição:** Tarefa ligada a zero, uma ou várias sessões e cada tentativa registrada como job distinto.

**Interpretação aprovada / refinamento:** Não assumir 1:1; múltiplas tentativas, provedores e vinculações; eventos observáveis.

## HX-14 — Iniciar agente a partir da tarefa — MELHORAR

**Definição:** Abrir agente no diretório correto, passando contexto autorizado da tarefa.

**Interpretação aprovada / refinamento:** Prévia do contexto e confirmação humana de CADA nova execução; nunca spawn por mover cartão.

## HX-15 — Atualização de progresso em tempo real — MELHORAR

**Definição:** Mostrar execução ativa, espera, falha e conclusão no cartão.

**Interpretação aprovada / refinamento:** Eventos reais preferidos; distinguir inferência e confirmação; revisão humana mantém controle do status final.

## HX-16 — Execução paralela vinculada ao Kanban — MELHORAR

**Definição:** Workers simultâneos na mesma tarefa ou em tarefas diferentes, com worktrees e sessões.

**Interpretação aprovada / refinamento:** Controle de conflitos, isolamento, concorrência e vínculos rastreáveis; crescimento por fases.

## HX-17 — Resultados e evidências por tarefa — MELHORAR

**Definição:** Reunir diffs, testes, custos, logs e arquivos alterados.

**Interpretação aprovada / refinamento:** Fonte, data, agente e nível de confiabilidade; texto de agente não é prova de execução de teste.

## HX-18 — Continuidade e handoff contextual — MANTER

**Definição:** Transferir progresso, decisões e contexto entre agentes, ou retomar tarefas.

**Interpretação aprovada / refinamento:** Complementa AG-06 e AX-32; pacote revisável, sem vazamento de credenciais.

## HX-19 — Fila unificada de revisões — MELHORAR

**Definição:** Reunir tudo que aguarda decisão humana, por projeto/tipo/prioridade.

**Interpretação aprovada / refinamento:** Associar cada aprovação ao job/arquivos/permissão exatos; regras para mobile.

## HX-20 — Aprovar, rejeitar ou solicitar alterações — MANTER

**Definição:** Usuário aceita resultado, devolve para revisão ou solicita correção.

**Interpretação aprovada / refinamento:** Manter histórico de iterações; decisão humana explícita.

## HX-21 — Políticas de permissão por tarefa — MELHORAR

**Definição:** Escopo das operações permitidas/negadas por execução.

**Interpretação aprovada / refinamento:** Combinar regra do projeto, executor, sandbox e dispositivo; reconhecer limite do shell livre.

## HX-22 — Operações Git protegidas — MELHORAR

**Definição:** Commit, push, merge somente após comando humano, sem delegar autonomamente.

**Interpretação aprovada / refinamento:** Fronteira auditável no backend/execução isolada quando possível; Git mobile apenas revisão/aprovação.

## HX-23 — Auditoria de aprovações — MANTER

**Definição:** Registrar autor, ação, projeto, job, alterações e instante da aprovação.

**Interpretação aprovada / refinamento:** Adequada a desktop e dispositivos pareados; não guardar segredos em eventos.

## HX-24 — Central global de atividades — MELHORAR

**Definição:** Visão de tarefas, agentes, jobs e aprovações de todos os projetos.

**Interpretação aprovada / refinamento:** Filtros, notificações por prioridade e acesso rápido sem entrar em cada projeto.

## HX-25 — Kanban e aprovações pelo iPhone — MELHORAR

**Definição:** Interface touch para tarefas, respostas a agentes e aprovações.

**Interpretação aprovada / refinamento:** Mobile próprio e granular; Git protegido fica no desktop; conexão e serviço seguros.

## HX-26 — Orçamento de IA por tarefa ou projeto — ADIAR

**Definição:** Teto e alertas de consumo para execuções, separado do histórico real.

**Interpretação aprovada / refinamento:** ADIADO; registrar custos reais desde cedo, adicionar limites configuráveis quando fontes forem confiáveis.

## Integração transversal

A tarefa representa trabalho; um job representa uma tentativa de execução; sessões, terminais, agentes e worktrees são entidades relacionadas sem obrigatoriedade 1:1. Conclusão técnica do job NÃO aprova a tarefa.
