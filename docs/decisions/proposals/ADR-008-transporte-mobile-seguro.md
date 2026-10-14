# ADR-008 — Transporte mobile: PWA sobre TLS com pareamento

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** DT-09 (usuário local único + dispositivos pareados — pareamento não
  substitui auth granular), DT-10 (iPhone logo após o núcleo desktop confiável; sem cloud
  relay obrigatória), DT-06 (revisar/aprovar no mobile; Git protegido no desktop),
  RC-01/02/03/05/06/07, HX-25; RC-04 **descartado** (nada sem TLS). A auditoria
  recomenda herdar o modelo de consentimentos/limites do Alethe trocando o transporte.
- **Alternativas consideradas:**
  1. **PWA servida pelo hatid sobre TLS/WSS** — na LAN com certificado próprio (CA local
     + pinning no pareamento); fora de casa via **Tailscale serve** (tailnet, certs
     automáticos `*.ts.net`); pareamento QR→token efêmero→dispositivo com escopos
     (RC-03), expiração, lockout, revogação (RC-06); read-only default (RC-02).
  2. **App nativo iOS** — push/biometria nativos, custo alto; RC/HX não exigem; atrasa
     DT-10. A API do daemon permanece igual — reversível depois.
  3. **Proxy/túnel público** (ngrok/cloudflare) — expõe o serviço a terceiros; contraria
     local-first; RC-05 aprovou Tailscale assistido, não relay público.
  Evidências: Tailscale serve/funnel (tailscale.com/kb/1247) — serve restringe ao
  tailnet com TLS; `opencode pair` (docs v2) valida o padrão "web local com auth" que o
  HATI generaliza para dispositivos pareados.
- **Decisão (recomendação):** **PWA** entregue pelo próprio `hatid` (RemoteGateway) sob
  TLS obrigatório (LAN: cert próprio + pinning declarado no pareamento; fora de casa:
  Tailscale serve no tailnet do usuário — detectado e assistido, nunca obrigatório);
  pareamento por QR com token efêmero de janela curta; `PairedDevice` com escopos
  granulares por ação (RC-03), expiração default, revogação imediata, tentativas
  limitadas e auditoria (RC-06); **nenhuma rota de Git de escrita existe no contrato
  mobile** (DT-06); escritas offline não existem na F5 (sem CRDT prematuro — escrever
  exige conexão viva); notificações push exigiriam serviço externo — fora do escopo
  inicial (alertas via PWA aberta/notificações locais).
- **Experimento/PoC:** PoC-07 (F5): iPhone pareado via tailnet vê Kanban e aprova um job;
  revogação <5s; conexão sem TLS recusada (RC-04 inexistente); enumeração de rotas sem
  Git de escrita (teste).
- **Impacto:** Linux: nenhuma mudança no desktop. iPhone: PWA instalável; sem app-store.
  Windows (F6): gateway idêntico. IDs: RC-01..03/05..07, HX-25, DT-06/09/10. Riscos:
  UX de cert próprio na LAN (mitigada por pinning explícito no pareamento); dependência
  de Tailscale opcional (LAN cobre o caso local); restrições de PWA em iOS
  (armazenamento/background — desenhar para reconnect por cursor, já coberto pelo
  ADR-004).
- **Consequências:** melhor: DT-10 cedo e barato; mesma API desktop/mobile; zero app
  nativo. Pior: sem push nativo na F5 (declarado). Observar: adoção do Tailscale pelo
  usuário e comportamento da PWA em reconexões longas.
