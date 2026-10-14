# Arquitetura do HATI — requisitos e questões EM ABERTO

> **Nenhuma stack foi escolhida.** Este documento consolida os requisitos que qualquer
> arquitetura terá que satisfazer e as questões que o estudo arquitetural (Prompt 03) deve
> responder com comparação de alternativas, evidências e experimentos. Nada aqui é decisão.

## 1. Requisitos duros (derivados de DT e da matriz — não negociáveis para qualquer stack)

| # | Requisito | Origem |
|---|---|---|
| R1 | Serviço de execução (PTYs, jobs, sessões) **independente da janela**; fechar a GUI não mata nada; logout/desligamento/suspensão/crash tratados explicitamente | DT-05, TM-01, WS-03, AX-30 |
| R2 | Fronteira de **autorização no backend**: cada novo job exige confirmação humana; a UI nunca é a única barreira | DT-04, HX-21, OR-04, CROSS_REVIEW |
| R3 | Git de escrita fora da superfície automatizável; trilha auditável quando a ação existir na UI | HX-22, DT-06, GT-04 |
| R4 | Dados do HATI **fora** dos repositórios dos projetos; segredos nunca versionados nem exportados por acidente | DT-07, AX-20, MC-05 |
| R5 | Linux/Arch/Omarchy/Wayland é plataforma de primeira classe (CI e e2e reais); Windows é port posterior | PRODUCT_PRINCIPLES, TM-21 |
| R6 | OpenCode de primeira classe (além de PTY: API server, sessões, eventos, subagentes, jobs, quando viável); Codex e Claude Code com profundidade honesta e sem paridade fictícia | DT-02, AG-08, AG-13, OR-15 |
| R7 | Tarefa ≠ job: entidades distintas com vínculos n-para-n; conclusão de job não aprova tarefa | HX-13, HX-10, CROSS_REVIEW |
| R8 | Kanban como única fonte de tarefas; board de orquestração mostra jobs, nunca uma segunda fonte de tarefas | OR-01, HX-06, PL-07 |
| R9 | Modelo de eventos confiável e normalizado por agente para estado vivo de cartões, usage e alertas | AG-07, HX-15, US-* |
| R10 | Extensibilidade planejada cedo (API de plugins) com core independente; isolamento real considerado desde o início | DT-08, PL-01, PL-02 |
| R11 | Acesso remoto somente com transporte protegido + pareamento + revogação (RC-04 descartado) | RC-01..07, DT-09, DT-10 |
| R12 | i18n pt-BR/EN com chaves validadas por build | AX-16 |
| R13 | Usage local-first; scraping remoto apenas consentido, com frescor/erros visíveis | US-04, US-01/02, US-09 |
| R14 | Escrita segura em configs de agentes de terceiros: preview, validação, backup, atomicidade | MC-03/MC-04 |

## 2. Modelo conceitual de entidades (não é esquema de banco)

Projeto possui tarefas; tarefa pode ter vários jobs/tentativas; job referencia
agente/provedor, processo/PTY, sessão nativa da CLI e possivelmente worktree; logs, métricas
e diffs são evidências; revisão/autorização é entidade auditável; dispositivo pareado é
entidade com permissões granulares. Relacionamentos não são 1:1.

**Não equiparar:** fim do processo ≠ sucesso técnico; sucesso técnico ≠ critérios de aceite
cumpridos; critérios cumpridos ≠ aprovação humana; aprovação ≠ commit Git.

## 3. Questões abertas (a estudar no Prompt 03 — nenhuma decidida)

1. **Desktop/shell:** Tauri 2/WebKitGTK vs outras shells; Wayland/Hyprland; atualização;
   empacotamento Linux; caminho para Windows.
2. **Serviço persistente:** backend separado da GUI; systemd `--user` ou outra abordagem;
   PTYs e reanexação; comportamento em logout, suspensão, reinício e crash.
3. **Persistência:** SQLite vs JSON versionado vs híbrido; migrações; transações; backups
   consistentes (condiciona o Kanban).
4. **Eventos:** schema versionado; snapshots; reconciliação; replay; backpressure;
   sincronização desktop/mobile.
5. **Adaptadores de agentes:** protocolo OpenCode disponível; Codex app-server; Claude Code;
   capacidades distintas; versionamento e fallback seguro.
6. **Modelo de entidades:** formalização de projeto, tarefa, job, terminal, sessão de CLI,
   worktree, review, approval, evidência, dispositivo.
7. **Autorizações:** "job autorizado" vs tool calls internos; sandbox real; Git; aprovações
   remotas; auditoria.
8. **Plugins:** desenho de APIs; isolamento (WebView compartilhada declaradamente não
   basta — PL-02); aprovação de capacidades; atualização; armazenamento.
9. **Mobile:** PWA vs app; endpoint TLS/WSS; pareamento; túnel (ex.: Tailscale assistido —
   RC-05) vs proxy próprio; sessão remota e revogação.
10. **Usage:** fontes locais vs APIs oficiais; consentimento; cache; métricas comparáveis;
    custos aproximados.
11. **Graphify:** índice incremental; custos de RAM/CPU; privacidade; API para agentes.
12. **Qualidade:** e2e Linux real; regressão de PTYs; stress; segurança; CI; ADRs e riscos.
13. **Roadmap técnico:** confirmar/ajustar a proposta de `docs/ROADMAP.md` sem reclassificar
    IDs.
14. **Empacotamento e portabilidade:** instalação segura; migração entre PCs; backups;
    arquivos próprios fora de repositórios; compatibilidade com CLIs.

## 4. Requisitos de validação de qualquer alternativa (do pacote de contexto)

Para cada decisão arquitetural: **opções comparadas · evidências · limitações · trade-offs ·
riscos · experimento/PoC a realizar depois de aprovado · impacto Linux e Windows · impacto
em mobile · critérios mensuráveis de aceitação.**

## 5. Riscos prioritários herdados (do CROSS_REVIEW — alertas, não decisões)

- **Alto:** vida longa de processos no Linux (systemd/session) · Git com execução livre ·
  plugins não confiáveis · alterações concorrentes em worktree · acesso remoto e segredos ·
  estado duplicado/sincronização inconsistente.
- **Médio:** disponibilidade de APIs oficiais de uso dos provedores · protocolos que mudam ·
  custo do Graphify · multiplataforma · drift entre documentação e código.

## 6. Delimitações deste documento

- A auditoria do Alethe descreve **como o Alethe funcionava** (Tauri 2, PTYs, etc.) — é
  referência funcional, não escolha do HATI.
- Nada aqui autoriza implementação; o Prompt 03 produzirá recomendações comparativas e
  ADRs **propostos** para aprovação humana.
