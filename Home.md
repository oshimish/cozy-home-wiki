# Cozy Windows — Home

> Local AI dev hub · Windows 11 + Intel AI Boost NPU

## What Is This?

A local-first AI workstation setup that runs LLMs and speech-to-text on the **Intel NPU** — no API costs, no data leaving your machine. The `cozy-windows` repo wires together:

- **CozyHome** — .NET web UI with AI Ask (RAG), Voice input, Browser tab control, Git, ADO, Diary, Notes
- **OV LLM Server** — Phi-4-mini INT8 on NPU (OpenAI-compatible API)
- **Whisper STT Server** — distil-whisper-large-v3 INT8 on NPU
- **Cozy Tabs** — Edge MV3 extension syncing browser state to CozyHome
- **Claude Relay** — escalate from NPU to Claude Code for harder questions

## Navigation

- [Architecture](Architecture.md) — System overview, data flow, ports
- [Setup Guide](Setup.md) — First-time installation
- [NPU Servers](NPUServers.md) — OV LLM + Whisper STT
- [Claude Relay](Relay.md) — NPU → Claude escalation
- [Changelog](Changelog.md) — What was built when

## Quick Reference

| Thing | Where | Command |
|-------|-------|---------|
| CozyHome UI | http://localhost:8080 | `cd webui-dotnet; .\run.ps1` |
| OV LLM (NPU) | http://localhost:8000 | `cd ov-server; .\start.ps1` |
| Whisper STT (NPU) | http://localhost:8001 | `cd whisper-server; .\start.ps1` |
| Start all | — | `C:\UEFI\cozy-start.ps1` |
| Claude relay | File: `C:\UEFI\relay.md` | `/relay` in Claude Code |

## Model Status

| Service | Model | Note |
|---------|-------|------|
| OV LLM | Phi-4-mini-instruct-int8-ov | INT8 only — INT4 broken on VPUX |
| Whisper | distil-whisper-large-v3-int8-ov | INT8 only |
| Codesearch | jina-embeddings-v2-base-code (ONNX) | Code-aware embeddings |