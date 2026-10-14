# Matriz de decisões do HATI — 187 IDs

> Fonte de verdade de **status**: seleções explícitas do usuário nesta conversa. Fontes de **descrição**: auditoria Athena do Alethe para WS–RC e propostas do HATI para HX. A coluna "Intenção HATI" registra o refinamento de produto discutido, não uma solução de implementação.

**Definições:** MANTER = preservar capacidade; MELHORAR = aprovar com refinamento; ADIAR = não incluído nas entregas iniciais; DESCARTAR = excluído do escopo conforme descrição específica do ID. **Nenhum status é compromisso de MVP.**

| Tipo | MANTER | MELHORAR | ADIAR | DESCARTAR | Total |
|---|---:|---:|---:|---:|---:|
| Workspace e Projects | 11 | 6 | 3 | 1 | 21 |
| Layouts | 4 | 1 | 1 | 0 | 6 |
| Terminais e PTYs | 10 | 11 | 4 | 1 | 26 |
| Agentes de Programação | 3 | 10 | 1 | 2 | 16 |
| Usage e Limites | 2 | 7 | 2 | 0 | 11 |
| Orquestração | 1 | 13 | 1 | 1 | 16 |
| MCPs e Skills | 1 | 8 | 0 | 0 | 9 |
| Plugins | 1 | 7 | 0 | 0 | 8 |
| Git | 1 | 7 | 1 | 0 | 9 |
| Recursos Complementares | 3 | 20 | 9 | 0 | 32 |
| Acesso Remoto e Mobile | 0 | 6 | 0 | 1 | 7 |
| Exclusivas do HATI | 7 | 16 | 3 | 0 | 26 |
| **TOTAL** | **44** | **112** | **25** | **6** | **187** |

## Catálogo detalhado

### WS — Workspace e Projects (21 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| WS-01 | Cadastro de projeto por pasta local | MELHORAR | Projetos registrados por pasta na aba Projects; gerenciar caminho, detectar Git e permitir reconectar diretórios movidos. |
| WS-02 | Grupos e subgrupos aninhados | MANTER | Grupos e subgrupos hierárquicos, com organização independente do diretório real. |
| WS-03 | Containers: projeto aberto vira moldura; fechar não mata processos | MELHORAR | Distinguir ocultar/fechar o container, suspender, encerrar e manter sessões; ciclo de vida explícito do serviço. |
| WS-04 | Múltiplos projetos simultâneos em abas de workspace | MANTER | Múltiplos projetos simultaneamente; navegar sem matar sessões. |
| WS-05 | Histórico de navegação do workspace (Alt+←/→) | MANTER | Histórico de navegação entre projetos e workspaces. |
| WS-06 | Modo flat (combinar panes de vários projetos) | ADIAR | Modo flat agrupando painéis de projetos distintos — não faz parte da fase inicial. |
| WS-07 | Fullscreen de container | MANTER | Modo fullscreen de container preservado. |
| WS-08 | Colapsar containers em headers compactos | MANTER | Recolher containers sem confundir invisibilidade com encerramento. |
| WS-09 | Drag & drop de projetos/grupos/terminais | MELHORAR | Drag-and-drop entre projetos/grupos/painéis com alternativas por teclado e acessibilidade. |
| WS-10 | Cores/ícones por projeto e grupo | MANTER | Cores e ícones personalizados por projeto/grupo. |
| WS-11 | Arquivar/ocultar projetos | MANTER | Arquivamento e ocultação reversíveis. |
| WS-12 | Recentes | MANTER | Projetos recentes e recuperação de navegação. |
| WS-13 | Clone de repo GitHub gera briefing p/ agentes | MELHORAR | Clonagem Git opcional e explícita; NÃO escrever AGENTS.md/CLAUDE.md automaticamente nos repositórios clonados. |
| WS-14 | Detecção de stack do projeto | MANTER | Detecção não destrutiva da stack do projeto. |
| WS-15 | Perfis locais isolados | ADIAR | Perfis locais independentes de usuário adiados; escopo inicial é usuário único. |
| WS-16 | Modo "agentSandbox" de projeto | DESCARTAR | Descartar apenas o modo especial "agentSandbox" como categoria de PROJETO; não descartar isolamento de workers. |
| WS-17 | Sidebar em árvore Home/grupos/projetos/terminais | MELHORAR | Sidebar em árvore integrada a Projects, Kanban, sessões, agentes e indicadores de execução. |
| WS-18 | Menus de contexto (grupo/projeto/terminal) | MANTER | Menus de contexto por entidade, com ações perigosas confirmadas. |
| WS-19 | Comando alethe <path> abre projeto; single-instance com handoff | MANTER | CLI `hati <caminho>` e integração single-instance, respeitando o sistema operacional. |
| WS-20 | Suspender grupo (mata PTYs, fecha containers, reativável) | MELHORAR | Diferenciar ocultar, pausar quando possível, suspender e terminar, com estado/avisos explícitos. |
| WS-21 | Grids múltiplos por projeto + layout por grupo/workspace | ADIAR | Múltiplos grids armazenados por projeto/grupo adiados; Custom Grid único continua aprovado. |

