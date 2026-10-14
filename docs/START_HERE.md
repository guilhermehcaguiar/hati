# START_HERE — Roteiro de entrada para IAs e pessoas novas

Objetivo: qualquer agente, em qualquer máquina, recuperar o contexto completo do HATI
**a partir do repositório**, sem acesso a conversas anteriores.

## Sequência mínima de leitura

| Passo | Arquivo | Por quê |
|---|---|---|
| 1 | `HATI_CONTEXT_INSTRUCOES.md` | Instruções do pacote de contexto e mapa geral |
| 2 | `README.md` | O que é o HATI e status factual |
| 3 | `AGENTS.md` | Regras de conduta (e `CLAUDE.md` se for Claude Code) |
| 4 | `docs/PRODUCT_VISION.md` | Visão, princípios e diferenciação |
| 5 | `docs/SCOPE.md` | Escopo aprovado — 187 IDs + 10 DT — e como ler a matriz |
| 6 | `docs/hati-context/governance/DIRECTIVES_DT.md` | As 10 diretrizes imutáveis (texto canônico) |
| 7 | `docs/hati-context/scope/SCOPE_MATRIX.md` (+ `scope/HX_REQUIREMENTS.md`) | Status e **intenção específica** de cada ID |
| 8 | `docs/CURRENT_STATUS.md` | Estado real verificado, pendências e próximo passo |
| 9 | `docs/HANDOFF.md` | Como continuar de onde a última sessão parou |
| 10 | `docs/hati-context/architecture/CROSS_REVIEW.md` + `architecture/OPEN_QUESTIONS.md` | Modelo conceitual, tensões e o que NÃO está decidido |

## Quando pesquisar o Alethe

`docs/hati-context/source/ALETHE_AUDIT_ORIGINAL.txt` (reprodução integral e verificável da
auditoria; commit `f09c597bac056321aeae81c85e176336ca518dfe`; SHA-256 no `MANIFEST.json`).
Regra: o que está lá é **fato sobre o Alethe**, não decisão sobre o HATI.

## Regras de ouro

1. **Inspecione antes de afirmar.** Nenhuma parte do aplicativo existe em código; "aprovado"
   na matriz não é "implementado" no repositório.
2. **A intenção de um MELHORAR é a especificação.** O código de status e o nome sozinhos não
   bastam — leia a coluna "Intenção específica para HATI" na matriz.
3. **DT-01 a DT-10 não mudam por iniciativa de IA.** Em caso de conflito técnico comprovado,
   documente e peça decisão humana.
4. **Git de escrita é humano. Cada novo job exige confirmação humana.**
5. Ao terminar a sessão, atualize/entregue `docs/HANDOFF.md`.

## Mapa rápido das 187 decisões

- **161 IDs WS–RC** — funcionalidades avaliadas a partir da auditoria do Alethe
  (WS 21 · LY 6 · TM 26 · AG 16 · US 11 · OR 16 · MC 9 · PL 8 · GT 9 · AX 32 · RC 7).
- **26 IDs HX** — propostas exclusivas do HATI (HX-01 a HX-26).
- Totais: 44 MANTER · 112 MELHORAR · 25 ADIAR · 6 DESCARTAR.
- Resumo e leitura orientada: `docs/SCOPE.md`. Dados estruturados:
  `docs/hati-context/scope/DECISIONS.json`.
