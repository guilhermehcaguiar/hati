# Desenvolvimento do HATI — processo entre PCs, IAs e sistemas

> Como trabalhar no HATI **antes** de existir código: a fonte de verdade é o repositório,
> nunca o histórico de uma conversa. Nenhum comando de setup é listado aqui — ambiente e
> dependências serão definidos pela arquitetura e só serão instalados com aprovação.

## 1. Ciclo de trabalho de qualquer sessão

1. **Ler antes de agir:** `docs/START_HERE.md` define a sequência mínima; `AGENTS.md` define
   a conduta. Quem pula a leitura produz trabalho incoerente.
2. **Uma autorização = uma etapa:** escopo e critérios de aceitação explícitos; ao terminar:
   resumo, arquivos tocados, verificações executadas (com resultados reais), pendências,
   sugestão de commit — e pausa.
3. **Verificar antes de afirmar:** o estado real do repositório é inspecionável; o estado
   declarado em `docs/CURRENT_STATUS.md` é o registro oficial. Documentação descreve
   intenção; inspeção descreve realidade.
4. **Handoff obrigatório:** preencher/entregar `docs/HANDOFF.md` ao encerrar (base no
   template `docs/hati-context/workflow/SESSION_HANDOFF_TEMPLATE.md`).
5. **Git de escrita é humano:** commits, pushes, merges e afins são executados pelo usuário.
   Agentes apenas sugerem mensagens (Conventional Commits, PT-BR).

## 2. Multi-PC e portabilidade

- **Entrada em qualquer máquina:** obter o repositório por Git (ação humana, quando o
  usuário decidir versionar/hospedar), abrir qualquer CLI suportada (OpenCode, Codex,
  Claude Code), ler `README.md` → `docs/START_HERE.md` → `docs/CURRENT_STATUS.md` →
  `docs/HANDOFF.md`. A documentação funciona sem memória de sessões anteriores.
- **Ambientes alvo:** Linux/Arch/Omarchy (Wayland/Hyprland) é o primário; Windows é alvo
  posterior (Fase 6 do roadmap proposto). Documentos usam **caminhos relativos** — nada de
  paths absolutos de máquinas específicas.
- **CLIs suportadas na v1 do produto:** OpenCode, Codex, Claude Code (DT-02). As versões
  instaladas em cada máquina variam; a documentação não presume comportamento de versão
  específica — verifique na sua CLI o carregamento de `AGENTS.md`/`CLAUDE.md`.

## 3. O que NUNCA é versionado

- Segredos, tokens de provedor, credenciais e chaves (nunca, em nenhum arquivo).
- Dados locais do HATI (bancos de sessão, caches, estados por máquina) — por padrão ficam
  **fora dos repositórios dos projetos** e fora deste repositório (DT-07).
- Configurações pessoais de OpenCode/Codex/Claude do usuário; logs; artefatos de build.
- Um `.gitignore` adequado será definido na fase de arquitetura — não criar antes.

## 4. Ferramentas e dependências

- **Nada é instalado antecipadamente.** Versões de Node/Rust/ferramentas só existirão como
  requisito quando ADRs forem aprovados.
- Skills e configurações globais das CLIs não são alteradas por agentes sem aprovação.
- O pacote `docs/hati-context/` é **somente leitura permanente** (integridade por hashes do
  `MANIFEST.json`).

## 5. Testes e qualidade (política futura)

- Quando houver código: testes de unidade, regressão de PTYs, e2e Linux real e CI — a
  estratégia será decidida no Prompt 03 (questão 12) e registrada em ADR.
- Até lá: qualquer verificação executada por uma sessão deve ser reportada com comando e
  resultado real; verificação não executada é declarada como tal (`docs/HANDOFF.md`).
- Conventional Commits em PT-BR para mensagens sugeridas: `docs: …`, `feat: …`, `fix: …`,
  `chore: …` etc. — sempre como sugestão, nunca executadas por agentes.

## 6. Linguagem e convenções do repositório

- Documentação em **português do Brasil**; termos técnicos consagrados podem ficar em inglês.
- Interface do produto planejada em pt-BR e EN desde a arquitetura (AX-16).
- Mudanças de decisões de escopo passam pela matriz (`docs/hati-context/scope/`); decisões
  técnicas passam por ADR (`docs/decisions/`).
