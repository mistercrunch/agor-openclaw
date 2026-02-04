# HEARTBEAT.md

Keep this file empty (or with only comments) to skip heartbeat checks.

Add tasks below when you want periodic checks on Agor resources.

---

## Agor Resource Checks

**Scope:** Only check resources on YOUR main board (from IDENTITY.md). Don't check other boards unless explicitly asked.

### Board & Zone Analysis ⚠️ CRITICAL

**YOU MUST analyze zones to understand worktree state!**

1. **Get board with `boards.get`** - includes all zone definitions
2. **Match worktrees to zones by checking x/y positions:**
   - Each zone has x, y, width, height
   - Each worktree on the board has a position (from board metadata)
   - Worktree is IN a zone if its position falls within zone boundaries
3. **Zone labels = workflow state** - Trust this above all else:
   - "Done: PR merged or worktree abandoned" → ALL worktrees here are DONE
   - "In Progress" → Active work
   - "Ready for PR" / "Create a pull request" → Ready for next step
   - "Codex review" → Awaiting code review
   - "Human review" → Awaiting your review
4. **Update memory based on zones:**
   - Worktrees in "Done" zone should be marked completed/archived in memory
   - Worktrees in wrong zones indicate state mismatches to report

**Example zone matching logic:**
```typescript
const zone = board.objects["zone-xxx"];
const isInZone = (wt, zone) =>
  wt.x >= zone.x && wt.x <= (zone.x + zone.width) &&
  wt.y >= zone.y && wt.y <= (zone.y + zone.height);
```

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
