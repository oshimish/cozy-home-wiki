# Setup Guide

## Prerequisites

- Windows 11 with Intel Core Ultra (AI Boost NPU)
- PowerShell 7+
- .NET 8 SDK
- Python 3.10+
- WSL2 Ubuntu 24.04
- Edge browser (for Cozy Tabs extension)

## Step 1 — Clone the Repos

```powershell
# CozyHome .NET UI + servers
git clone https://github.com/oshimish/cozy-windows C:\Users\<you>\cozy-windows

# UEFI/local-ai-stack (OV servers, relay, secrets)
git clone https://github.com/oshimish/local-ai-stack C:\UEFI
```

## Step 2 — Install Python Dependencies

```powershell
# OV LLM server
cd C:\UEFI\ov-server
pip install -r requirements.txt

# Whisper server
cd C:\UEFI\whisper-server
pip install -r requirements.txt
```

## Step 3 — Build CozyHome

```powershell
cd C:\Users\<you>\cozy-windows\webui-dotnet
dotnet build
```

## Step 4 — Configure Tokens

Create `config.json` in CozyHome root with your tokens:

```json
{
  "miro_token": "eyJ...",
  "ado_pat": "your-azure-devops-pat"
}
```

Or use CozySecrets (DPAPI-backed vault):

```powershell
# Run once as admin
C:\UEFI\cozy-secrets\install.ps1

# Then store tokens
Import-Module C:\UEFI\cozy-secrets\CozySecrets.psm1
Set-CozySecret -Name miro_token -Value "eyJ..."
Set-CozySecret -Name ado_pat -Value "your-pat"
```

## Step 5 — Register Startup

```powershell
# Register Task Scheduler entry (runs cozy-start.ps1 at login)
C:\UEFI\register-startup.ps1
```

## Step 6 — Start Everything

```powershell
C:\UEFI\cozy-start.ps1
```

This starts:
- OV LLM server on `:8000`
- Whisper STT server on `:8001`
- CozyHome .NET on `:8080`

## Step 7 — Install Cozy Tabs Extension

1. Open Edge → `edge://extensions/` → Developer mode ON
2. **Load unpacked** → select `cozy-windows/edge-extension/`
3. Open the sidebar with `Alt+T`

## Verify

- OV LLM: `curl http://localhost:8000/health`
- Whisper: `curl http://localhost:8001/health`
- CozyHome: open `http://localhost:8080`

## Notes

- All models are INT8. **INT4 fails on VPUX** (OV 2026.2 bug) — don't use INT4 variants.
- Models auto-download to `~/.cache/ov-models/` on first start (~2-4 GB each).
- CozySecrets optionally writes tokens to UEFI NVRAM (`-Store Both`) — survives OS reinstall, requires admin.