# Changelog

## 2026-06-08

### CozyHome v1.4 — Voice + Claude Relay
- **🎤 Voice input** — Whisper STT on NPU (distil-whisper-large-v3-int8-ov, :8001)
  - Mic buttons in AI Ask and Diary tabs
  - `toggleVoice(targetId, btnEl)` — MediaRecorder → `/transcribe` proxy → fills textarea
- **🔗 Claude Relay** — "Ask Claude" button in AI Ask tab
  - `relayToClaude()` → `POST /relay/prepare` → saves to `C:\UEFI\relay.md` + copies to clipboard
  - `/relay` slash command in Claude Code reads the file and continues
- **OV server**: swapped from TinyLlama → **Phi-4-mini-instruct-int8-ov** (INT8, NPU-safe)
- `run.ps1`: switched to `dotnet watch` for hot-reload on save
- `cozy-start.ps1`: auto-starts Whisper server at login

### CozyHome v1.3 — Browser Tab Control
- **🌐 Browser tab** — live tab tree from Cozy Tabs extension
- WebSocket bridge at `/browser-ws` — tab tree synced in real-time
- Activate/close tabs from CozyHome UI
- `/browser/panel`, `/browser/tabs`, `/browser/cmd` endpoints

### Cozy Tabs Edge Extension
- MV3 sidebar + popup for Edge
- Tab tree with parent-child relationships (webNavigation API)
- Tab groups with Catppuccin color dots
- `Alt+T` toggle sidebar, `Alt+C` popup
- **MV3 keep-alive fix**: `chrome.alarms` every 24s prevents service worker death
- Sidebar maintains its own persistent WebSocket

### CozyHome v1.2 — Git + ADO
- **🍊 Git tab** — branch status, 20 commits, quick commit/push/standup
- **📋 ADO Work Items** — Azure DevOps @me items via PAT
- CSS classes: `.git-file`, `.git-commit`, `.git-hash`, `.git-msg`, `.git-date`

### Electron Wrapper
- Wraps CozyHome in native window with tray icon (purple house pixel art)
- Minimize-to-tray, auto-starts .NET backend if not running
- `cozy-start.ps1` launches Electron at login

## 2026-06-01

### CozyHome v1.0 — Initial
- Codesearch (jina-embeddings-v2 ONNX INT8, 4,278 chunks)
- AI Ask (RAG + TinyLlama-INT8 on NPU)
- PII Scan, Miro integration, Notes, Diary
- Catppuccin Mocha dark theme
- Build streaming (SSE), Status panel