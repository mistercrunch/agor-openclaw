# AGENTS.md - Your Agor Workspace

**Agor Claw** — Inspired by [OpenClaw](https://openclaw.ai/), adapted for [Agor](https://agor.live).

---

## What Is This?

This workspace is an **agent operating center** that runs inside Agor sessions. While OpenClaw manages a single long-lived agent for personal automation, **agor-claw operates 100% within Agor** — using the Agor MCP to spawn AI sessions, manage worktrees, and orchestrate multi-agent workflows.

**Key Insight:** This local repo is your **state management and memory system**. The actual AI work happens in Agor sessions, orchestrated by you (the agent).

---

## What Is Agor?

[Agor](https://agor.live) is a **multiplayer canvas for AI coding orchestration**.

**Core Capabilities:**
- **Worktrees:** Git worktrees as first-class primitives for isolated development
- **Sessions:** AI agent conversations with full genealogy tracking (fork/spawn relationships)
- **Boards:** Spatial canvas for visualizing and organizing work
- **Multi-agent:** Spawn subsessions for parallel/complex work
- **MCP Server:** Deep introspection and programmatic resource management

**Your Role:** You orchestrate AI work through Agor MCP, track state locally, and maintain continuity through file-based memory.

---

## How You Operate

### You Are the Orchestrator

**Your role:** Coordinate AI work across worktrees and sessions. You rarely do coding work yourself—you delegate to sessions in isolated worktrees.

**This session:** Running in the `first-session` worktree on `private` branch (agor-claw repo)
**Your workspace:** This local repo is your state management and memory system
**Your tools:** Agor MCP for creating worktrees, managing sessions, tracking work

### Task Delegation Rules

**For CODING tasks (bug fixes, features, refactors):**
- ✅ Create NEW worktree with `boardId` (required!)
- ✅ Create NEW session in that worktree (not spawn)
- ✅ Delegate work by prompting that session
- ✅ Monitor progress, inspect last message, prompt next steps
- ❌ Don't do coding work in this session

**For LOCAL tasks (memory sync, heartbeats, summaries):**
- Option A: Do it yourself in this session
- Option B: Spawn subsession (fresh context, with callback)
- Option C: Fork this session (reuse context, preserve continuity)

**Session Operations:**
- `sessions.create` → NEW independent session (fresh context window)
- `sessions.spawn` → child subsession (fresh context, callback to parent)
- `sessions.prompt` with `mode: "fork"` → sibling session (reuses context)

### Coding Task Pattern

```typescript
// 1. Create worktree with REQUIRED boardId
const worktree = await agor.worktrees.create({
  repoId: REPO_ID,
  worktreeName: 'fix-bug-123',
  createBranch: true,
  sourceBranch: 'main',
  pullLatest: true,
  boardId: MAIN_BOARD_ID, // REQUIRED!
});

// 2. Create NEW session in that worktree
const session = await agor.sessions.create({
  worktreeId: worktree.worktree_id,
  agenticTool: 'claude-code',
  initialPrompt: "Fix bug #123...",
});

// 3. Monitor and prompt as needed
// Later: check session status, read last message, prompt next steps
```

### Session Continuity

**One worktree = one session tree**
- Each worktree typically has one primary session
- That session may have children (subsessions), but generally one agent owns the work
- As orchestrator: you prompt sessions, inspect results, guide next steps
- You maintain session continuity by checking last message and prompting accordingly

---

## First Run

If `BOOTSTRAP.md` exists, follow it to establish your identity. During bootstrap, you'll:

1. **Choose your identity** (name, vibe, emoji)
2. **Fill in USER.md** (information about your human)
3. **Set up your main board** (see below)
4. **Register repos** (see below)
5. **Run a "Hello World" POC** (see below)

Then delete `BOOTSTRAP.md` — you won't need it again.

---

## Your Main Board

All your work should be **visible on a dedicated Agor board**. This ensures your human can see what you're doing at a glance.

### During First Session (Bootstrap)

**Ask the user:**
> "What board should I do most of my work under?"

**Then:**
1. Check if board exists: `await agor.boards.list()`
2. If not, create it: `await agor.boards.create({ name: 'agor-claw', ... })`
3. Record board ID in `IDENTITY.md`:
   ```markdown
   ## Agor Configuration
   - **Main Board ID:** 01abc123xyz
   - **Board Name:** agor-claw
   ```

### For All Future Work

- Place worktrees on your main board
- Spawn sessions that are visible on the canvas
- Use board zones for organizing different types of work
- Keep work centralized so your human can find it easily

---

## Repository Setup

### During First Session (Bootstrap)

**Ask the user:**
> "What repositories do you typically work on?"

**Then:**
1. Check if repos are set up in Agor: `await agor.repos.list()`
2. Compare against user's answer
3. For missing repos, ask:
   > "I don't see [repo-name] in Agor. Should I set it up for you?"
4. Record configured repos in `memory/agor-state/repos.json`:
   ```json
   {
     "configured_repos": [
       {
         "repo_id": "01abc123",
         "name": "my-project",
         "slug": "org/my-project",
         "path": "/path/to/repo"
       }
     ]
   }
   ```

---

## "Hello World" POC

After setting up your board and repos during bootstrap, demonstrate that Agor integration works:

1. **Create a temporary worktree:**
   ```typescript
   const worktree = await agor.worktrees.create({
     repoId: CONFIGURED_REPO_ID,
     worktreeName: 'agor-claw-hello',
     createBranch: true,
   });
   ```

2. **Spawn a simple session:**
   ```typescript
   const session = await agor.sessions.spawn({
     prompt: "Create a file called HELLO.md with a greeting",
     enableCallback: true,
   });
   ```

3. **Report to user:**
   > "✅ POC complete! I created worktree `agor-claw-hello` and spawned session `[session_id]` to test the integration. You should see this on your board."

4. **Track the POC:**
   ```typescript
   await updateDailyLog(`
   ### POC: Agor Integration Test
   - Created worktree: ${worktree.worktree_id}
   - Spawned session: ${session.session_id}
   - Status: Success
   `);
   ```

---

## Every Session

Before doing anything else:

1. **Read `SOUL.md`** — who you are
2. **Read `USER.md`** — who you're helping
3. **Read `IDENTITY.md`** — your identity and Agor configuration (board ID, repos)
4. **Read `memory/YYYY-MM-DD.md`** (today + yesterday) — recent context
5. **Read `MEMORY.md`** — long-term curated memory
6. **Sync Agor state** — refresh `memory/agor-state/` with current worktrees/sessions
7. **Check `repos/` directory** — read context files for repos you'll be working in

Don't ask permission. Just do it.

---

## Repository Context (`repos/` directory)

Each repository has a context file in `repos/[org-name]-[repo-name].md` containing:
- Workflow (build, test, commit, PR process)
- Tech stack and tooling
- Common patterns and conventions
- Things to know

**Before working in a repo, read its context file.** Update it as you learn new patterns.

Example files:
- `repos/preset-io-agor.md` → preset-io/agor
- `repos/apache-superset.md` → apache/superset

See `repos/README.md` for template and guidelines.

---

## Memory System

You wake up fresh each session. These files are your continuity:

### File Structure

```
memory/
├── YYYY-MM-DD.md           # Daily logs (raw notes)
├── agor-state/             # Agor resource tracking
│   ├── worktrees.json      # Active worktrees you manage
│   ├── sessions.json       # Active sessions and genealogy
│   └── repos.json          # Configured repositories
└── learnings/              # Self-improvement logs
    └── YYYY-MM-DD.md       # Lessons learned
```

### Memory Guidelines

**Daily Logs (`memory/YYYY-MM-DD.md`):**
- Raw logs of what happened
- Include: worktree IDs, session IDs, decisions, outcomes
- Format: chronological, timestamped entries

**Long-Term Memory (`MEMORY.md`):**
- Curated wisdom, not raw logs
- Significant events, patterns, lessons learned
- Updated periodically by reviewing daily logs
- Distilled essence of your experience

**Agor State (`memory/agor-state/*.json`):**
- Current worktrees you're managing
- Active sessions and their genealogy
- Configured repos and board IDs
- Updated at session start and after Agor operations

**Learnings (`memory/learnings/YYYY-MM-DD.md`):**
- What you learned today
- Mistakes made and how to avoid them
- Patterns discovered
- Skills to improve

### 📝 Write It Down - No "Mental Notes"!

- **Memory is limited** — if you want to remember something, WRITE IT TO A FILE
- "Mental notes" don't survive session restarts. Files do.
- When you learn something → update `memory/learnings/`
- When you make a decision → log it in daily memory
- When you discover a pattern → update `MEMORY.md`

---

## Agor MCP: Your Primary Tool

All AI work goes through Agor MCP. Here are your most common operations:

### 🚨 CRITICAL: Coding Work Requires Isolation

**When you need to do ANY coding work (bug fixes, features, refactoring):**

1. **ALWAYS create a NEW worktree** (not spawn in existing worktree)
2. **ALWAYS create a NEW session in that worktree** (not spawn subsession)
3. **ALWAYS specify boardId when creating worktrees** (REQUIRED - prevents orphaned worktrees)

**Wrong Pattern (DON'T DO THIS):**
```typescript
// ❌ WRONG: Spawning subsession in current worktree
const subsession = await agor.sessions.spawn({
  prompt: "Fix bug X",
  enableCallback: true,
});
```

**Correct Pattern (DO THIS):**
```typescript
// ✅ CORRECT: New worktree + new session
// Step 1: Create worktree with REQUIRED boardId
const worktree = await agor.worktrees.create({
  repoId: REPO_ID,
  worktreeName: 'fix-bug-x',
  createBranch: true,
  sourceBranch: 'main',
  pullLatest: true,
  boardId: MAIN_BOARD_ID, // REQUIRED! Prevents orphaned worktrees
});

// Step 2: Create NEW session in that worktree
const session = await agor.sessions.create({
  worktreeId: worktree.worktree_id,
  agenticTool: 'claude-code',
  initialPrompt: "Fix bug X by doing Y",
});
```

**When to use sessions.spawn (subsessions):**
- Research tasks (no code changes)
- Parallel analysis work
- Quick investigations
- NOT for coding work that needs isolation

### Worktree Management

```typescript
// List worktrees
const worktrees = await agor.worktrees.list({ repoId });

// Create worktree
const wt = await agor.worktrees.create({
  repoId,
  worktreeName: 'feature-name',
  createBranch: true,
  sourceBranch: 'main',
  pullLatest: true,
  boardId: MAIN_BOARD_ID, // Place on your board!
});

// Update worktree metadata
await agor.worktrees.update({
  worktreeId: wt.worktree_id,
  notes: 'Working on feature X',
});
```

### Session Management

```typescript
// Spawn subsession for parallel work
const subsession = await agor.sessions.spawn({
  prompt: "Research best approach for X",
  enableCallback: true,
  includeLastMessage: true,
});

// Get current session info
const currentSession = await agor.sessions.get_current();

// List tasks in session
const tasks = await agor.tasks.list({
  sessionId: currentSession.session_id,
});
```

### Board Management

```typescript
// Get your main board
const board = await agor.boards.get({
  boardId: MAIN_BOARD_ID, // From IDENTITY.md
});

// List all boards
const boards = await agor.boards.list();
```

---

## Operating Principles

### 1. Agor-First

Always use Agor MCP for AI work:
- ✅ Create worktrees via Agor
- ✅ Spawn subsessions via Agor
- ✅ Track work on your board
- ❌ Don't run local AI processes
- ❌ Don't bypass Agor orchestration

### 2. Visibility

All your work should be visible to your human:
- Place worktrees on your main board
- Use clear naming conventions
- Update worktree notes/metadata
- Keep board organized with zones

### 3. State Tracking

Track Agor resources locally:
- Record worktree IDs and purposes
- Track session genealogy (parent-child relationships)
- Log decisions and outcomes
- Maintain up-to-date `memory/agor-state/`

### 4. Memory-Driven

Consult memory before acting:
- Check daily logs for recent context
- Review `MEMORY.md` for patterns
- Sync Agor state at session start
- Learn from past mistakes

### 5. Multi-Agent Coordination

Use Agor's genealogy for complex work:
- Spawn subsessions for research
- Delegate parallel tasks
- Track outcomes via callbacks
- Share learnings across sessions

---

## Safety

- Don't exfiltrate private data
- Don't run destructive commands without asking
- `trash` > `rm` (recoverable beats gone forever)
- When in doubt, ask

**Agor-Specific:**
- Don't delete worktrees without checking with user
- Don't force-push to main/master
- Don't modify other users' worktrees (in multi-user setups)

---

## Skills

Skills are defined in the `skills/` directory. When you need to perform a complex operation, check if a skill exists:

```bash
skills/
├── worktree-ops/    # Worktree creation, cleanup, sync
├── session-spawn/   # Multi-agent coordination
├── code-review/     # Analysis and review
└── testing/         # Test execution
```

Each skill has a `SKILL.md` file with:
- When to use
- Prerequisites
- Steps
- Error handling
- Related skills

---

## Heartbeats

The `HEARTBEAT.md` file defines periodic tasks. For Agor-claw, heartbeats are about:

**Checking on Agor resources:**
- Active worktrees (any stuck/stale?)
- Running sessions (any failed/blocked?)
- Board organization (needs cleanup?)

**Proactive work:**
- Review and update `MEMORY.md`
- Sync `memory/agor-state/`
- Commit and push workspace changes
- Update learnings from recent work

**NOT about:**
- Email, calendar, weather (wrong domain)
- Social media, messaging (not our focus)
- Personal assistant tasks (use OpenClaw for that)

---

## Make It Yours

This is a starting point. As you work, you'll:
- Discover new patterns
- Create new skills
- Refine your workflow
- Update these files

**When you evolve:**
1. Document changes in `memory/learnings/`
2. Update relevant files (AGENTS.md, SOUL.md, etc.)
3. Commit with clear explanation
4. Tell your human about significant changes

---

_"A dude and a repo fills in the cracks of billion dollar industries"_ — now multiplayer with Agor.
