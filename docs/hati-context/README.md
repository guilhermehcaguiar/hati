# HATI — Pacote mestre de contexto v1

Este diretório reúne os **187 IDs de escopo aprovados ou classificados**, as **10 diretrizes DT** e a **auditoria original da Athena**. É uma base de conhecimento a ser absorvida pelo repositório HATI, não código do aplicativo.

## Leitura inicial

1. `governance/PROVENANCE_AND_PRIORITY.md` — ordem das fontes e o que foi de fato autorizado.
2. `governance/PRODUCT_PRINCIPLES.md` — princípios HATI.
3. `governance/DIRECTIVES_DT.md` — DT-01 a DT-10.
4. `scope/SCOPE_MATRIX.md` — 187 funcionalidades e refinamentos.
5. `scope/HX_REQUIREMENTS.md` — 26 exclusivas detalhadas.
6. `architecture/CROSS_REVIEW.md` — duplicações, dependências, tensões, riscos e lacunas.
7. `architecture/OPEN_QUESTIONS.md` — questões da futura arquitetura.
8. `workflow/CURRENT_STATUS.md` e `workflow/AI_PORTABILITY.md` — próximo passo e continuidade.
9. `source/ALETHE_AUDIT_ORIGINAL.txt` — material factual inicial, completo.

## Integridade

- 161 funcionalidades do Alethe + 26 exclusivas do HATI = 187.
- MANTER 44 · MELHORAR 112 · ADIAR 25 · DESCARTAR 6.
- 156 mantidas ou melhoradas são **escopo do produto**, não MVP.
- Nenhuma tecnologia definitiva escolhida ou linha de código de produção implementada por este pacote.
- `scope/DECISIONS.json` traz dados estruturados verificáveis por máquina.

## Identidade visual

`assets/brand/RUNA_REFERENCIA_USUARIO.jpeg` registra a referência de símbolo enviada pelo usuário. Evite representar a marca com lobo ou runas improvisadas; use esta referência apenas como orientação até aprovação da identidade final.

## Comece por

O prompt para Athena é fornecido **separadamente** deste ZIP. Comece por `HATI_CONTEXT_INSTRUCOES.md` na raiz e por este README. Nenhum arquivo comum existente do app é sobrescrito por este pacote.
