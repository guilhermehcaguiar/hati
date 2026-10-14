# Decisões arquiteturais do HATI — convenção ADR

> **Nenhum ADR existe ainda — corretamente.** Nenhuma decisão técnica foi aprovada; a
> matriz de escopo define o **produto**, e o Prompt 03 produzirá **propostas** de decisão.

## O que é um ADR aqui

Registro de uma decisão **técnica** tomada (ou proposta), com contexto e consequências —
não uma especificação funcional. Exemplos de futuros temas: shell do desktop, serviço
persistente, camada de persistência, schema de eventos, adaptadores de agentes, sandbox de
plugins, transporte remoto.

## Distinção com os outros registros de decisão

| Registro | Natureza | Fonte | Mutabilidade |
|---|---|---|---|
| DT-01..DT-10 | Produto, diretrizes humanas | `docs/hati-context/governance/DIRECTIVES_DT.md` | Imutáveis para IA |
| 187 IDs (matriz) | Escopo de produto | `docs/hati-context/scope/` | Somente por decisão humana |
| ADR-NNN | Tecnologia | este diretório | Mutável por novo ADR |
| Roadmap | Sequência de entregas | `docs/ROADMAP.md` | Proposta; alteração humana |

## Estrutura exigida de cada ADR

```markdown
# ADR-NNN — [título curto]

- **Status:** proposta | aceito | substituído por ADR-XXX
- **Data:** AAAA-MM-DD
- **Contexto:** problema e restrições aplicáveis (citar DTs/IDs/AGENTS.md relevantes).
- **Alternativas consideradas:** cada uma com evidências, trade-offs e limitações.
- **Decisão (ou recomendação, se status=proposta):** o que foi escolhido e por quê.
- **Experimento/PoC:** o que validar antes/depois da aprovação, com critérios mensuráveis.
- **Impacto:** Linux/Windows/mobile; IDs da matriz afetados; riscos.
- **Consequências:** o que fica melhor, o que fica pior, o que observamos depois.
```

## Regras

1. Numeração sequencial (`ADR-001`, `ADR-002`, …), um arquivo por ADR
   (`docs/decisions/ADR-001-titulo.md`).
2. Nenhum ADR é criado com status "aceito" sem aprovação humana explícita registrada.
3. Decisões tomadas "na conversa" sem registro aqui **não existem** para futuras sessões.
4. Um ADR só é substituído por outro ADR — nunca editado para dizer outra coisa.
5. As 14 questões abertas (`docs/ARCHITECTURE.md` §3) são os primeiros candidatos a ADRs
   após o Prompt 03.
