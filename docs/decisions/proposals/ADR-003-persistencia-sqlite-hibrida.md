# ADR-003 — Persistência híbrida com espinha SQLite

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** R4/DT-07 — dados do HATI fora dos repositórios dos projetos; Kanban como
  fonte única auditável (R8, HX-06..12); vínculos n-n (HX-13); histórico de sessões e
  usage (US-04); backups consistentes (AX-20); preferências editáveis. A auditoria
  demonstra o custo do modelo JSON integral reescrito a cada mutação (§11) e a própria
  recomendação do relatório original: SQLite tende a servir melhor para Kanban+métricas
  (§25-3).
- **Alternativas consideradas:**
  1. **SQLite (WAL) p/ estruturados + arquivos p/ scrollback + JSON versionado p/
     preferências** — transações, integridade n-n, consultas, backup online; scrollback é
     stream de bytes (não relacional); preferências pedem legibilidade/edição manual.
  2. **JSON integral versionado** (modelo Alethe) — legível, mas serialização total por
     mutação, sem transações nem queries; Kanban n-n e auditoria viram retrabalho.
  3. **SQLite p/ tudo** — scrollback em BLOB piora o banco; preferências perdem
     editabilidade humana direta.
  4. **Barem de sincronização (CRDT/replicação)** — desnecessário agora: daemon é o único
     escritor; clientes sincronizam por eventos (ADR-004); mobile é read-mostly (RC-02).
  Evidência: rusqlite 0.40.1 (bundled SQLite 3.53.2; backup API; SQLCipher se criptografia
  for exigida futuramente; MIT) — docs oficiais 15/10/2026.
- **Decisão (recomendação):** `hati.db` (SQLite WAL via rusqlite bundled) em
  `~/.local/share/hati/` como **único escritor: o daemon**; migrations versionadas
  executadas no boot (schema_version) e testadas em CI; scrollback append-only em
  `scrollback/<pty>.bin` (cap/compactação configuráveis — TM-03); preferências/temas em
  `~/.config/hati/*.json` com escrita atômica; segredos **nunca** no banco — keyring do
  OS; backup = API de backup/`VACUUM INTO` + export zip seletivo validado (anti-zip-slip),
  nunca contendo keyring. Modelo conceitual: `DATA_MODEL.md`.
- **Experimento/PoC:** PoC-05: 10k tarefas com filtros <100ms; migração v1→v2 com dados
  sintéticos; backup+restore idêntico; kill -9 sem corrupção (WAL).
- **Impacto:** Linux/Windows: idêntico (biblioteca bundled). Mobile: sincronização por
  eventos (ADR-004), sem banco local no iPhone na F5. IDs: HX-06..12, HX-13..17, US-*,
  AX-20, TM-03. Riscos: ferramentas externas escrevendo no banco (mitigado por
  arquivo-sinal e documentação); tamanho com histórico de eventos (prune por idade como
  preferência, nunca automática sem aviso).
- **Consequências:** melhor: integridade transacional, queries reais para Kanban/usage,
  backup consistente, auditoria barata. Pior: um formato binário no meio do backup (o
  export resolve); disciplina de migrations. Observar: crescimento do log de eventos e
  custo de VACUUM em perfis grandes.