### LY — Layouts (6 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| LY-01 | Layout Auto (1=full, 2=lado a lado, 3+=grid) | MANTER | Auto Layout para uma, duas ou mais panes. |
| LY-02 | Layout Spotlight (primário + pilha lateral) | MANTER | Spotlight com pane principal e secundárias empilhadas. |
| LY-03 | Layout Sidebar (lista estreita + pane ativa) | MANTER | Sidebar Layout com pane ativa principal. |
| LY-04 | Custom Grid: editor visual com colSpan/rowSpan, frações redimensionáveis, swap por drag | MELHORAR | Custom Grid ajustável com painéis de terminais, Kanban, Agents, Usage e Git; spans, tamanho e drag/drop. |
| LY-05 | Histórico de layouts salvos por nível (projeto/grupo/workspace) | ADIAR | Histórico de layouts salvos adiado; a configuração ATUAL deve persistir normalmente. |
| LY-06 | Layout Designer modal (editor dedicado, lazy) | MANTER | Layout Designer visual dedicado integrado ao Custom Grid. |

### TM — Terminais e PTYs (26 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| TM-01 | PTYs reais multi-OS | MELHORAR | PTYs reais, Linux/Omarchy primeiro; investigar serviço independente da janela e compatibilidade futura Windows. |
| TM-02 | Sub-abas: cada aba tem agente/cwd/PTY próprios | MELHORAR | Subabas por agente/shell/cwd com sessão própria; associação opcional ao projeto e à tarefa. |
| TM-03 | Scrollback persistido por PTY | MELHORAR | Scrollback durável e recuperável; retenção, integridade, desempenho e proteção de dados sensíveis. |
| TM-04 | Attach com tail 512KB alinhado a UTF-8 | MANTER | Reanexar terminal com tail UTF-8 íntegro. |
| TM-05 | Streaming em lotes (16ms/64KB) + throttling de atividade em pane oculta (450ms) | MANTER | Agrupar streaming e reduzir atualizações de panes ocultas, sem perder dados. |
| TM-06 | Resize automático + ResizeObserver + fit | MANTER | Resize automático junto aos layouts e em janela remota. |
| TM-07 | Restart de terminal (mantém id) | MELHORAR | Reiniciar preservando organização, confirmando interrupções e preservando histórico anterior. |
| TM-08 | Desabilitar terminal/projeto; suspender grupo | MELHORAR | Operações separadas: ocultar, pausar, suspender, encerrar; integrar a WS-20. |
| TM-09 | Kill de árvore de processos multi-OS | MELHORAR | Encerrar grupo de processos de maneira segura e graciosa antes de forçar; proteger processos externos. |
| TM-10 | Garantia de morte de descendentes (Job Object Win) | ADIAR | Windows Job Objects ficam para fase Windows. |
| TM-11 | Ceifa de órfãos entre sessões | MELHORAR | Reconciliar processos órfãos por identidade comprovada; nunca matar apenas por nome/PID ambíguo. |
| TM-12 | Gate de memória no spawn (400MB/45s) | MELHORAR | Controle configurável de memória antes de spawn, com aviso e opção humana; não adotar limite fixo como dogma. |
| TM-13 | Prioridade de processo (Windows) | ADIAR | Prioridade de processo Win32 adiada. |
| TM-14 | Pausar/retomar leitura de PTY | MANTER | Pausar e retomar leitura sem corromper output nem provocar deadlock. |
| TM-15 | Histórico de prompts por terminal | MANTER | Histórico de comandos/prompts com retenção e proteção de informações privadas. |
| TM-16 | Busca dentro do terminal | MANTER | Pesquisa dentro do terminal e scrollback. |
| TM-17 | Overlay de reinício quando processo morre | MANTER | Overlay de saída que distingue fim, falha, suspensão e cancelamento; restart explícito. |
| TM-18 | PTYs one-shot com watcher de exit | MANTER | Watchers para comandos temporários; relacionar a job quando aplicável. |
| TM-19 | WSL como shell de primeira classe | ADIAR | WSL como shell fica para suporte Windows. |
| TM-20 | Fonte de terminal configurável + heap profile Node | MELHORAR | Configurar Nerd Fonts e parâmetros seguros de runtime por provedor/perfil; sem injeção arbitrária de heap. |
| TM-21 | xterm WebGL com budget 4 + fallback canvas | MANTER | WebGL com fallback Canvas e testes reais Wayland/Hyprland. |
| TM-22 | Ghostty nativo (superfície NSView sobre a WebView) | DESCARTAR | Descartar integração Ghostty nativa macOS; não descarta outras opções futuras de renderização. |
| TM-23 | Copy/paste nativo + comandos de clipboard | MANTER | Clipboard e atalhos nativos Linux com cuidado a colagens perigosas. |
| TM-24 | RAM por processo + snapshot de runtime | MELHORAR | Snapshot de RAM/runtime por processo com integração a Usage e Resource Manager. |
| TM-25 | Crash watch + toast de crash com órfãos ceifados | MELHORAR | Crash Watch com alerta, diagnóstico sanitizado e recuperação controlada. |
| TM-26 | Watcher de processo filho p/ WSL (master nunca dá EOF) | ADIAR | Watcher especial WSL adiado junto com TM-19. |

