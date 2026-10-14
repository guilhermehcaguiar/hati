# Revisão transversal aprovada para referência (análise, não arquitetura escolhida)

## Sobreposições a unificar semanticamente

| IDs | Proposta de responsabilidade única |
|---|---|
| WS-01, HX-01, HX-02, AX-01 | Projects Hub e Home sobre a mesma identidade de projeto |
| AX-03, PL-07, HX-06, HX-07 | Kanban nativo como única fonte de tarefas; extensões adicionam capacidades |
| OR-01, HX-06, HX-24 | Kanban (tarefas) separado do Orchestration Board (jobs) |
| TM-03, TM-04, AG-04, AG-05 | Histórico de PTY, sessão de provedor, retomada de conversa, reanexação não são a mesma coisa |
| AG-06, AX-32, HX-18 | Um modelo conceitual de continuidade/handoff |
| OR-03, OR-11, GT-05, HX-16 | Um serviço de gerenciamento de worktrees |
| OR-05, GT-02, HX-17 | Diffs e evidências compartilhados |
| US-05, OR-06, HX-17 | Métricas por sessão/job, apresentadas em painéis distintos |
| OR-04, HX-19–23, RC-03 | Política e trilha de autorização humanas comuns |
| AX-31, RC-07, HX-24 | Eventos e notificações compartilhados com clientes distintos |

**Nenhum ID é removido do histórico.** Uma implementação física poderá servir diversos IDs.

## Entidades de produto (modelo conceitual, não esquema de banco)

Projeto possui tarefas; tarefa pode ter vários jobs/tentativas; job referencia agente/provedor, processo/PTY, sessão nativa, possivelmente worktree; logs, métricas e diff são evidências; revisão/autorização é uma entidade auditável. Relacionamentos não são todos 1:1.

**Não equiparar:** fim do processo != sucesso técnico; sucesso técnico != critérios de aceite cumpridos; critérios cumpridos != aprovação humana; aprovação != commit Git.

## Dependências relevantes

1. Projeto com identidade persistente e dados locais → Kanban, históricos, backup e portabilidade.
2. Serviço de PTY/processos fora da janela → retomada, execução em segundo plano e mobile.
3. Adaptadores e eventos confiáveis → estado vivo dos cartões, Usage, orquestração e alertas.
4. Tarefa/job como entidades distintas → revisão, auditoria e continuidade.
5. Gerenciamento de Git/worktree → workers paralelos, diffs e integração de resultados.
6. Autorização / identidade / segredos → agentes, plugins, clientes remotos e Git protegido.
7. Transporte remoto criptografado e pareamento → mobile fora de casa.

## Tensões identificadas e delimitação

- `WS-16 DESCARTAR` vs `AG-16 MELHORAR`: descartar modo especial de PROJETO sandbox, manter execução isolada.
- `GT-08 ADIAR` vs `OR-11 MELHORAR`: trabalho básico de revisão/integração humana, sem Merge Center completo inicial.
- `LY-05, WS-21 ADIAR` vs `LY-04 MELHORAR`: persistir layout atual; histórico/múltiplos grids salvos adiados.
- `US-03 ADIAR` vs `OR-15 MELHORAR`: OpenCode worker é prioridade; Antigravity worker não impõe Antigravity Usage inicial.
- `RC-04 DESCARTAR` vs `RC-05 MELHORAR`: cliente remoto não deve expor HTTP/WS desprotegido. Detalhar camada de TLS/túnel.
- `PL-07 MELHORAR` vs `HX-06 MELHORAR`: plugin Todo não deve duplicar armazenamento de tarefas.
- `TM, AX-30, DT-05`: ocultar janela, encerrar serviço, suspender OS e logout são operações distintas.
- `AG-03, GT, HX-22`: agentes com shell irrestrito podem contornar proteção de UI; não prometer bloqueio absoluto sem isolamento real.
- `DT-04` vs delegação OR: cada **novo job** exige autorização; suboperações dentro de job já autorizado precisam de política de permissões delineada.

## Lacunas ainda abertas à análise técnica

Estados formalizados de tarefa/job; políticas de autorização desktop/remote; ownership de dados/consistência; crash-recovery; privacidade e retenção; diretórios movidos; distribuição Linux; testes E2E reais; backups e migração; plugin sandbox; telemetria sem segredos; uso de APIs oficiais de agentes; portabilidade para Windows; garantia/limites Git; transmissão remota e revogação.

## Riscos prioritários

Alto: vida longa dos processos no Linux + systemd/session; Git com execução livre; plugins não confiáveis; alterações concurrentes em worktree; acesso remoto e segredos; estado duplicado ou sincronização inconsistente.

Médio: disponibilidade de APIs oficiais de uso dos provedores; APIs/protocolos que mudam; custo de Graphify; multiplataforma; drift entre documentação e código.

## Nota legal

O relatório original usa simplificação de risco de AGPL. Não transformar essa formulação em regra jurídica categórica: analisar licença apenas se houver distribuição, incorporação de código/material protegido ou uso específico que acione deveres; desenvolvimento de capacidade equivalente, por si só, não impõe automaticamente licença AGPL.
