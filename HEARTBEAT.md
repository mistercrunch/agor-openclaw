# HEARTBEAT.md

Keep this file empty (or with only comments) to skip heartbeat checks.

Add tasks below when you want periodic checks on Agor resources.

---

## Agor Resource Checks

**Scope:** Only check resources on YOUR main board (from IDENTITY.md). Don't check other boards unless explicitly asked.

### Board & Zone Analysis ⚠️ CRITICAL

**Read `BOARD.md` first** to understand zone meanings and workflow states.

**Then execute this workflow for each worktree on your board:**

```
1. Get board ID from IDENTITY.md
2. List all worktrees: agor_worktrees_list (includes zone_id, zone_label automatically)
3. Filter to your board: where board_id === MAIN_BOARD_ID

FOR EACH worktree on your board:

  IF zone_label = "Done: PR merged or worktree abandoned":
    → Skip (don't report, reduce noise)

  IF zone_label = "MAIN SESH":
    → Check health, ensure orchestrator is running properly

  IF zone_label = "Agor Coding Tasks":
    → Get session for worktree (agor_sessions_get using worktree_id)
    → Check session.status and last_message
    → IF session is blocked/stuck:
      → Review why (check last_message for errors, questions)
      → IF unblockable without human: Flag for attention
      → IF simple unblock (e.g., "keep going"): Prompt session to continue
    → IF session looks done coding (last_message indicates completion):
      → Move to "Create a pull request" zone WITH trigger:
        agor_worktrees_set_zone(worktreeId, zone-1770152350171, triggerTemplate=true, targetSessionId)
    → IF pull_request_url exists:
      → Check PR status: gh pr view <url> --json state,mergeable,reviews
      → IF merged: Move to "Done" zone immediately
      → IF has requested changes: Flag or prompt session to address

  IF zone_label = "Create a pull request":
    → Get session last_message
    → IF no pull_request_url on worktree yet:
      → Check if session is working on it (review last_message)
      → IF stuck: May need to prompt or flag
    → IF pull_request_url exists:
      → Check PR: gh pr view <url> --json state,mergeable,reviews
      → IF merged/closed: Move to "Done" zone
      → IF ready for review: Decide Codex or Human
        → Complex/risky code: Move to "Codex review" WITH trigger
        → Simple/confident: Move to "Human review" (no trigger)

  IF zone_label = "Codex review":
    → Get session last_message
    → Check if Codex subsession callback received
    → IF callback received with feedback:
      → Check if session addressed feedback
      → IF needs prompting: Prompt with "address Codex feedback worth addressing"
    → IF review complete and addressed:
      → Move to "Human review" zone (no trigger, terminal state)

  IF zone_label = "Human review":
    → MUST check PR status: gh pr view <pull_request_url> --json state,mergeable,reviews,comments
    → IF merged: Move to "Done" zone IMMEDIATELY
    → IF closed (not merged): Move to "Done" zone, note abandoned
    → IF requested changes: Flag for attention, may need to move back to "Agor Coding Tasks"
    → IF new comments from human: Flag for attention
    → IF approved but not merged: Just waiting, do nothing

END FOR EACH
```

**Key insight:** Zones encode workflow state. Trust `zone_label` as source of truth. **Execute actions based on zone state.**

### Active Worktrees (on your board)
**See pseudocode above for detailed workflow by zone.**

Additional checks:
- Flag stale worktrees (no activity in >7 days) in "Agor Coding Tasks"
- Identify worktrees with `needs_attention` flag
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