### AG — Agentes de Programação (16 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| AG-01 | 14 agentes built-in + custom + plugins | MELHORAR | OpenCode, Codex e Claude Code na primeira versão; adaptadores extensíveis e CLIs customizadas; não exigir paridade fictícia. |
| AG-02 | Detecção de instalação de CLIs | MELHORAR | Descobrir PATH/instalação sem alterar sistema; instalação/update com consentimento; prioridade Linux. |
| AG-03 | Flags "unrestricted" por agente | MELHORAR | Permissões granulares por agente/execução; não usar bypass de forma automática; avaliar isolamento real. |
| AG-04 | Session resume | MELHORAR | Separar retomar conversa do agente, reconectar PTY e restaurar UI; registrar capacidades por provedor. |
| AG-05 | Recent Chats: listar conversas do cwd e reabrir | MELHORAR | Histórico por projeto, modelo, provedor e sessão, sem migrar ou sobrescrever históricos nativos. |
| AG-06 | Handoff Claude↔Codex com pacote de contexto redigido | MANTER | Handoff entre agentes APROVADO (MANTER), com pacote revisável e proteção de segredos; redação automática best-effort não é garantia. |
| AG-07 | Hooks de eventos dos agentes → app | MELHORAR | Camada de eventos normalizada por agente; integrações oficiais preferidas; sem modificar configs globais silenciosamente. |
| AG-08 | Plugin global do OpenCode (working/idle) | MELHORAR | OpenCode integração de primeira classe; avaliar API server, sessões, eventos, subagentes e jobs além do PTY. |
| AG-09 | OpenCode GSD sync | DESCARTAR | Descartar integração específica OpenCode GSD Sync, incluindo autocommits; não descarta planejamento nem Kanban. |
| AG-10 | Modelos por provedor + discovery | MELHORAR | Poder descobrir modelos do provedor e exibir indisponibilidade/frescor dos dados; preservar config nativa. |
| AG-11 | Runtime profiles (full/lean/diagnostic) | MANTER | Perfis de runtime full/lean/diagnostic conforme suporte de cada CLI. |
| AG-12 | 9router (proxy local multi-provedor) | ADIAR | 9router/proxy multiprovedor adiado. |
| AG-13 | Codex app-server bidirecional | MELHORAR | Usar Codex app-server quando viável para bidirecionalidade, jobs, eventos e aprovações. |
| AG-14 | Ícones custom de agente | MANTER | Ícones dos agentes customizáveis. |
| AG-15 | Agent Canvas (POC) | DESCARTAR | Descartar POC abandonada Agent Canvas; não descartar painel original de orquestração. |
| AG-16 | Agent Sandbox (workers em sandbox) | MELHORAR | Isolamento/sandbox de EXECUÇÕES, sem reintroduzir modo de projeto WS-16; autorização explícita. |

