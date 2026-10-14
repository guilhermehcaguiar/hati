# Visão do produto — HATI ᚼᛅᛏᛁ

## O que é o HATI

Um workspace desktop **independente** para desenvolvimento assistido por múltiplos agentes de
IA, **local-first**, com prioridade Linux/Arch/Omarchy (Wayland/Hyprland) e Windows apenas
posteriormente. O HATI organiza o trabalho real de quem opera vários agentes em paralelo:
projetos, terminais, tarefas, execuções e evidências em um só lugar, com o humano no controle.

**Fluxo principal:** Projects → Kanban → Agentes/Sessões/Jobs/Worktrees → Evidências →
Revisão humana.

## Componentes centrais

- **Projects Hub** — cadastro de projetos por pasta local, com saúde do projeto e histórico.
- **Kanban por projeto** — única fonte de verdade das **tarefas**; cartões ligados a sessões,
  jobs e evidências; drag/drop sem efeitos colaterais de execução.
- **Terminais PTY reais** — sub-abas por agente/shell, scrollback persistido, retomada de
  sessões.
- **Agentes** — OpenCode (Agent Suite), Codex e Claude Code na primeira versão, com
  **OpenCode como cidadão de primeira classe**; aceitar diferenças de capacidades e não
  prometer paridade (DT-02).
- **Jobs × tarefas** — a tarefa é trabalho; o job é uma tentativa de execução. Conclusão
  técnica de um job **não** aprova a tarefa; aprovação é sempre humana.
- **Usage e limites** — histórico local-first (OpenCode primeiro), consentimento explícito
  por provedor, custos e cotas visíveis sem telemetria externa.
- **Git e worktrees** — trabalho paralelo isolado; **commit/push/merge são ações humanas**,
  nunca automáticas.
- **Revisão humana** — fila unificada de aprovações; o que aguarda decisão está visível.
- **iPhone (fase futura)** — Kanban, acompanhamento de agentes e aprovações pelo celular,
  com transporte seguro; Git protegido permanece no desktop (DT-06, DT-10).

## Diferenciação (por que o HATI existe)

1. **OpenCode de primeira classe** — a suite do usuário (Athena, Forge, Pixel, Atlas, Probe,
   Sentinel, Aegis) é o motor principal; o HATI gerencia o que os agentes compartilham
   (CLIs, sessões, MCPs, skills), sem duplicar a lógica interna deles.
2. **Kanban nativo** — inexistente na referência; tarefas ↔ agentes ↔ sessões ↔ limites.
3. **Linux/Omarchy-first** — plataforma primária de verdade, com estética dark, densa e
   minimalista (estilos Normal e Clean previstos — AX-15).
4. **Usage local-first** — dados locais e auditáveis antes de scraping de APIs remotas.
5. **Mobile seguro** — nenhum modo remoto desprotegido (RC-04 descartado); transporte
   protegido por decisão de arquitetura.
6. **Guardas Git por design** — o controle humano obrigatório é um requisito de produto, não
   uma preferência de configuração.

## Princípios aprovados

- Local-first; dados do HATI **fora** dos repositórios dos projetos por padrão (DT-07).
- Fases incrementais: escopo total (156 mantidos/melhorados) não é a primeira entrega (DT-01).
- Orquestração básica primeiro, ampliada por fases (DT-03) — sem duplicar orquestradores.
- Cada nova execução exige confirmação humana (DT-04).
- Fechar a janela não encerra o serviço (DT-05); os casos de logout/crash serão tratados
  explicitamente pela arquitetura.
- Usuário local único + dispositivos pareados (DT-09).
- Plugins: API projetada cedo, catálogo depois; core essencial nunca depende de plugins (DT-08).
- Interfaces pt-BR e EN desde a arquitetura (AX-16).

## O que o HATI NÃO é

- Não é extensão de IDE, não é IDE completa, não é fork ou rebranding do Alethe.
- Não é orquestrador autônomo: o HATI **assiste** o humano que dirige os agentes.
- Não substitui o OpenCode nem a Agent Suite — é a casa onde operam.
- Não tem cloud própria obrigatória; acesso remoto será por serviço no computador do
  usuário, com túnel/transporte seguro a decidir (DT-10).

## Identidade

Referência de marca: a runa do usuário (`docs/hati-context/assets/brand/RUNA_REFERENCIA_USUARIO.jpeg`).
Não gerar lobo, não inventar runas. Logo, tipografia e tokens visuais: ainda não decididos.

## Fronteira legal e autoria

A auditoria do Alethe é referência funcional/factual. O código do Alethe é AGPL-3.0-or-later:
**não copiar código, arte, texto protegido ou identidade**. Funcionalidade semelhante
desenvolvida de forma independente não impõe automaticamente o AGPL ao HATI; qualquer
incorporação de material de terceiros exige análise de licença específica (nota do
`docs/hati-context/architecture/CROSS_REVIEW.md`).
