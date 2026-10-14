# Protocolo de continuidade entre PCs e IAs

## Meta

Qualquer agente que trabalhe no HATI deve poder recuperar o contexto **do repositório**, sem depender do histórico privado de ChatGPT, OpenCode, Codex ou Claude.

## Sequência mínima de leitura

1. `HATI_CONTEXT_INSTRUCOES.md` na raiz do repositório.
2. `docs/hati-context/README.md`.
3. `governance/PRODUCT_PRINCIPLES.md` e `governance/DIRECTIVES_DT.md`.
4. `scope/SCOPE_MATRIX.md` e `scope/HX_REQUIREMENTS.md`.
5. `architecture/CROSS_REVIEW.md` + `architecture/OPEN_QUESTIONS.md`.
6. `workflow/CURRENT_STATUS.md` e handoff vigente se existir.
7. Ao pesquisar Alethe: `source/ALETHE_AUDIT_ORIGINAL.txt`.
8. Antes de afirmar que algo está implementado, inspecionar o repositório real.

## Integração com arquivos de instruções de provedores

- Ao criar `AGENTS.md` ou `CLAUDE.md` na raiz, preferir **referências curtas** aos documentos canônicos, sem duplicar status e regras longas.
- A forma de carregamento de `AGENTS.md` e `CLAUDE.md` depende das versões das CLIs: verificar o suporte real em OpenCode, Codex e Claude Code; não presumir que lerão os mesmos arquivos automaticamente.
- O prompt para a Athena é **fornecido separadamente** do ZIP. Outras IAs podem utilizar este mesmo contexto documental, com instruções adaptadas ao seu ambiente e sem depender de acesso ao shell.

## Multi-PC

O Git remoto, se escolhido/configurado pelo usuário, versiona textos, imagens de referência e scripts aprovados; **não** versionar segredos, tokens, bancos locais de sessão, cache, arquivos específicos da máquina nem estados sensíveis de agentes.

O ambiente e dependências serão definidos em arquitetura. Não criar setup scripts ou `package.json` antecipadamente.

## Quando uma sessão termina

Documentar alterações reais, arquivos tocados, resultados de testes, pendências, handoff e sugestões de commit. A IA não efetua commits automaticamente.