### US — Usage e Limites (11 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| US-01 | Claude usage cards | MELHORAR | Claude Code: janelas de cotas/reset quando autorizadas e por fontes sustentáveis, sem depender só de APIs internas. |
| US-02 | Codex usage | MELHORAR | Codex: dados realmente disponíveis, distinguindo assinatura ChatGPT, API e créditos. |
| US-03 | Antigravity usage | ADIAR | Antigravity Usage adiado; evitar fontes internas sem garantia/autorização. |
| US-04 | OpenCode usage (local, robusto) | MELHORAR | OpenCode local-first, histórico por projeto/agente/sessão/job; não confundir custo local com cota remota. |
| US-05 | Token HUD por sessão | MELHORAR | Token HUD por sessão: entrada, saída, cache, contexto/custo quando disponíveis, com fontes e estimativas explícitas. |
| US-06 | Indicadores na topbar com poll 5min + cache 60s compartilhado | MELHORAR | Topbar compacta e customizável; cache compartilhado por provedor, última atualização e erros visíveis. |
| US-07 | Notificação de reset de limite | MANTER | Alertas de previsão de renovação de cota com opção de desativar. |
| US-08 | Modal de reset credit (Codex) | ADIAR | Modal Reset Credit Codex adiado até verificar API, sem agir sobre conta sem consentimento. |
| US-09 | Consentimento explícito por provedor (usageAccess) | MANTER | Consentimento separado por provedor, desativado por padrão; revogável. |
| US-10 | Modal AI Usage com N cards | MELHORAR | Painel Usage completo filtrável por provedor/modelo/período/projeto, estados indisponíveis. |
| US-11 | Activity stats local (widgets Home) | MELHORAR | Activity Stats locais por projeto/agente com retenção, sem telemetria externa ou rankings de produtividade inventados. |

### OR — Orquestração (16 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| OR-01 | Orchestration Board (off por default) | MELHORAR | Orchestration Board como visão de JOBS sincronizada com Kanban (que guarda TAREFAS); evitar segunda fonte de tarefas. |
| OR-02 | Workers Claude+Codex com steer/cancel/diff/aprovação | MELHORAR | Workers multi-provedor com status/steer/cancel/approval quando suportados; OpenCode como primeiro nível. |
| OR-03 | Worktree isolado por worker (isolate) | MELHORAR | Worktrees isoladas por worker, integradas a tarefa, com limpeza sem perder alterações pendentes. |
| OR-04 | Aprovação de sandbox ("asks before leaving sandbox") | MELHORAR | Gates de permissão/sandbox com confirmação, trilha auditável e sem bypass implícito. |
| OR-05 | Diff inline no card do worker | MELHORAR | Diffs por worker e tarefa; revisão humana com origem das mudanças. |
| OR-06 | Status/custo/tokens por card | MELHORAR | Estado/tokens/custo por job usando mesmo provedor de métricas de Usage. |
| OR-07 | Roles com fallback por cota | MELHORAR | Perfis de papéis e fallback por cota, com confirmação se custos, permissões ou provedores mudarem. |
| OR-08 | Routing rules por kind/effort/quota-gate | MELHORAR | Roteamento explicável, configurável e manual como alternativa; não iniciar novos jobs sem confirmação humana. |
| OR-09 | Consciência de cota cross-vendor (fitness) | MELHORAR | Consciência de cotas com fontes e comparabilidade explícitas (cotas heterogêneas não são números equivalentes). |
| OR-10 | Routing trace no canvas (chosen/ignored) | MANTER | Routing Trace preserva razão da escolha e das alternativas ignoradas. |
| OR-11 | Apply worktree (caminho limpo) | MELHORAR | Preparar/inspecionar integração de worktrees, mas git commit/push/merge requer ação humana e fronteira de autorização. |
| OR-12 | Janela destacada do board | ADIAR | Janela destacada de orquestração adiada. |
| OR-13 | Skill alethe-orchestrate bundada | MELHORAR | Skills de orquestração compatíveis com Agent Suite, sem duplicar lógica nem alterar configs globais. |
| OR-14 | GSD Sync (2ª orquestração) | DESCARTAR | Descartar segundo orquestrador/GSD Sync redundante. |
| OR-15 | Antigravity/OpenCode como worker | MELHORAR | OpenCode worker de primeira classe; Antigravity worker condicionado a viabilidade técnica futura, sem antecipar US-03. |
| OR-16 | Personas planner/brainstorm/executor | MELHORAR | Papéis configuráveis reusando Athena, Forge, Pixel, Atlas, Probe, Sentinel e Aegis; não impor personas duplicadas. |

