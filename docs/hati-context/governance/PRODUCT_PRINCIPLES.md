# HATI — princípios e limites aprovados

- **Produto:** HATI (ᚼᛅᛏᛁ), aplicativo desktop independente, não extensão de IDE nem fork do Alethe.
- **Primeiro ambiente:** Linux/Arch/Omarchy em Wayland/Hyprland, Windows depois. Interfaces pt-BR e EN previstas.
- **Fluxo principal:** Projects → Kanban → Agentes/Sessões/Jobs/Worktrees → Evidências → Revisão humana.
- **Primeiros agentes:** OpenCode (Agent Suite), Codex, Claude Code. Aceitar diferenças de capacidades; não prometer paridade.
- **Agent Suite existente:** Athena planeja; Forge executa/orquestra sob autorização; especialistas Pixel, Atlas, Probe, Sentinel e Aegis conforme configurações do OpenCode do usuário. HATI **não** deve recriá-los automaticamente.
- **Autorização:** o usuário confirma cada início de job. Agentes não executam commits, pushes, merges ou publicação automaticamente. Interfaces devem ser explícitas; qualquer garantia real exige engenharia de isolamento/enforcement, não apenas UI.
- **Serviço persistente:** fechar janela desktop não encerra jobs. Hipóteses de comportamento durante logout, reinício, suspensão e crash continuam abertas.
- **Dados:** app local-first; seus arquivos e banco fora dos repositórios por padrão, segredos nunca versionados.
- **Mobile:** iPhone via cliente adaptado, serviço no computador ligado; Tailscale/HTTPS/túnel seguro a avaliar; não disponibilizar modo remoto desprotegido ao usuário.
- **Plugins:** interfaces planejadas desde cedo, catálogo em fase futura; core essencial independente.
- **Identidade:** estética dark clean/densa inspirada em Omarchy; referenciar a imagem de runa específica em `../assets/brand/RUNA_REFERENCIA_USUARIO.jpeg`. Não gerar lobo ou inventar runas adicionais como se fossem a marca aprovada. Tipografia, logo definitivo e tokens visuais NÃO foram finalizados.
- **AGPL / autoria:** auditoria do Alethe é referência factual/funcional. Não copiar código nem arte de marca sem analisar licenças; não assumir que funcionalidades semelhantes por si mesmas determinam licença.
- **Escopo:** 187 IDs confirmados; 156 mantidos/melhorados, 25 adiados, 6 descartados; "aprovado" não equivale a "MVP" ou "implementado".
