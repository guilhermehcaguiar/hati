# Referência — Auditoria original do Alethe

> **Índice de referência factual.** A auditoria é reprodução **verbatim** e não deve ser
> reescrita, resumida como fonte primária nem tratada como fato novo sobre versões
> posteriores do Alethe.

## O que é

Relatório de auditoria técnica do Alethe Agents produzido pela Athena (13/10/2026) como
base da seleção de escopo do HATI: arquitetura, funcionalidades catalogadas com IDs
(WS/LY/TM/AG/US/OR/MC/PL/GT/AX/RC), segurança, testes, limitações e riscos — **187 IDs
nasceram daí**.

## Fonte canônica

- **Arquivo:** `docs/hati-context/source/ALETHE_AUDIT_ORIGINAL.txt`
- **Repositório auditado:** `Kc1t/alethe-agents`, branch `main`, commit
  `f09c597bac056321aeae81c85e176336ca518dfe` (2026-10-12), versão 1.7.0.
- **Integridade:** SHA-256 declarado `1d04d8c90e94b96fcf3549456feab15ca9f64b87d4d65b36a0b02a1794328ae8`
  (ver `docs/hati-context/MANIFEST.json`).

## Como usar (regras)

1. **Fato sobre o Alethe ≠ decisão sobre o HATI.** As decisões do HATI vivem na matriz de
   escopo (ver `docs/reference/SCOPE_MATRIX.md`); onde divergem da auditoria, prevalece a
   decisão humana.
2. **Snapshot de 13/10/2026.** O Alethe pode ter mudado desde então; citações sobre ele
   devem referenciar o commit auditado.
3. **Referência funcional, não licença.** O Alethe é AGPL-3.0-or-later: nada do seu
   código/arte/texto pode ser copiado sem análise de licença (ver `README.md`).
4. As recomendações E/R/O/Q/D da auditoria foram **preliminares** e foram superadas pela
   seleção humana; estão preservadas no relatório apenas como contexto histórico.

## Limitações declaradas da auditoria (constam do próprio relatório)

Somente leitura via GitHub (sem clone/execução); comportamento em runtime não testado;
cobertura exata de testes não medida; segunda página de issues não consultada; itens não
inspecionáveis marcados como hipóteses ou pendências no texto original.