### MC — MCPs e Skills (9 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| MC-01 | Scan de todos os MCPs da máquina por 6 agentes | MELHORAR | Inventário read-only por agente/origem/escopo, OpenCode prioritário. |
| MC-02 | Escopos global/projeto (+ .mcp.json do repo) | MELHORAR | MCPs global/projeto/usuário com precedência e conflitos visíveis. |
| MC-03 | Upsert/remove/enable com self-check + backup + atômico | MELHORAR | Edição/upsert/remover com pré-visualização diff, validação, backup, escrita atômica e confirmação. |
| MC-04 | Copiar servidor entre agentes (sync backend-only) | MELHORAR | Sincronizar configs compatíveis entre CLIs com revisão, escopo explícito e tratamento de segredos no backend. |
| MC-05 | Reveal de env sob clique único | MELHORAR | Ocultar segredos por padrão; revelar apenas individual/temporariamente e restringir remotamente. |
| MC-06 | Busca no registry oficial | MANTER | Pesquisa em registro público opcional; listado não significa confiável. |
| MC-07 | Health check real por CLI (claude/codex/opencode) | MELHORAR | Health checks diagnósticos categorizando erro de config/auth/rede; execução/conexão externa autorizada. |
| MC-08 | Skills: scan/detail/uninstall por agente | MELHORAR | Gerenciar skills e stores compartilhados evitando remoção inadvertida de conteúdo de outro agente. |
| MC-09 | Skills bundadas "From Alethe" com install/update e proteção de conflito | MELHORAR | Skills originais do HATI versionadas e instaladas com opt-in; não copiar código/skills do Alethe. |

### PL — Plugins (8 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| PL-01 | Tudo é plugin (features oficiais incl.) | MELHORAR | Arquitetura extensível sem transformar serviços críticos em plugins opcionais; Projects/PTYs/segurança no core. |
| PL-02 | Capacidades + comandos proibidos | MELHORAR | Enforcement de permissões no backend e isolamento de código não confiável; WebView compartilhada NÃO basta. |
| PL-03 | Catálogo com SHA-256 + install 1-click | MELHORAR | Catálogo futuro com integridade, origem e review de permissões; hash não comprova segurança do código. |
| PL-04 | Import local limitado | MANTER | Permitir importar plugins locais desabilitados por padrão com revisão de risco e consentimento. |
| PL-05 | Storage por plugin/por perfil | MELHORAR | Armazenamento segregado/quotas/migrações sem leitura direta de bancos internos. |
| PL-06 | Contribuições: views/commands/themes/panes/modals/agentProvider | MELHORAR | Extension API para panels, commands, menus, providers e temas com capacidades explícitas. |
| PL-07 | Oficiais: Todo List, Git Control, Theme Pack | MELHORAR | Equivalentes oficiais sem duplicar Todo/Kanban; ações Git protegidas sempre humanas. |
| PL-08 | Temas como plugin (tokens sanitizados) | MELHORAR | Temas instaláveis, visualização antes de aplicar, tokens sanitizados, contraste; sem executar código arbitrário. |

