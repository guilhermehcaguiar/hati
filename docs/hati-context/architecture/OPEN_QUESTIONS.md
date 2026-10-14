# Questões reservadas ao Prompt 03 (arquitetura)

**Não decidir neste pacote.** A Athena deverá comparar alternativas e recomendar com experimentos necessários, sem implementar.

1. Desktop: Tauri 2/WebKitGTK vs outras shells de desktop; Linux Wayland/Hyprland, atualizações, empacotamento e Windows futuro.
2. Serviço persistente: backend separado da GUI, gerenciamento systemd --user, PTYs, reanexação; logout/suspensão/reinício e crash.
3. Persistência: SQLite vs JSON vs híbrido, migrações, transações e backups consistentes.
4. Eventos: schema versionado, snapshots, reconciliação, replay, queue/backpressure e sincronização cliente desktop/mobile.
5. Adaptadores: protocolo OpenCode disponível, Codex app-server, Claude Code, capacidades distintas, versão e fallback seguro.
6. Modelo de entidades: projeto, tarefa, job, terminal, sessão de CLI, worktree, review, approval, evidências e dispositivos.
7. Autorizações: diferenciação "job autorizado" vs tool calls internos, sandbox real, Git, aprovações remotas, auditoria.
8. Plugins: desenho de APIs desde cedo, isolamento, aprovação de capacidades, mecanismo de atualização e armazenamento.
9. Mobile: PWA, endpoint TLS/WSS, pareamento, túnel Tailscale, sessão remota e revogação.
10. Usage: fontes locais vs APIs oficiais, consentimento, cache, métricas comparáveis e custos aproximados.
11. Graphify: índice incremental, custos RAM/CPU, privacidade e API para agentes.
12. Qualidade: e2e Linux real, regressão de PTYs, stress, segurança, CI, ADRe riscos.
13. Roadmap: recomendar fases sem reclassificar IDs aprovados como descartados; MVP será proposta, não decisão já tomada.
14. Empacotamento seguro, migração entre PCs, backups, arquivos próprios fora de repositórios e compatibilidade com CLIs.

### Requisitos de validação de alternativas

Para cada decisão arquitetural: opções, evidências, limitações, trade-offs, risco, experimento/PoC a realizar depois de aprovado, impacto Linux e Windows, impacto em mobile e critérios mensuráveis de aceitação.
