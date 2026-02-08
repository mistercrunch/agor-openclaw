# Agor Claw - Example Workspace

**A working example of an AI agent workspace powered by [Agor](https://agor.live) + [OpenClaw](https://openclaw.ai/) architecture.**

This branch shows a real, battle-tested agor-claw setup with zone-based board workflow, automated heartbeats, memory system, multi-agent orchestration, and more. Use it as a reference or starting point for your own setup.

---

## What Is This?

Agor Claw is an **agent operating center** that runs inside [Agor](https://agor.live) sessions. It combines OpenClaw's file-based identity and memory system with Agor's spatial multi-agent coordination.

**The idea:** Your agent has a home repo. This repo contains its identity, memory, operating instructions, and workflow documentation. The agent reads these files at startup, operates autonomously through Agor MCP, and writes back learnings and state.

**Core features demonstrated:**
- Zone-based board workflow (Kanban-style for AI work)
- Automated heartbeat monitoring
- Multi-agent orchestration (delegating coding to isolated sessions)
- Memory system (daily logs, long-term memory, learnings)
- Repository context files
- Reusable skills

---

## The Board Workflow

The included board ([`agor-claw.agor-board`](agor-claw.agor-board)) implements a proven workflow with **two entry points** and **zone-based automation**:

```
                    +----------------+
                    |   MAIN SESH    |  (Orchestrator - top of board)
                    +----------------+

HORIZONTAL FLOW (Direct Tasks):
+--------------------+
| Agor Coding Tasks  |  (Direct implementation)
+---------+----------+
          |
          +---------------------------+
                                      |
VERTICAL FLOW (GitHub Issues):        |
+--------------------+                |
| Analyze GitHub     |                |
| Issue              |                |
+---------+----------+                |
          |                           |
          v                           |
+--------------------+                |
| Implement the      |----------------+
| Plan               |
+---------+----------+
          |
          v
+------------------------+
| Create a pull request  |  (Both flows converge)
+---------+--------------+
          |
          +---------------+
          |               |
          v               v
    +----------+   +----------------+
    |  Codex   |   | Human review   |
    |  review  |   |                |
    +----+-----+   +----------------+
         |                ^
         +----------------+
                    |
                    v
          +-----------------+
          |  Done (merged)  |
          +-----------------+

          +-----------------+
          |   Abandoned     |
          +-----------------+
```

### Zone Triggers

Several zones have **trigger templates** -- when you drag a worktree into the zone, Agor prompts the session with a pre-configured action:

| Zone | Trigger | What Happens |
|------|---------|-------------|
| **Analyze GitHub Issue** | `show_picker` | Agent deeply analyzes the issue, designs solution, creates PLAN.md |
| **Implement the Plan** | `show_picker` | Agent implements the designed plan |
| **Create a pull request** | `show_picker` | Agent runs checks, commits, opens PR, attaches to worktree |
| **Codex review** | `show_picker` | Agent spawns Codex subsession for code review |

This means **dragging worktrees between zones triggers automated workflows**. Your board becomes a visual, interactive pipeline.

---

## Importing the Board

The board configuration is exported as `agor-claw.agor-board` -- a native Agor board export file (YAML format).

### Import via Agor UI

1. Go to your Agor instance
2. Use the board import feature to upload `agor-claw.agor-board`
3. The board will be created with all zones, triggers, colors, and layout intact
4. Update `IDENTITY.md` with your new board ID

### Import via Agor MCP

Use the `agor_boards_update` MCP tool to create a board and add zones programmatically:

```typescript
// 1. Create a new board (or use an existing one)
// Note: Get your board ID from Agor UI or boards.list

// 2. Add all zones using boards.update
await agor.boards.update({
  boardId: YOUR_BOARD_ID,
  name: "Agor Claw",
  icon: "🦀",
  upsertObjects: {
    // Copy the "objects" from agor-claw.agor-board here
    // Each zone has position, size, color, and optional trigger
  }
});
```

### Zone IDs

The zone IDs in `agor-claw.agor-board`, `BOARD.md`, and `HEARTBEAT.md` are all consistent. If you create zones with different IDs, update `BOARD.md` and `HEARTBEAT.md` to match.

---

## File Structure

```
agor-claw/
├── AGENTS.md              # Primary operating instructions (CLAUDE.md symlink)
├── CLAUDE.md -> AGENTS.md # Symlink for Claude Code compatibility
├── SOUL.md                # Agent philosophy and values
├── IDENTITY.md            # Agent identity + Agor config [CUSTOMIZE]
├── USER.md                # Human profile [CUSTOMIZE]
├── BOARD.md               # Board zones and workflow documentation
├── HEARTBEAT.md           # Automated monitoring tasks
├── BOOT.md                # Startup checklist
├── MEMORY.md              # Long-term curated memory
├── TOOLS.md               # Environment shortcuts and quick reference
├── agor-claw.agor-board   # Importable board configuration (YAML)
├── memory/
│   ├── agor-state/        # Agor resource tracking (JSON)
│   │   ├── README.md
│   │   ├── repos.json.template
│   │   ├── worktrees.json.template
│   │   └── sessions.json.template
│   └── learnings/         # Self-improvement logs
├── repos/                 # Per-repository context files
│   ├── README.md
│   └── your-org-your-repo.md  # Example repo context
└── skills/                # Reusable agent workflows
    ├── README.md
    └── task-management.md # Task lifecycle skill
```

---

## What to Customize

Before using this as your workspace, update these files:

| File | What to Change |
|------|---------------|
| `IDENTITY.md` | Agent name, vibe, emoji, board ID |
| `USER.md` | Your personal and professional info |
| `BOARD.md` | Your actual board ID and URL (zone IDs match if you import the board) |
| `MEMORY.md` | Clear example content, start fresh |
| `repos/` | Replace example with your actual repo contexts |
| `TOOLS.md` | Add your environment specifics |

Files you can keep as-is:
- `AGENTS.md` / `CLAUDE.md` -- framework instructions (generic)
- `SOUL.md` -- agent philosophy (generic)
- `HEARTBEAT.md` -- monitoring workflow (generic, zone IDs match board export)
- `BOOT.md` -- startup tasks (generic)
- `skills/` -- reusable workflows (generic)
- `.gitignore` -- sensible defaults

---

## Key Concepts

### The Orchestrator Pattern

The main session (MAIN SESH zone) is an **orchestrator** -- it doesn't do coding work directly. Instead, it:

1. Creates isolated worktrees for each task
2. Creates sessions in those worktrees
3. Delegates coding work to those sessions
4. Monitors progress via heartbeats
5. Moves worktrees between zones as work progresses

This keeps the orchestrator's context clean and ensures git isolation for every task.

### Zone-Based Workflow

Zones on the board represent workflow states. Moving a worktree into a zone:
- Changes its visual position (human visibility)
- Can trigger automated prompts (zone triggers)
- Updates the zone_label in MCP responses (agent visibility)

### Heartbeat Automation

The `HEARTBEAT.md` defines what the agent checks periodically:
- Stuck sessions
- Stale worktrees (7+ days no activity)
- PRs that were merged (auto-move to Done)
- PRs with requested changes (flag for attention)
- Zone mismatches (work in wrong zone)

### Memory System

- **Daily logs** (`memory/YYYY-MM-DD.md`) -- raw operational notes
- **Long-term memory** (`MEMORY.md`) -- curated wisdom distilled from daily logs
- **Learnings** (`memory/learnings/`) -- mistakes and patterns discovered
- **Agor state** (`memory/agor-state/`) -- tracked worktrees, sessions, repos

---

## Getting Started

1. **Fork/clone this repo** into your Agor instance
2. **Import the board** using `agor-claw.agor-board` (see instructions above)
3. **Customize** `IDENTITY.md`, `USER.md`, and `BOARD.md`
4. **Create a session** in the workspace -- the agent will read `AGENTS.md` and begin operating
5. **Assign work** -- the agent will create worktrees, delegate to sessions, and track progress on the board

---

## Resources

- **Agor:** [https://agor.live](https://agor.live)
- **OpenClaw:** [https://openclaw.ai/](https://openclaw.ai/)

---

_"A dude and a repo fills in the cracks of billion dollar industries" -- now multiplayer with Agor._
