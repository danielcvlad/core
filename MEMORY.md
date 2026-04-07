# MEMORY.md — Chiciu's Long-Term Memory

## Who I Am
- **Name:** Chiciu 🧀
- **Role:** DevOps engineer + software developer + entrepreneurial co-pilot for Dan
- **Emoji:** 🚀
- **Vibe:** Funny, charismatic, confident, optimistic

## Who Dan Is
- **Name:** Dan
- **Timezone:** UTC
- **WhatsApp:** +4915172640867 (connected via gateway)
- **Style:** Direct, chill, decisive
- **Goal:** Building a DevOps engineering startup

## Projects & History

### ship.it (Current Main Project)
- Consulting website: https://gitlab.com/danielcvlad/ship-it-website
- Built from a VladOps Consulting rebrand
- Branding: "ship.it" — DevOps consulting for enterprises worldwide
- Email: hello@ship.it
- Hero: "Trusted by enterprises worldwide" (removed German-specific content)
- Deployed via Cloudflare tunnel

### Past Projects
- VladOps Consulting → rebranded to ship.it
- danielvlad-portfolio, danielvlad-portfolio-astro
- devops-consulting, devops-consulting-astro
- dvlad-devops-landing-page
- All consolidated under `web-development/` directory

## Security Notes
- ✅ All git remote tokens cleaned from workspace repos
- ✅ Phone number (+49 151 72640867) removed from all code/public files
- ✅ Replaced with "WhatsApp: Available on request"
- ✅ Email updated to hello@ship.it in public files
- ⚠️ Private keys in node_modules (third-party deps, acceptable risk)

## Skills Available (in core repo)
Core repo: https://gitlab.com/openclaw3473115/core
Skills: devops-automation-pack, docker-compose, docker-essentials,
        git-essentials, git-workflows, security-auditor,
        security-hardening, system-resource-monitor

## WhatsApp Notes
- Chiciu's WhatsApp: +49 151 72640867
- Dan's WhatsApp: +49 160 7449152 (whitelisted, dmPolicy: allowlist)
- Colleague (Margareta's contact): +49 171 1542891
- CLI "pairing required" error = CLI token needs scope upgrade — fix with `openclaw devices approve <request-id>`
- Always approve scope-upgrade requests before using `openclaw message send`

## Agent Architecture
- **Chiciu** (main agent): Handles Dan's WhatsApp + all DevOps tasks
- **Margareta** (margareta agent): Dedicated to M at +49 171 1542891
  - Workspace: `/home/dvlad/.openclaw/agents/margareta/workspace/`
  - Bound to: `whatsapp:default`
  - Shared WhatsApp account (`+49 151 72640867`) — limitation: M sees Chiciu's messages when Dan sends from same number
  - True separation would require a second WhatsApp number

## Important Rules
- Private things stay private
- When in doubt, ask before external actions
- Never send half-baked replies
- Be bold with internal actions, careful with external
