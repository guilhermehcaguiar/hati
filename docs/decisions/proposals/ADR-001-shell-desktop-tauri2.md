# ADR-001 — Shell de desktop: Tauri 2

- **Status:** proposta
- **Data:** 2026-10-15
- **Contexto:** o HATI precisa de uma shell desktop Linux-first (Wayland/Hyprland — R5,
  TM-21), leve e densa (estética Omarchy, AX-14/15), com distribuição Linux ampla e
  update assinado (AX-23), frontend não presumido (Prompt 03 §5A). A GUI é um cliente do
  daemon (ADR-002) — a shell só hospeda UI. Referências: DT-02/DT-05; auditoria do Alethe
  (Tauri 2, com Linux subvalidado — issues #205/#192/#111 e workaround WEBKIT_DISABLE_DMABUF).
- **Alternativas consideradas:**
  1. **Tauri 2** — WebView nativa (WRY/TAO; WebKitGTK no Linux); app mínimo <600KB; audits
     de segurança públicos; distribuição oficial AppImage/AUR/deb/rpm/snap + signing;
     plugins updater/single-instance/notification. Limitação: quirks WebKitGTK (DMABUF,
     WebGL, IME) exigem PoC e e2e próprios. (docs: v2.tauri.app, jul/2026)
  2. **Electron 44** — Chromium embutido: renderização uniforme, ecossistema gigante;
     custo ~150–250MB e RAM elevada; contraria o perfil Omarchy. (docs: electronjs.org,
     out/2026)
  3. **GTK4 nativo (Rust)** — integração Wayland máxima; custo de UI de terminal/Kanban
     proibitivo sem xterm.js e ecossistema web; desenvolvimento lento.
  4. **Flutter desktop** — motor próprio, ecossistema de terminal fraco, Dart fora do
     repertório da Agent Suite.
- **Decisão (recomendação):** **Tauri 2** como shell do HATI, condicionada à aprovação da
  PoC-06 (terminal xterm.js em WebKitGTK/Wayland com critérios objetivos de fps/IME/
  clipboard/resize). **Plano B documentado:** se a PoC-06 reprovar de forma intransponível,
  migrar para Electron sem mudar ADR-002..010 (a GUI é cliente do daemon; a troca de shell
  é local). Critério de troca: qualquer falha sem workaround na lista da PoC-06 após uma
  tentativa razoável de correção upstream/own-code.
- **Experimento/PoC:** PoC-06 (`VALIDATION_PLAN.md` §1): 60fps @100k linhas scrollback;
  IME pt-BR; clipboard; resize; drag&drop; AppImage funcional em Omarchy limpo.
- **Impacto:** Linux/Windows: Tauri 2 cobre ambos (WebView2 no Windows — F6); mobile: a
  PWA não usa a shell (ADR-008). IDs: TM-21, AX-19 (zoom WebView), AX-23, WS-19
  (single-instance plugin). Riscos: quirks WebKitGTK (mitigados por PoC+plano B);
  dependência de plugins oficiais (updater) saudáveis.
- **Consequências:** melhor: consumo/alinhamento Omarchy, Rust compartilhado com o
  daemon, distribuição ampla. Pior: dois "engines" de atenção (WebKitGTK) e o dever de
  provar Linux em CI. Observar: evolução do WebKitGTK no Arch e do `tauri-driver` para e2e.
