# Proveniência, status e precedência das fontes

## Fontes existentes neste pacote

1. `source/ALETHE_AUDIT_ORIGINAL.txt`: reprodução byte a byte do relatório original Athena, baseado no Alethe v1.7.0, commit `f09c597bac056321aeae81c85e176336ca518dfe`, auditado em 13/10/2026; SHA-256 `1d04d8c90e94b96fcf3549456feab15ca9f64b87d4d65b36a0b02a1794328ae8`.
2. `scope/SCOPE_MATRIX.md` e `scope/DECISIONS.json`: 187 status aprovados e seus refinamentos para HATI.
3. `scope/HX_REQUIREMENTS.md`: detalhamento das 26 funcionalidades próprias HX.
4. `governance/DIRECTIVES_DT.md`: 10 escolhas de produto confirmadas.
5. `architecture/CROSS_REVIEW.md`: resultado analítico de sobreposições, dependências, conflitos e lacunas; propostas arquiteturais NÃO são decisões tecnológicas aprovadas.
6. `assets/brand/RUNA_REFERENCIA_USUARIO.jpeg`: imagem referência de marca fornecida pelo usuário (não equivale à arte final).

## Ordem recomendada de interpretação

- **Escolhas explícitas do usuário e diretivas DT** governam as funcionalidades do HATI.
- **Intenção HATI** na matriz detalha o significado de MANTER/MELHORAR/ADIAR/DESCARTAR.
- **Auditoria original** serve de fonte para o que o Alethe fazia em seu commit auditado, NÃO para decisões automáticas no HATI.
- **Revisão transversal** é análise e recomendação; não substitui decisões do usuário.
- **Estado real do repositório** é verificado por inspeção; documentação não é evidência de código executado.

## O que NÃO está aprovado

Nenhuma stack, arquitetura definitiva, migração técnica, cronograma, agente de execução ou etapa de implementação. Nenhum comando Git de escrita foi autorizado.