### GT — Git (9 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| GT-01 | Painel Git por projeto | MELHORAR | Painel Git ligado a Projects, Kanban e agentes; status/stage e operações explícitas; commits/pushes humanos. |
| GT-02 | Diffs em pane dedicada | MELHORAR | Diff unificado por tarefa, sessão, worker e worktree; inline e lado a lado; destacar conflitos. |
| GT-03 | Log graph + inspeção de commit | MANTER | Histórico e grafo de commits por repositório. |
| GT-04 | Branch/cherry-pick/revert/reset | MELHORAR | Branch, cherry-pick, revert e reset com análise de risco/confirmar e sem automação protegida. |
| GT-05 | Worktrees de primeira classe | MELHORAR | Worktree Manager em primeira classe com ciclo de vida e não descarte de alterações sem revisão. |
| GT-06 | PR Review via gh (sem token próprio) | MELHORAR | PRs por gh ou integração autorizada, revisão IA read-only, CI e aprovações; sem comentários ou merges automáticos. |
| GT-07 | Aba Open PRs → todo com link | MELHORAR | Links de múltiplos PRs às tarefas Kanban com status quando autorizados. |
| GT-08 | Merge Center (analyze/prepare/validate/finalize/abort/rebase) | ADIAR | Merge Center complexo adiado; análise e alertas de conflito simples continuam possíveis. |
| GT-09 | Agente efêmero de conflito + sessões Review/Test | MELHORAR | Sessões efêmeras de revisão/testes/conflito com especialistas existentes, evidências e limpeza controlada. |

### AX — Recursos Complementares (32 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| AX-01 | Home (saudação, recentes, ações rápidas, widgets usage/Spotify) | MELHORAR | Home operacional: recentes, tarefas, sessões, aprovações e Usage, UI compacta. |
| AX-02 | Pomodoro integrado (25/5/15) | ADIAR | Pomodoro adiado. |
| AX-03 | Todos com tags (+ PRs) | MELHORAR | Todo List integrado ao Kanban, não criar base de dados de tarefas separada. |
| AX-04 | Pane Markdown + comentários ancorados | MELHORAR | Markdown/Mermaid com comentários referenciáveis em documentos de projeto; preservar âncoras. |
| AX-05 | Explorador de arquivos | MELHORAR | File explorer leve com pré-visualização, sem transformar HATI em IDE. |
| AX-06 | Panes de imagem e vídeo | MANTER | Painéis para imagens e vídeos dentro do workspace. |
| AX-07 | Navegador embutido | ADIAR | Navegador embutido e CDP adiados. |
| AX-08 | Graphify: grafo de código servido como MCP | MELHORAR | Graphify original e incremental, útil aos agentes, com limites de RAM e isolado por projeto. |
| AX-09 | AI Memory bridge | ADIAR | AI Memory Bridge de terceiros adiada; preservar histórico/handoff básico já aprovado. |
| AX-10 | Ditado por voz 100% local | ADIAR | Ditado por voz local adiado. |
| AX-11 | Voice commands (barra) | ADIAR | Comandos por voz adiados; nunca autorizar ações perigosas via voz sem confirmação. |
| AX-12 | Spotify Now Playing | ADIAR | Spotify Now Playing adiado. |
| AX-13 | Discord Rich Presence | ADIAR | Discord Rich Presence adiado; futuro opt-in com privacidade. |
| AX-14 | 16 temas built-in + app icons + terminal themes | MELHORAR | Temas e ícones originais dark/Clean/Omarchy, paleta uniforme com terminais e identidade runa específica. |
| AX-15 | Estilos visuais Normal/Clean | MANTER | Normal e Clean com densidade previsível. |
| AX-16 | i18n EN/pt-BR/zh-CN com enforcement por tipos | MELHORAR | i18n pt-BR e EN desde a arquitetura, chaves validadas. |
| AX-17 | Jump/find modal (Ctrl+P) + command palette com comandos de plugins | MELHORAR | Busca/Command Palette global para Projects, Kanban, sessões, comandos; ações sujeitas a permissões. |
| AX-18 | Atalhos completos | MANTER | Atalhos de teclado documentados, sem conflito com terminal. |
| AX-19 | Zoom da WebView + opacidade de janela (Win) | MELHORAR | Zoom acessível Linux-first; opacidade sujeita às capacidades do sistema. |
| AX-20 | Backup export/import zip (anti-zip-slip) | MELHORAR | Backup versionado/restauração validada e seletiva, sem exportar segredos por acidente. |
| AX-21 | GitHub Gist Sync (manual, secret gist) | ADIAR | GitHub Gist Sync adiado. |
| AX-22 | Cloud sync 1ª parte (device flow) | ADIAR | Cloud Sync adiado; manter local-first. |
| AX-23 | Auto-update com assinatura | MELHORAR | Atualizações assinadas, opt-in/política explícita e changelog; nunca executar atualização silenciosa. |
| AX-24 | What's New + Onboarding/Welcome + setup walkthrough | MELHORAR | Onboarding enxuto detecta ambiente, CLIs, pastas, sem writes globais automáticos. |
| AX-25 | Diagnósticos: spawn.log, export logs, reset/wipe | MELHORAR | Diagnóstico de subprocessos, erros e logs sanitizados, com reset seletivo. |
| AX-26 | Telemetria local jsonl (cap 2MiB per FEATURES.md; PRIVACY diz sem cap — divergência entre docs) | MELHORAR | Telemetria somente local, retenção e nenhum envio sem consentimento. |
| AX-27 | Terminal Inspector | MELHORAR | Terminal Inspector (PID, árvore, cwd, recursos) evitando expor secrets em args/env. |
| AX-28 | Right sidebar: MCP/Skills/Agents panel/PRs/Merge/Now Playing | MELHORAR | Sidebar direita contextual, recolhível, redimensionável e extensível por plugins seguros. |
| AX-29 | Resource Manager (política smart-lru opt-in, parking, orçamento) | MELHORAR | Resource Manager com dados confiáveis e ações opt-in, evitando matar trabalho ativo. |
| AX-30 | Confirmação de fechamento | MELHORAR | Fechar a janela NÃO encerra service/jobs; confirmação só para encerrar/suspender/interromper quando aplicável. |
| AX-31 | Notificações in-app (toasts) + nativas | MELHORAR | Notificações desktop e internas por agente/kanban/usage/remote, com controle de ruído e privacidade. |
| AX-32 | Memória de próximos passos: HANDOFF/docs de sessão | MELHORAR | Continuidade por projeto/tarefa, handoffs estruturados e próximos passos, sem duplicar bancos internos dos agentes. |

