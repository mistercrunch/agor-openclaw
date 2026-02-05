# HEARTBEAT.md

Keep this file empty (or with only comments) to skip heartbeat checks.

Add tasks below when you want periodic checks on Agor resources.

---

## Agor Resource Checks

**Scope:** Only check resources on YOUR main board (from IDENTITY.md). Don't check other boards unless explicitly asked.

### Board & Zone Analysis ⚠️ CRITICAL

**Read `BOARD.md` first** to understand zone meanings and workflow states.

Zone information is now **directly available** in worktree responses (no position calculations needed):

```
Get your main board ID from IDENTITY.md, then:

1. List all worktrees (use agor_worktrees_list)
   - Each worktree includes: zone_id, zone_label, board_id

2. Filter to your board (check board_id === MAIN_BOARD_ID)

3. Check zone_label for each worktree:
   - "Done: PR merged or worktree abandoned" → Mark completed, archive
   - "Open a PR" + no pull_request_url → Create PR
   - "In Progress" + stale last_updated → Flag as stale
   - "Design!" → Still planning, don't expect code yet
   - "Codex review" / "Human review" → In review, check PR status
```

**Key insight:** Zones encode workflow state. Trust `zone_label` as source of truth.

### Active Worktrees (on your board)
- Check for stale worktrees (no activity in >7 days)
- Identify worktrees with failed CI/CD or `needs_attention` flag
- Verify worktrees are in appropriate zones based on actual state
- Detect mismatches (e.g., PR merged but still in "In Progress" zone)
- **Follow zone-specific behaviors defined in BOARD.md**
- **When worktree has pull_request_url:** Check PR status with `gh pr view <url>` and follow zone instructions
- **Ignore worktrees on other boards** - not your responsibility

### Running Sessions (on your board)
- Check for blocked/stuck sessions
- Review failed tasks needing attention
- Identify sessions waiting for callbacks
- Track session genealogy for complex workflows

### Board Organization
- Review zone usage and organization
- Move completed worktrees to appropriate zones using agor_worktrees_set_zone
- Update worktree notes/metadata if stale
- Ensure spatial organization reflects actual work state

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

**Board and zone information:**
- `agor_boards_get` - Get board with zones (requires: boardId)
- Returns board.objects array with zone definitions

**Worktree operations:**
- `agor_worktrees_list` - List all worktrees (zone_id and zone_label included automatically)
- `agor_worktrees_get` - Get specific worktree with zone info (requires: worktreeId)
- `agor_worktrees_set_zone` - Move worktree to zone (requires: worktreeId, zoneId)
- `agor_worktrees_update` - Update metadata (requires: worktreeId, optional: notes, issueUrl, pullRequestUrl)

**Session operations:**
- `agor_sessions_list` - List sessions (optional: worktreeId filter)
- `agor_sessions_get_current` - Get your current session info
- `agor_tasks_list` - List tasks in a session (requires: sessionId)

**GitHub integration (when available):**
- `gh pr view <url>` - View PR details, status, and recent comments
- `gh pr checks <url>` - Check CI/CD status
- `gh pr view <url> --json comments` - Get recent PR comments for analysis
- Useful when worktree has pull_request_url field

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
