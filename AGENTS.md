# AGENTS.md — Regras universais para agentes trabalhando no HATI

Aplica-se a qualquer IA (OpenCode, Codex, Claude Code ou outra) operando neste repositório,
em qualquer máquina. É complemento — não substituto — da documentação canônica em `docs/`.
Se este arquivo conflitar com uma decisão humana registrada, **prevalece a decisão humana**.

## 1. Ordem de leitura obrigatória antes de qualquer ação

1. `HATI_CONTEXT_INSTRUCOES.md` (raiz)
2. `docs/START_HERE.md`
3. `docs/PRODUCT_VISION.md` e `docs/SCOPE.md`
4. `docs/hati-context/governance/DIRECTIVES_DT.md` (DT-01 a DT-10 — imutáveis)
5. `docs/hati-context/scope/SCOPE_MATRIX.md` + `scope/HX_REQUIREMENTS.md` (intenção de cada ID)
6. `docs/CURRENT_STATUS.md` + `docs/HANDOFF.md` (estado real e continuidade)
7. Ao pesquisar comportamento do Alethe: `docs/hati-context/source/ALETHE_AUDIT_ORIGINAL.txt`

Não presuma que sua CLI carregou este arquivo automaticamente: verifique o suporte real da
sua versão e, em caso de dúvida, leia-o explicitamente.

## 2. Precedência de fontes de verdade

1. **Decisões humanas registradas** (DT-01 a DT-10; os 187 IDs da matriz com status e
   intenção; escolhas explícitas do usuário na conversa quando registradas em documentos).
2. **Interpretações da auditoria do Alethe** (referência factual — como o Alethe funcionava
   no commit auditado — não como o HATI deve ser implementado).
3. **Sugestões de IA** (planos, recomendações) — sempre propostas, nunca decisões.

Divergências entre esses níveis devem ser **preservadas e reportadas**, nunca resolvidas
silenciosamente. Se uma incompatibilidade técnica comprovada colidir com uma DT, documente a
alternativa e solicite decisão humana; não reescreva a DT.

## 3. Invariantes de produto (não negociáveis por IA)

- **DT-04:** cada **novo job/execução** exige confirmação humana. Mover cartão Kanban **não**
  inicia código. Sub-operações dentro de um job já autorizado seguem a política de permissões
  definida por tarefa (HX-21) — não reinterprete isso como licença para spawn automático.
- **DT-05:** fechar a janela GUI não encerra serviço/execuções. Logout, desligamento,
  suspensão e crash são casos a tratar explicitamente na arquitetura.
- **Git de escrita é humano:** nunca execute `git add/commit/push/merge/rebase/reset/restore/stash/checkout/switch/clean/tag`, nem equivalentes (scripts, APIs, GitHub CLI), nem altere
  metadados Git. Sugestões de mensagem de commit são apenas texto. Merge/apply de resultados
  de agentes é sempre revisado e confirmado por humano (HX-22, DT-06).
- **Shell irrestrito:** agentes com shell livre podem contornar proteções de UI. Não prometa
  bloqueio absoluto; qualquer garantia real exige isolamento/enforcement projetado
  (arquitetura, Prompt 03).
- **Dados (DT-07):** dados próprios do HATI ficam **fora** dos repositórios dos projetos por
  padrão. Nunca escreva `AGENTS.md`/`CLAUDE.md` ou qualquer artefato em repositórios alheios
  sem opt-in específico. Segredos, tokens e credenciais **nunca** são versionados.
- **Usuário único local + dispositivos pareados** (DT-09): pareamento não substitui
  autenticação/autorização granular.
- **Agent Suite existente:** Athena planeja; Forge executa sob autorização; Pixel, Atlas,
  Probe, Sentinel e Aegis atuam conforme as configurações do OpenCode do usuário. O HATI
  **integra-se** a essa suite — **não a recria** nem assume seus papéis.
- **Licenciamento:** não copie código, texto protegido, arte ou identidade do Alethe
  (AGPL-3.0-or-later). Capacidades semelhantes implementadas de forma independente não
  acionam automaticamente deveres de licença, mas incorporação de material exige análise.

## 4. Regras de conduta de sessão

- **Sem shell presumido:** sessões podem operar sem terminal. Use ferramentas nativas de
  leitura/escrita quando disponíveis; se não houver canal de gravação, entregue conteúdo
  completo na conversa com caminhos de destino claros. Não solicite liberação de shell se o
  usuário a tiver negado deliberadamente.
- **Uma autorização = uma etapa funcional coerente**, com escopo e critérios de aceitação.
  Identifique a etapa antes de começar; ao concluir, apresente resumo, arquivos, verificações
  executadas (com resultados reais), pendências e pausa.
- **Não afirme implementação sem inspeção.** Antes de declarar que algo existe no repositório,
  verifique. Aprovação de escopo não é implementação.
- **Não mude status/DT/prioridades** da matriz sem decisão humana explícita registrada.
- **Reporte lacunas; não invente.** Onde a informação for incompleta ou conflitante, registre
  como lacuna em `docs/CURRENT_STATUS.md` ou na conversa.
- **Segredos e privacidade:** não exponha credenciais, tokens, paths pessoais absolutos de
  máquinas específicas ou informações privadas na documentação. Use caminhos relativos.
- **Handoff obrigatório:** ao encerrar a sessão, preencha `docs/HANDOFF.md` (ou entregue o
  conteúdo preenchido na conversa, se não puder gravar), conforme o template
  `docs/hati-context/workflow/SESSION_HANDOFF_TEMPLATE.md`.

## 5. Implementação (quando autorizada)

- A implementação de código **não está autorizada nesta fase** (fundação documental).
  A arquitetura será decidida no Prompt 03; etapas de implementação virão depois, cada uma
  com autorização explícita.
- Quando a implementação começar: trabalhe em etapas pequenas, coerentes e validáveis;
  registre decisões técnicas como ADRs em `docs/decisions/` (convenção no README local);
  Linux/Arch/Omarchy/Wayland é a plataforma de primeira classe; Windows é alvo posterior.
