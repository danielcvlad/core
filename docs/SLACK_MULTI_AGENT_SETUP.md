# Slack Multi-Agent Setup Guide

This document describes how to configure multiple AI agents as individual Slack bots using OpenClaw's Socket Mode.

## Overview

Each agent (Roger, Nina, Margareta, Chiciu) has its own Slack bot. When someone DMs a bot, OpenClaw routes the message to the correct agent based on which Slack bot received it.

## Architecture

```
Slack Workspace (claw-team-talk)
├── Chiciu Bot (@Chiciu Bot)     → main agent (Chiciu)
├── Roger Bot (@Roger Bot)        → roger-infrastructure-engineer
├── Nina Bot (@Nina Bot)          → nina-marketing-expert
└── Margareta Bot (@Margareta Bot) → margareta agent
```

## Prerequisites

For each agent's Slack app, you need:
1. **Bot Token** (`xoxb-...`) — from OAuth & Permissions
2. **App Level Token** (`xapp-...`) — from Basic Information → App-Level Tokens
3. **Socket Mode enabled** in app settings
4. **Event Subscriptions configured** with `im_message` subscribed

## Setup Steps

### Step 1: Create Slack Apps from Manifests

For each agent, go to https://api.slack.com/apps and create a new app from manifest:

| Agent | Manifest File |
|-------|--------------|
| Chiciu | `slack-manifests/01-chiciu-bot.json` |
| Margareta | `slack-manifests/02-margareta-bot.json` |
| Roger | `slack-manifests/03-roger-bot.json` |
| Nina | `slack-manifests/04-nina-bot.json` |

### Step 2: Enable Socket Mode

In each app's settings:
1. Go to **Socket Mode** in left sidebar
2. Toggle **Enable Socket Mode** to ON
3. A webhook URL will be generated (not used — OpenClaw handles this)

### Step 3: Generate App-Level Token

In each app's settings:
1. Go to **Basic Information** → **App-Level Tokens**
2. Click **Generate Token**
3. Name it (e.g., "chiciu-app-token")
4. Add scope: `connections:write`
5. Copy the app-level token

### Step 4: Subscribe to Events

In each app's settings:
1. Go to **Event Subscriptions**
2. Toggle **Enable Events** to ON
3. Under **Subscribe to bot events**, click **Add Bot User Event**
4. Add: `im_message`
5. Save

### Step 5: Install to Workspace

In each app's settings:
1. Go to **Install App** → **Install to Workspace**
2. Copy the **Bot User OAuth Token**

### Step 6: Configure OpenClaw

Add each bot's tokens to `~/.openclaw/openclaw.json`:

```json
{
  "channels": {
    "slack": {
      "enabled": true,
      "mode": "socket",
      "accounts": {
        "chiciu": {
          "botToken": "<BOT_TOKEN>",
          "appToken": "<APP_TOKEN>"
        },
        "margareta": {
          "botToken": "<BOT_TOKEN>",
          "appToken": "<APP_TOKEN>"
        },
        "roger": {
          "botToken": "<BOT_TOKEN>",
          "appToken": "<APP_TOKEN>"
        },
        "nina": {
          "botToken": "<BOT_TOKEN>",
          "appToken": "<APP_TOKEN>"
        }
      }
    }
  }
}
```

### Step 7: Configure Routing Bindings

Add routing bindings to `~/.openclaw/openclaw.json` to map each Slack account to an agent:

```json
{
  "bindings": [
    {
      "type": "route",
      "agentId": "main",
      "match": { "channel": "slack", "accountId": "chiciu" }
    },
    {
      "type": "route",
      "agentId": "margareta",
      "match": { "channel": "slack", "accountId": "margareta" }
    },
    {
      "type": "route",
      "agentId": "roger-infrastructure-engineer",
      "match": { "channel": "slack", "accountId": "roger" }
    },
    {
      "type": "route",
      "agentId": "nina-marketing-expert",
      "match": { "channel": "slack", "accountId": "nina" }
    }
  ]
}
```

### Step 8: Restart Gateway

```bash
openclaw gateway restart
```

### Step 9: Verify

```bash
openclaw channels status
```

Should show Slack: `ON · OK · tokens ok (bot config×5, app config×5)`

## Testing

Send a DM to any bot in Slack. It should respond with the correct agent identity.

## Agent Identities

Each agent has its own workspace with distinct identity files:

- `roger-infrastructure-engineer/IDENTITY.md` + `SOUL.md` — Roger, Infrastructure Engineer 🔧
- `nina-marketing-expert/IDENTITY.md` + `SOUL.md` — Nina, Marketing Expert 📊
- `margareta/` — Margareta, Assistant 🌟
- Main agent — Chiciu, DevOps Co-pilot 🧀

## Troubleshooting

**Bots don't receive messages:**
- Check Event Subscriptions → Enable Events is ON
- Check that `im_message` is in "Subscribe to bot events"
- Verify Socket Mode is enabled

**Gateway shows Slack WARN:**
- Check that all accounts have both `botToken` AND `appToken`
- Restart gateway: `openclaw gateway restart`

**Agent has wrong identity:**
- Check agent's workspace `SOUL.md` and `IDENTITY.md`
- Ensure BOOTSTRAP.md has been deleted after initial setup
- Restart gateway after updating identity files