### RC — Acesso Remoto e Mobile (7 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| RC-01 | Remote Control LAN (off por default) | MELHORAR | Pareamento, sessão autenticada e transporte seguro para iPhone; PWA responsiva, reconexão e serviço desktop separado da janela. |
| RC-02 | Modelo read-mostly | MELHORAR | Expandir read-mostly para Kanban, responder agentes, revisar e aprovar; não expor shell livre por padrão. |
| RC-03 | Opt-ins escalonados de input | MELHORAR | Permissões granulares por dispositivo/ação (Kanban, sessões, approvals, shell, Git). |
| RC-04 | Transporte HTTP/WS sem TLS — "trusted LAN only" | DESCARTAR | Descartar modo de acesso remoto desprotegido HTTP/WS sem proteção de transporte e autenticação. |
| RC-05 | Alcance via Tailscale | MELHORAR | Tailscale assistido, TLS quando viável, diagnóstico; não obrigar Tailscale ao uso local. |
| RC-06 | Gestão de dispositivos | MELHORAR | Dispositivos pareados, revogação, expiração, atividade, limites de tentativas e auditoria. |
| RC-07 | Follow agents: acompanhar e responder agentes do celular | MELHORAR | Follow Agents com eventos, scrollback, notificações e aprovações permitidas, vinculado a tarefas. |

### HX — Exclusivas do HATI (26 itens)

