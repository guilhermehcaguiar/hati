# ADR-007 — Runtime de plugins isolado

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** DT-08/R10 — API projetada cedo, catálogo depois; core independente;
  PL-02: enforcement no backend e "WebView compartilhada não basta"; PL-04 import local
  limitado (default off); PL-05 storage segregado; PL-06 contribuições
  views/commands/themes/panes/providers; PL-08 temas sanitizados; PL-03 catálogo futuro.
  A auditoria é explícita: plugins na mesma WebView = "guard-rail, não sandbox"; decidir
  isolamento cedo (child webviews) ou declarar o limite.
- **Alternativas consideradas:**
  1. **UI em child webviews + backend em processos sidecar** — isolamento real; falha
     contida; API declarada por capability; custo de host (processos/janelas).
  2. **Plugins na WebView principal** (padrão Alethe) — simples; limite declarado
     (guard-rail); PL-02 do HATI já rejeita isso como solução alvo.
  3. **WASM como runtime** — sandbox de memória forte, mas sem DOM (não serve p/ views/
     temas); viável só p/ lógica pura no futuro.
- **Decisão (recomendação):** (1) **Manifesto `hati-plugin.json` definido desde a Fase 1**
  (id, versão, faixa da `hati-plugin-api`, capabilities, contribuições views/commands/
  themes/panes, storage) — DT-08: API cedo; (2) **PluginHost na Fase 4**: UI de plugin em
  **child webviews** (esquema `hati-plugin://` por plugin, CSP própria), backend em
  **processo sidecar** com API IPC restrita por capability; (3) storage segregado por
  plugin com quota (PL-05); um plugin não acessa dados de outro nem do core; (4) temas =
  tokens sanitizados (PL-08) — podem anteceder o host completo por serem dados, não
  código; (5) import local desabilitado por default (PL-04); catálogo (PL-03) depois,
  com SHA-256 + review de permissões (hash não comprova segurança — exibido como
  integridade); (6) core 100% funcional sem plugins (critério de saída da Fase 4).
- **Experimento/PoC:** PoC de host (F4): plugin de exemplo + e2e de segurança (capabilities
  excedidas rejeitadas; storage alheio invisível; crash de plugin não derruba o app;
  manifesto inválido rejeitado no load).
- **Impacto:** Linux/Windows: child webviews suportadas no Tauri 2 nas duas plataformas.
  Mobile: plugins não são servidos à PWA na F5 (escopo desktop). IDs: PL-01..08, AX-28.
  Riscos: complexidade do host atrasa F4 (mitigado: contratos F1, runtime F4); custo de
  memória por webview (limitar nº ativos por preferência).
- **Consequências:** melhor: isolamento real (PL-02 cumprido de verdade); ecossistema
  seguro por construção. Pior: host mais caro de construir que a alternativa 2 — pago
  de propósito. Observar: adesão de autores externos ao manifesto e a API v1.
