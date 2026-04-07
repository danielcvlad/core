# OpenClaw WhatsApp Channel Setup — Complete Runbook

**Date:** 2026-04-07
**Purpose:** Step-by-step guide to configure OpenClaw WhatsApp channel on a fresh machine

---

## Overview

OpenClaw supports WhatsApp via the Baileys protocol (WhatsApp Web). This guide covers:
1. Enabling the WhatsApp plugin
2. Linking your WhatsApp account (QR code login)
3. Configuring access policies (allowlist)
4. Fixing CLI auth issues ("pairing required")
5. Sending outbound messages

---

## Step 1 — Enable the WhatsApp Plugin

By default, the WhatsApp plugin is **disabled**. Enable it with:

```bash
openclaw plugins enable whatsapp
```

Then restart the gateway:

```bash
openclaw gateway restart
```

Verify it loaded:

```bash
openclaw plugins list | grep whatsapp
# Should show: whatsapp | whatsapp | openclaw | loaded | stock:whatsapp/index.js
```

---

## Step 2 — Link WhatsApp (QR Login)

> **Note:** If credentials already exist at `~/.openclaw/credentials/whatsapp/default/`, the account is already linked and this step can be skipped.

```bash
openclaw channels login --channel whatsapp
```

This will display a QR code in the terminal. Scan it with the WhatsApp app you want to link.

After scanning, the gateway will show:
```
Listening for personal WhatsApp inbound messages.
```

**Phone numbers:**
- The WhatsApp account linked = **Chiciu's WhatsApp**: `+49 151 72640867`
- **Dan's phone** (whitelisted): `+49 160 7449152`
- **Colleague** (when needed): `+49 171 1542891`

---

## Step 3 — Configure Access Policy

By default, `dmPolicy` is set to `"pairing"`, which requires incoming senders to be manually approved.

For direct allowlist mode (recommended for known numbers):

```bash
openclaw config set channels.whatsapp '{"allowFrom":["+491607449152","+491711542891"],"dmPolicy":"allowlist"}'
```

This whitelist means:
- Only numbers in `allowFrom` can message the bot
- No pairing/approval flow needed

Apply with:

```bash
openclaw gateway restart
```

Verify:

```bash
openclaw channels status
# Should show: dm:allowlist, allow:+491607449152,+491711542891
```

---

## Step 4 — Fix "pairing required" CLI Error

**Symptom:**
```bash
openclaw message send --channel whatsapp --target +49XXX --message "Hi"
# Error: gateway connect failed: GatewayClientRequestError: pairing required
```

**Root Cause:** The CLI's device token lacks the `operator.talk` scope needed to send messages. The gateway automatically generates a scope-upgrade request (visible with the "repair" flag).

**Fix:**

```bash
# Check for pending pairing requests
openclaw devices list
# Look for a row with "repair" flag and "operator.talk" in scopes

# Approve the pending request (replace REQUEST_ID with the id from above)
openclaw devices approve <REQUEST_ID>
```

After approval, the CLI can send messages:

```bash
openclaw message send --channel whatsapp --target +491607449152 --message "Hello!"
# ✅ Sent via gateway (whatsapp). Message ID: 3EB0XXX
```

---

## Step 5 — Send Outbound Messages

Once the CLI has proper scopes:

```bash
openclaw message send --channel whatsapp --target +491607449152 --message "Your message here"
```

**Options:**
```bash
# With media
openclaw message send --channel whatsapp --target +491607449152 --message "Check this out!" --media /path/to/image.png

# Silent send (no notification)
openclaw message send --channel whatsapp --target +491607449152 --message "Ping" --silent
```

---

## Colleague Setup (Margareta's Agent)

When creating agents that use the shared WhatsApp channel, their number must be added to `allowFrom`:

```bash
openclaw config set channels.whatsapp '{"allowFrom":["+491607449152","+491711542891"],"dmPolicy":"allowlist"}'
openclaw gateway restart
```

---

## Key Lessons

1. **"pairing required" ≠ WhatsApp unlinked** — It means the CLI token needs a scope upgrade, fixed with `openclaw devices approve <request-id>`

2. **Allowlist survives restarts** — The `allowFrom` config persists; only the gateway needs restarting to pick up changes

3. **WhatsApp credentials are per-account** — Located at `~/.openclaw/credentials/whatsapp/<accountId>/`

4. **The "repair" flag** — Auto-generated when a previously-paired device reconnects and needs scope expansion approved

---

## Useful Commands Cheat Sheet

```bash
# Channel status
openclaw channels status

# Enable plugin
openclaw plugins enable whatsapp

# Link WhatsApp
openclaw channels login --channel whatsapp

# Logout WhatsApp
openclaw channels logout --channel whatsapp

# List devices + pending requests
openclaw devices list

# Approve pairing/scope request
openclaw devices approve <request-id>

# Send message
openclaw message send --channel whatsapp --target +49XXX --message "Hi"

# Restart gateway
openclaw gateway restart

# View logs
tail -f /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log
```

---

## Architecture

```
OpenClaw Gateway (loopback:18789)
├── WhatsApp Plugin (Baileys / WhatsApp Web)
│   └── Linked to: +49 151 72640867
├── Telegram Bot (polling)
└── CLI Client
    └── Token scopes: operator.read (+ operator.talk after approval)
```
