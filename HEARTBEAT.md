# HEARTBEAT.md

Keep this file empty (or with only comments) to skip heartbeat checks.

Add tasks below when you want periodic checks on Agor resources.

---

## Agor Resource Checks

**Scope: Main board only** - Only check resources on YOUR main board (from `IDENTITY.md`). Don't check other users' boards unless explicitly asked.

### Board & Zone Analysis

**Read `BOARD.md` first** to understand zone meanings and workflow states.

Zone information is now **directly available** in worktree responses (no position calculations needed):

```typescript
// Get your main board ID from IDENTITY.md
const MAIN_BOARD_ID = '[from IDENTITY.md]';

// List worktrees on your board - zone_id and zone_label are included!
const worktrees = await agor.worktrees.list();
const myWorktrees = worktrees.data.filter(wt => wt.board_id === MAIN_BOARD_ID);

// Zone fields are directly available
myWorktrees.forEach(wt => {
  console.log(`${wt.name}: ${wt.zone_label || 'no zone'}`);

  // Take actions based on zone
  if (wt.zone_label === 'Done: PR merged or worktree abandoned') {
    // Mark completed, archive from tracking
  } else if (wt.zone_label === 'Open a PR' && !wt.pull_request_url) {
    // Ready for PR but none created yet
  } else if (wt.zone_label === 'In Progress' && isStale(wt)) {
    // Active work but no recent activity
  }
});
```

**Key insight:** Zones encode workflow state. Trust `zone_label` as source of truth.

### Active Worktrees (on main board)
- Check for stale worktrees (no activity in >7 days)
- Identify worktrees with failed CI/CD
- Look for completed work that can be cleaned up
- Verify worktrees are in appropriate zones
- Detect mismatches (e.g., completed work in "In Progress" zone)

### Running Sessions (on main board)
- Check for blocked/stuck sessions
- Review failed tasks needing attention
- Identify sessions waiting for callbacks
- Track session genealogy for complex workflows

### Board Organization
- Review zone usage and organization
- Move completed worktrees to appropriate zones
- Update worktree notes/metadata if stale
- Use `worktrees.set_zone` to organize work

---

## Memory Maintenance

### Periodic Tasks
- Review recent daily logs (`memory/YYYY-MM-DD.md`)
- Update `MEMORY.md` with significant learnings
- Sync `memory/agor-state/` with current Agor state
- Commit workspace changes if modified

---

## Available MCP Tools

Use these Agor MCP tools for heartbeat checks:

```typescript
// Get board information with zones
const board = await agor.boards.get({ boardId: MAIN_BOARD_ID });

// List worktrees (zone_id and zone_label included automatically)
const worktrees = await agor.worktrees.list();

// Get specific worktree with zone info
const wt = await agor.worktrees.get({ worktreeId });

// Move worktree to zone
await agor.worktrees.set_zone({
  worktreeId,
  zoneId: 'zone-1234567890',
});

// Update worktree metadata
await agor.worktrees.update({
  worktreeId,
  notes: 'Updated status',
});

// List sessions on board
const sessions = await agor.sessions.list({ boardId: MAIN_BOARD_ID });

// Get current session
const currentSession = await agor.sessions.get_current();
```

---

## Example Heartbeat Tasks

```markdown
## Daily Checks (if enabled)

- [ ] Sync Agor state: refresh worktrees.json and sessions.json
- [ ] Review yesterday's log, extract learnings to MEMORY.md
- [ ] Check for stuck/failed sessions on main board
- [ ] Commit workspace changes if any

## Weekly Checks (if enabled)

- [ ] Review all active worktrees, identify cleanup candidates
- [ ] Archive old daily logs (keep last 30 days)
- [ ] Review MEMORY.md, remove outdated information
- [ ] Update skills based on learnings
```

---

**Note:** Heartbeats are optional. Many agents work better in reactive mode (human-initiated). Use heartbeats if you need proactive monitoring of Agor resources.
