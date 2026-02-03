# Agor-Claw Readiness Checklist

**Goal:** Ensure all template files are Agorified and ready for first agent bootstrap.

---

## File Status

### ✅ Agorified (Ready)

- [x] **AGENTS.md** - Fully Agorified, Agor-native operations documented
- [x] **BOOTSTRAP.md** - Includes board setup, repo registration, POC workflow
- [x] **TOOLS.md** - Agor MCP shortcuts, no personal assistant content
- [x] **HEARTBEAT.md** - Agor resource monitoring, no email/calendar
- [x] **README.md** - Explains project and branch strategy
- [x] **SETUP.md** - Setup guide with branch workflow

### 🔄 Needs Minor Updates

- [ ] **IDENTITY.md** - Add Agor Configuration section template
- [ ] **SOUL.md** - Review for any personal assistant assumptions (looks clean)
- [ ] **USER.md** - Looks generic, but verify no assumptions
- [ ] **BOOT.md** - Make Agor-aware (currently OpenClaw-specific)

### 📦 Additional Files Needed

- [ ] **memory/agor-state/repos.json** - Template structure
- [ ] **memory/agor-state/worktrees.json** - Template structure
- [ ] **memory/agor-state/sessions.json** - Template structure
- [ ] **.gitignore** - Already exists, verify covers agor-state/

### ❓ Optional / To Decide

- [ ] **skills/** directory - Do we create example skills now or later?
- [ ] **.dev.md files** - Keep as alternative templates or remove for simplicity?

---

## Quick Wins Needed

### 1. Update IDENTITY.md
Add Agor Configuration section to template:
```markdown
## Agor Configuration

- **Main Board ID:** [to be filled during bootstrap]
- **Main Board Name:** [to be filled during bootstrap]
- **Board URL:** https://agor.live/board/[board_id]
```

### 2. Update BOOT.md
Change from OpenClaw hooks to Agor-aware startup:
```markdown
# BOOT.md

Add short, explicit instructions for what the agent should do on startup.

Examples:
- Sync Agor state (refresh worktrees.json, sessions.json)
- Check for stuck/failed sessions on main board
- Review yesterday's memory log
- Commit workspace changes if any

Keep it minimal. This runs every time the agent starts.
```

### 3. Create memory/agor-state/ Templates
Create empty JSON templates so directory structure exists:
- `repos.json.template`
- `worktrees.json.template`
- `sessions.json.template`

---

## Testing Plan (After Readiness)

1. **Clone in Agor** - Set up worktree for agor-claw in Agor
2. **Start Agent Session** - Launch agent with BOOTSTRAP.md present
3. **Watch Bootstrap** - Observe:
   - Does it understand Agor MCP?
   - Can it list boards?
   - Can it create a worktree?
   - Can it spawn a session?
   - Does POC work?
4. **Analyze Struggles** - Note where agent gets confused
5. **Iterate on `init`** - Fix templates based on findings
6. **Merge to `main`** - Update personal branch with improvements

---

## Estimated Time to Ready

- IDENTITY.md update: 2 min
- BOOT.md update: 3 min
- memory/agor-state/ templates: 5 min
- Final review: 5 min

**Total: ~15 minutes to first boot readiness**

---

## Post-Bootstrap Improvements (Later)

These can wait until after first agent run:

- Create example skills (worktree-ops, session-spawn)
- Add more detailed Agor MCP examples
- Create troubleshooting guide
- Document common patterns discovered during use
