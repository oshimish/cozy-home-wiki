# Cozy Windows — Public Wiki

Local AI dev hub running **LLMs + Speech-to-Text on Intel NPU** — no cloud costs, no data leaving your machine.

**Private code repos:** [oshimish/cozy-windows](https://github.com/oshimish/cozy-windows) · [oshimish/local-ai-stack](https://github.com/oshimish/local-ai-stack)

---

## Pages

| Page | Description |
|------|-------------|
| [Home](Home.md) | Overview + quick reference |
| [Architecture](Architecture.md) | System diagram, data flows, ports |
| [Setup Guide](Setup.md) | First-time installation |
| [NPU Servers](NPUServers.md) | OV LLM + Whisper STT servers |
| [Claude Relay](Relay.md) | Escalate from NPU to Claude Code |
| [Changelog](Changelog.md) | What was built when |

## Stack

- **CozyHome** — ASP.NET Core 8 minimal API + HTMX, Catppuccin dark theme
- **OV LLM** — Phi-4-mini-instruct INT8 on Intel NPU via OpenVINO GenAI
- **Whisper STT** — distil-whisper-large-v3 INT8 on NPU
- **Cozy Tabs** — Edge MV3 extension with real-time tab tree
- **CozySecrets** — DPAPI vault + UEFI NVRAM token storage
- **Claude Relay** — Manual escalation from local NPU to Claude Code

## Quick Start

```powershell
C:\UEFI\cozy-start.ps1   # starts all servers + CozyHome
```

Then open http://localhost:8080