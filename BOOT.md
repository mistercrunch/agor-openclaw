# BOOT.md

Add short, explicit instructions for what the agent should do on startup.

---

## Agor-Aware Startup Tasks

Examples of what to include:

```markdown
## On Every Startup

- [ ] Sync Agor state: refresh worktrees.json and sessions.json
- [ ] Check for stuck/failed sessions on main board
- [ ] Review yesterday's memory log (memory/YYYY-MM-DD.md)
- [ ] Commit workspace changes if any files modified
```

---

## Guidelines

- **Keep it minimal** - This runs every time the agent starts
- **Be specific** - "Check X" not "look around"
- **Focus on Agor** - Sync state, monitor resources, update memory
- **Quick wins** - Tasks that take <30 seconds

---

## When to Use

Use BOOT.md when you want consistent startup behavior. Many agents work fine without it (reactive mode). Use it if you need:
- Regular state synchronization
- Proactive monitoring
- Automatic workspace maintenance

---

**Note:** This file is optional. Delete or leave empty to skip boot tasks.
