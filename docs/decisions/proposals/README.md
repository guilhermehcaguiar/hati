# Propostas de ADR — Prompt 03 (arquitetura)

> **Nenhum ADR aqui está aprovado.** Todos têm status **proposta**, produzidos pela Athena
> em 15/10/2026 (Prompt 03). A aprovação é humana e explícita, registro por registro.
> Ao aprovar, o ADR migra para `docs/decisions/ADR-NNN-*.md` (convenção do
> `docs/decisions/README.md`) mantendo a numeração; a aprovação é registrada na conversa
> e em `docs/CURRENT_STATUS.md`. Reprovado/permanece proposta → fica aqui com anotação.

## Índice

| ADR | Título | Questão (TECHNICAL_STUDY) |
|---|---|---|
| [ADR-001](ADR-001-shell-desktop-tauri2.md) | Shell de desktop: Tauri 2 | Q1 |
| [ADR-002](ADR-002-servico-persistente-systemd-user.md) | Serviço persistente: daemon hatid + systemd --user | Q2 |
| [ADR-003](ADR-003-persistencia-sqlite-hibrida.md) | Persistência híbrida com espinha SQLite | Q3 |
| [ADR-004](ADR-004-barramento-de-eventos.md) | Barramento de eventos append-only normalizado | Q4 |
| [ADR-005](ADR-005-adaptadores-de-agentes.md) | Camada de adaptadores de agentes com capacidades | Q5 |
| [ADR-006](ADR-006-autorizacao-e-fronteira-git.md) | Autorização no backend e fronteira Git | Q7 |
| [ADR-007](ADR-007-runtime-de-plugins.md) | Runtime de plugins isolado | Q8 |
| [ADR-008](ADR-008-transporte-mobile-seguro.md) | Transporte mobile: PWA sobre TLS com pareamento | Q9 |
| [ADR-009](ADR-009-usage-local-first.md) | Usage local-first com consentimento | Q10 |
| [ADR-010](ADR-010-frontend-react-typescript.md) | Frontend React + TypeScript | Q1 |

## Regras desta pasta

1. Aprovação é por ADR individual — aprovar um não aprova o conjunto.
2. Nenhum ADR proposta autoriza implementação; as PoCs referenciadas são etapas futuras
   com autorização própria (ver `docs/architecture/VALIDATION_PLAN.md` §6).
3. Numeração reservada: ADR-011+ para decisões que emergirem das PoCs (ex.: estratégia de
   testes/CI, formato IPC definitivo, sandbox de workers).
