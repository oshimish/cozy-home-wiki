# Claude Relay

The relay bridges the local NPU model (fast, private, free) with Claude Code (smarter, broader context).

## Flow

```
User types question
       │
       ▼
  AI Ask tab (CozyHome)
       │ POST /ask
       ▼
  Phi-4-mini on NPU answers (~1-3s)
       │
       ▼
  User clicks 🔗 Ask Claude
       │ POST /relay/prepare
       ▼
  relay.md written to C:\UEFI\relay.md
  Answer + question + sources copied to clipboard
       │
       ▼
  User types /relay in Claude Code
       │
       ▼
  Claude reads relay.md, continues from NPU answer
```

## relay.md Format

```markdown
# Claude Relay — 2026-06-08 15:42

## Original Question
How does PII redaction work in the logging pipeline?

## NPU Answer (Phi-4-mini on NPU)
The PII redaction middleware intercepts log entries...

## Code Sources
Services/Logging/PiiRedactor.cs:42, Services/Logging/LoggingMiddleware.cs:18

## What I Need From Claude
...
```

## `/relay` Skill

The `/relay` command is defined in `C:\UEFI\.claude\commands\relay.md`.

When run, Claude:
1. Reads `C:\UEFI\relay.md`
2. Acknowledges what Phi-4-mini got right
3. Corrects or extends with deeper knowledge
4. Provides complete, production-ready code if needed

## Philosophy

- NPU handles fast local queries (free, private, instant)
- Claude handles: architecture decisions, complex bugs, multi-file refactors
- Relay is manual escalation — you decide when the NPU answer isn't enough
- Future: teach Phi-4-mini to emit `[ASK_CLAUDE: <question>]` for auto-escalation