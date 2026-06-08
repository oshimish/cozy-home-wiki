# Architecture

## System Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│  Windows 11 — Intel Core Ultra (AI Boost NPU)                   │
│                                                                  │
│  ┌──────────────┐   WebSocket   ┌───────────────────────────┐   │
│  │  Edge Browser│◄─────────────►│  CozyHome .NET :8080      │   │
│  │  Cozy Tabs   │  ws://:8080/  │  ASP.NET Core minimal API │   │
│  │  (MV3 ext)   │  browser-ws   │  HTMX + Catppuccin UI     │   │
│  └──────────────┘               └───────────┬───────────────┘   │
│                                             │                    │
│  ┌──────────────┐                           │ HTTP              │
│  │  Electron    │ localhost:8080            │                    │
│  │  (tray app)  │◄──────────────────────────┤                    │
│  └──────────────┘                           │                    │
│                                   ┌─────────┴──────────┐        │
│                                   │  Local Services     │        │
│                                   │  :8000 OV LLM (NPU)│        │
│                                   │  :8001 Whisper (NPU)│        │
│                                   └────────────────────┘        │
│                                                                  │
│  ┌──────────────────────────────────────────────────────┐        │
│  │  WSL2 Ubuntu — ~/cozy-home/                          │        │
│  │  Codesearch: jina-embeddings-v2 ONNX INT8            │        │
│  │  Code chunks from your local project                 │        │
│  └──────────────────────────────────────────────────────┘        │
└─────────────────────────────────────────────────────────────────┘
         │ relay.md
         ▼
  Claude Code (CLI)
  /relay skill reads C:\UEFI\relay.md
```

## Data Flows

### AI Ask (RAG + NPU)
1. User types question in CozyHome AI Ask tab
2. CozyHome calls WSL codesearch → top 5 code chunks
3. Chunks + question → `POST http://localhost:8000/v1/chat/completions`
4. Phi-4-mini answers on NPU in 1–3 seconds
5. Optional: "🔗 Ask Claude" → saves to `relay.md` → `/relay` in Claude Code

### Voice Input
1. User clicks 🎤 → MediaRecorder captures audio
2. Audio blob → `POST /transcribe` (CozyHome)
3. CozyHome proxies to `http://localhost:8001/v1/audio/transcriptions`
4. Whisper on NPU transcribes → text fills textarea

### Browser Tab Control
1. Cozy Tabs extension connects to `ws://localhost:8080/browser-ws`
2. Sends tab list on every change (active, title, url, groupId, parentId)
3. CozyHome caches + renders in Browser tab
4. Click "activate" → CozyHome sends JSON command back via WS → extension calls `chrome.tabs.update`

## Ports

| Port | Service | Protocol |
|------|---------|----------|
| 8080 | CozyHome .NET | HTTP + WebSocket |
| 8000 | OV LLM (Phi-4-mini) | HTTP (OpenAI-compatible) |
| 8001 | Whisper STT | HTTP (OpenAI-compatible) |

## Key Constraints

- **INT4 fails on NPU**: OV 2026.2 VPUX MatMul decomp bug — all models must be INT8
- **MV3 service workers**: killed after ~30s inactivity — solved via `chrome.alarms` keep-alive every 24s + sidebar's own WebSocket
- **WSL paths**: Windows sees `\wsl.localhost\Ubuntu\home\<user>\`, WSL sees `/home/<user>/`, never mix them