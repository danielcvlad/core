# Multi-Agent Identity Confusion Fix

**Date:** 2026-04-07  
**Problem:** Roger (infrastructure-engineer agent) was responding as Chiciu. All agents were confused about their identity.

## Symptoms

- Roger introduced himself as "Chiciu 🧀"
- Agents couldn't maintain consistent identity
- Each fresh session started with "who am I?" confusion

## Root Cause

**BOOTSTRAP.md files were still present** in agent workspaces.

Per AGENTS.md:
> "If `BOOTSTRAP.md` exists, that's your birth certificate. Follow it, figure out who you are, then delete it."

When BOOTSTRAP.md exists, the agent follows its instructions on every fresh session — forcing identity rediscovery instead of using the established `IDENTITY.md` + `SOUL.md` files.

## Affected Agents

| Agent | Workspace | Had BOOTSTRAP? | Fixed? |
|-------|-----------|----------------|--------|
| Roger | `workspace/roger-infrastructure-engineer/` | ✅ Yes | ✅ Deleted |
| Nina | `workspace/nina-marketing-expert/` | ✅ Yes | ✅ Deleted |
| Margareta | `agents/margareta/workspace/` | ✅ Yes | ✅ Deleted |

## Fix Steps

### 1. Identify the Problem

```bash
# Check for BOOTSTRAP.md in each agent workspace
find ~/.openclaw/workspace -name "BOOTSTRAP.md" 2>/dev/null
find ~/.openclaw/agents -name "BOOTSTRAP.md" 2>/dev/null
```

### 2. Delete BOOTSTRAP.md from Each Affected Agent

```bash
rm ~/.openclaw/workspace/roger-infrastructure-engineer/BOOTSTRAP.md
rm ~/.openclaw/workspace/nina-marketing-expert/BOOTSTRAP.md
rm ~/.openclaw/agents/margareta/workspace/BOOTSTRAP.md
```

### 3. Restart Gateway

```bash
openclaw gateway restart
```

### 4. Verify

Each agent should now use their `IDENTITY.md` and `SOUL.md` files on startup, maintaining consistent identity.

## Prevention

**Before creating a new agent:**
1. Complete the BOOTSTRAP.md setup process
2. Delete BOOTSTRAP.md after identity is established
3. Verify `IDENTITY.md` and `SOUL.md` are properly filled out

**Checklist for new agent workspaces:**
- [ ] `IDENTITY.md` — name, emoji, role, vibe
- [ ] `SOUL.md` — personality, specialties, how to work
- [ ] `USER.md` — who they're helping, context
- [ ] `BOOTSTRAP.md` — should NOT exist after setup

## Multi-Agent Architecture Reference

Each agent has its own Slack bot for routing:

```
Slack Workspace (claw-team-talk)
├── @Chiciu Bot     → main agent (Chiciu)
├── @Roger Bot      → roger-infrastructure-engineer
├── @Nina Bot       → nina-marketing-expert
└── @Margareta Bot  → margareta agent

Routing is via bindings in openclaw.json:
- slack:chiciu     → main
- slack:roger       → roger-infrastructure-engineer
- slack:nina        → nina-marketing-expert
- slack:margareta   → margareta
```

## Gateway Restart Note

When running `openclaw gateway restart`, the exec process is killed. Use the **PENDING_WORK.md** mechanism to survive restarts:

1. Before restart: write pending work to `PENDING_WORK.md`
2. After restart: agent reads it on startup and completes the work
