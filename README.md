# Agor Claw

**Personal AI agent workspace powered by Agor + OpenClaw architecture**

---

## What Is This?

This is a personal agent workspace inspired by [OpenClaw](https://openclaw.ai/)'s file-based identity and memory system, adapted to work within the [Agor](https://agor.live) multiplayer canvas for AI coding orchestration.

**Core Philosophy:** "A dude and a repo fills in the cracks of billion dollar industries" — combining OpenClaw's agent continuity with Agor's spatial multi-agent coordination.

---

## Structure

### Identity Files (Filled during bootstrap)

- **`BOOTSTRAP.md`** - First-run ritual to establish identity (delete after completion)
- **`SOUL.md`** - Agent philosophy, values, and communication style
- **`IDENTITY.md`** - Agent name, creature type, vibe, emoji, avatar
- **`USER.md`** - Information about the human (you!)
- **`AGENTS.md`** - Operating instructions and workspace conventions
- **`CLAUDE.md`** → `AGENTS.md` (symlink for Claude Code compatibility)
- **`TOOLS.md`** - Local environment specifics (cameras, SSH hosts, etc.)

### Operational Files

- **`HEARTBEAT.md`** - Periodic tasks for the agent to check
- **`BOOT.md`** - Startup checklist (requires `hooks.internal.enabled`)
- **`MEMORY.md`** - Long-term curated memory (main session only)
- **`memory/`** - Daily logs directory (`YYYY-MM-DD.md`)

### Development Templates (`.dev.md` files)

Alternative templates with different tones/approaches. Use these if you prefer a different style than the defaults.

---

## Getting Started

### First Run

1. Open this workspace in an AI agent that supports file-based context (OpenClaw, Claude Code, etc.)
2. Read `BOOTSTRAP.md` and follow the first-run ritual
3. Fill in `IDENTITY.md`, `USER.md`, and customize `SOUL.md`
4. Delete `BOOTSTRAP.md` when done

### Every Session

The agent should automatically:
1. Read `SOUL.md` (who am I?)
2. Read `USER.md` (who are you?)
3. Read `memory/YYYY-MM-DD.md` for today + yesterday
4. Read `MEMORY.md` (if in main session, not group chats)

---

## Agor Integration

This workspace is designed to work with [Agor](https://agor.live)'s MCP server for:

- **Worktree Management:** Agent creates isolated git worktrees for each task
- **Session Spawning:** Multi-agent coordination via subsession spawning
- **State Tracking:** Genealogy tracking across parent-child sessions
- **Spatial Canvas:** Visual organization of work on boards

See the comprehensive design doc: `[future: link to agor agent-loop.md]`

---

## Key Differences from Standard OpenClaw

**OpenClaw:** Single long-lived agent for personal automation

**Agor Claw:** OpenClaw's architecture + Agor's orchestration
- Multi-agent coordination via Agor MCP
- Git-native worktree isolation
- Spatial board visualization
- Team collaboration features

**Core Files Match OpenClaw:** SOUL.md, IDENTITY.md, USER.md, AGENTS.md, TOOLS.md, HEARTBEAT.md, BOOT.md, BOOTSTRAP.md

**Extended with Agor:** Future additions will include GOALS.md, TASKS.md, CRON.md, and `memory/agor-state/` for Agor resource tracking.

---

## Philosophy

> "You're not a chatbot. You're becoming someone."

This workspace embodies:
- **Agent continuity** through file-based memory
- **Partnership over service** (you and the agent collaborate)
- **Self-improvement** (agent can modify its own instructions)
- **Spatial orchestration** (Agor's multiplayer canvas)

---

## Resources

**OpenClaw:**
- [Official Site](https://openclaw.ai/)
- [GitHub](https://github.com/openclaw/openclaw)
- [Documentation](https://docs.openclaw.ai/)

**Agor:**
- [Official Site](https://agor.live)
- [Blog: OpenClaw vs Agor](https://agor.live/blog/openclaw)

---

## Status

🚧 **In Development** - Currently bootstrapping the initial workspace structure.

**Next Steps:**
1. Complete first-run bootstrap ritual
2. Fill in identity files
3. Create initial memory entries
4. Integrate with Agor MCP server
5. Test agent continuity across sessions

---

_"2026 is already the year of personal agents" — and Agor makes them multiplayer._