| ID | Funcionalidade | Status | Intenção específica para HATI |
|---|---|---|---|
| HX-01 | Projects Hub | MELHORAR | Pesquisa, ordenação, fixados e acesso rápido ao último workspace; integração com WS-01 e Home. |
| HX-02 | Cadastro inteligente de projetos | MELHORAR | Pré-visualização de dados detectados, reconexão após mudança de pasta, nenhuma escrita automática. |
| HX-03 | Painel de saúde do projeto | MANTER | Indicadores agregados por projeto; distinguir estado confirmado e indisponível. |
| HX-04 | Projetos com múltiplas raízes | ADIAR | ADIADO; decisão técnica sobre múltiplas raízes e permissões fica para fase futura. |
| HX-05 | Perfil de execução por projeto | MELHORAR | Separar defaults globais e configurações locais; não modificar settings nativos silenciosamente. |
| HX-06 | Kanban independente por projeto | MELHORAR | Drag/drop, persistência, painel ao lado do terminal e única fonte de verdade das tarefas. |
| HX-07 | Cartões de tarefas completos | MELHORAR | Criação simples com campos progressivos, não formulário extenso. |
| HX-08 | Subtarefas e dependências | MANTER | Evitar ciclos e refletir bloqueios sem presumir conclusão. |
| HX-09 | Filtros e múltiplas visualizações | MANTER | As visualizações leem as mesmas tarefas, não duplicam dados. |
| HX-10 | Workflow personalizável | MELHORAR | Separar status do quadro de estado de job; conclusão do job não aprova tarefa automaticamente. |
| HX-11 | Épicos, marcos e planejamento de releases | ADIAR | ADIADO; não bloquear o Kanban básico. |
| HX-12 | Histórico auditável das tarefas | MANTER | Trilha cronológica para reconstituir execuções e revisões. |
| HX-13 | Vínculo tarefa ↔ sessão ↔ execução | MELHORAR | Não assumir 1:1; múltiplas tentativas, provedores e vinculações; eventos observáveis. |
| HX-14 | Iniciar agente a partir da tarefa | MELHORAR | Prévia do contexto e confirmação humana de CADA nova execução; nunca spawn por mover cartão. |
| HX-15 | Atualização de progresso em tempo real | MELHORAR | Eventos reais preferidos; distinguir inferência e confirmação; revisão humana mantém controle do status final. |
| HX-16 | Execução paralela vinculada ao Kanban | MELHORAR | Controle de conflitos, isolamento, concorrência e vínculos rastreáveis; crescimento por fases. |
| HX-17 | Resultados e evidências por tarefa | MELHORAR | Fonte, data, agente e nível de confiabilidade; texto de agente não é prova de execução de teste. |
| HX-18 | Continuidade e handoff contextual | MANTER | Complementa AG-06 e AX-32; pacote revisável, sem vazamento de credenciais. |
| HX-19 | Fila unificada de revisões | MELHORAR | Associar cada aprovação ao job/arquivos/permissão exatos; regras para mobile. |
| HX-20 | Aprovar, rejeitar ou solicitar alterações | MANTER | Manter histórico de iterações; decisão humana explícita. |
| HX-21 | Políticas de permissão por tarefa | MELHORAR | Combinar regra do projeto, executor, sandbox e dispositivo; reconhecer limite do shell livre. |
| HX-22 | Operações Git protegidas | MELHORAR | Fronteira auditável no backend/execução isolada quando possível; Git mobile apenas revisão/aprovação. |
| HX-23 | Auditoria de aprovações | MANTER | Adequada a desktop e dispositivos pareados; não guardar segredos em eventos. |
| HX-24 | Central global de atividades | MELHORAR | Filtros, notificações por prioridade e acesso rápido sem entrar em cada projeto. |
| HX-25 | Kanban e aprovações pelo iPhone | MELHORAR | Mobile próprio e granular; Git protegido fica no desktop; conexão e serviço seguros. |
| HX-26 | Orçamento de IA por tarefa ou projeto | ADIAR | ADIADO; registrar custos reais desde cedo, adicionar limites configuráveis quando fontes forem confiáveis. |

## Registro de proveniência e incerteza

- As descrições resumidas do Alethe são dados auditados em outubro de 2026; não representam alegação sobre versões posteriores.
- Os refinamentos do HATI foram consolidados a partir das explicações apresentadas na seleção. Onde houver dúvida sobre detalhes de interação, Athena deve registrar a dúvida, não completar por palpite.
- Os mecanismos descritos no Alethe **não** estão aprovados automaticamente como arquitetura HATI.
- Licença e marca do Alethe precisam ser respeitadas. Recursos semelhantes feitos independentemente não implicam automaticamente obrigação de licenciar HATI como AGPL; uso de código de terceiros deve ser analisado separadamente.
- Consulte o relatório completo em `../source/ALETHE_AUDIT_ORIGINAL.txt` e a versão estruturada `DECISIONS.json`.
