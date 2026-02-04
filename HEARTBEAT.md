# HEARTBEAT.md

Keep this file empty (or with only comments) to skip heartbeat checks.

Add tasks below when you want periodic checks on Agor resources.

---

## Agor Resource Checks

**Scope:** Only check resources on YOUR main board (from IDENTITY.md). Don't check other boards unless explicitly asked.

### Board & Zone Analysis
- **Read board zones FIRST** - zone names are highly informative about workflow state
- Check which zones have worktrees (e.g., "In Progress", "Ready for PR", "Done")
- Zones tell you user's perception of work status - trust the spatial organization
- Look for worktrees in wrong zones (e.g., completed work still in "In Progress")

### Active Worktrees (on your board)
- Check for stale worktrees (no activity in >7 days)
- Identify worktrees with failed CI/CD or `needs_attention` flag
- Look for completed work that can be moved to archive/done zone
- **Ignore worktrees on other boards** - not your responsibility

### Running Sessions (on your board)
- Check for blocked/stuck sessions
- Review failed tasks needing attention
- Identify sessions waiting for callbacks

---

## Memory Maintenance

### Periodic Tasks
- Review recent daily logs (`memory/YYYY-MM-DD.md`)
- Update `MEMORY.md` with significant learnings
- Sync `memory/agor-state/` with current Agor state
- Commit workspace changes if modified

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
