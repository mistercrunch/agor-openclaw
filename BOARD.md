# BOARD.md - Your Agor Board Configuration

**Purpose:** Document your board structure, zones, and workflow expectations so agents understand how to organize work spatially.

---

## Board Information

- **Board ID:** `[Your board ID from IDENTITY.md]`
- **Board Name:** `[Your board name]`
- **Board URL:** `[Agor board URL]`

---

## Zones and Workflow

**Why zones matter:** Zones represent workflow states. The zone a worktree is IN determines its true status—this is your spatial organization of work.

### Zone Definitions

Document each zone on your board:

```markdown
### [Zone Name]

- **Zone ID:** zone-xxxxx
- **Purpose:** [What this zone represents]
- **Workflow State:** [e.g., "planning", "in-progress", "review", "done"]
- **Agent Behavior:**
  - [What should agents do when worktrees are in this zone?]
  - [Any automated actions to take?]
  - [When to move worktrees out of this zone?]

**Zone Trigger:** [If configured]
- Behavior: [always_new / show_picker]
- Agent: [claude-code / codex / gemini]
- Prompt Template: [What prompt gets triggered]
```

---

## Example Zone Configuration

### Design!

- **Zone ID:** zone-1770152859108
- **Purpose:** Planning and design phase before implementation
- **Workflow State:** planning
- **Agent Behavior:**
  - Focus on research, exploration, and design decisions
  - Don't start coding until design is approved
  - Update worktree notes with design decisions
  - Move to "In Progress" when ready to implement

### In Progress

- **Zone ID:** zone-1770152859362
- **Purpose:** Active development work
- **Workflow State:** in-progress
- **Agent Behavior:**
  - Actively work on implementation
  - Run tests frequently
  - Update worktree metadata with progress
  - Move to "Open a PR" when work is complete

### Open a PR

- **Zone ID:** zone-1770152859410
- **Purpose:** Work ready for pull request creation
- **Workflow State:** ready-for-pr
- **Agent Behavior:**
  - Create pull request if not exists
  - Link PR URL to worktree metadata
  - Ensure CI passes
  - Move to "Codex review" or "Human review" after PR created

### Codex review

- **Zone ID:** zone-1770152859458
- **Purpose:** Code review by AI agent
- **Workflow State:** ai-review
- **Agent Behavior:**
  - Spawn code review session
  - Check for common issues
  - Update PR with review comments
  - Move to "Human review" after AI review complete

### Human review / Trash

- **Zone ID:** zone-1770152859506
- **Purpose:** Terminal states (awaiting human or discarded)
- **Workflow State:** terminal
- **Agent Behavior:**
  - Don't take automated actions
  - Mark as completed/archived in memory
  - Only touch if explicitly asked by human

### Done: PR merged or worktree abandoned

- **Zone ID:** zone-1770152859554
- **Purpose:** Completed work
- **Workflow State:** done
- **Agent Behavior:**
  - Mark as completed in memory
  - Archive from active tracking
  - Clean up if worktree is stale
  - Don't report in heartbeat checks

---

## Workflow Transitions

Document how worktrees move between zones:

```
Design! → In Progress → Open a PR → Codex review → Human review → Done
                ↓                          ↓
              Trash ←──────────────────────┘
```

---

## Agent Instructions

**For heartbeat checks:**

Zone information is now **directly available** in worktree MCP responses as `zone_id` and `zone_label` fields (no position calculations needed):

```typescript
// Get worktrees with zone info included
const worktrees = await agor.worktrees.list();

// Zone fields are already there!
worktrees.data.forEach(wt => {
  console.log(`${wt.name}: ${wt.zone_label}`); // e.g., "Done: PR merged"

  // Take actions based on zone
  if (wt.zone_label === "Done: PR merged or worktree abandoned") {
    markCompleted(wt);
  } else if (wt.zone_label === "Ready for PR" && !wt.pull_request_url) {
    createPR(wt);
  } else if (wt.zone_label === "In Progress" && isStale(wt.last_updated)) {
    flagAsStale(wt);
  }
});
```

**Key points:**
- Zone info is automatically included in `worktrees.get` and `worktrees.list`
- No need to manually match positions or call `boards.get`
- Trust `zone_label` as source of truth for workflow state
- Use `worktrees.set_zone` to move worktrees between zones

**For new work:**
1. Create worktree with required `boardId`
2. Use `worktrees.set_zone` to place in appropriate starting zone
3. Zone triggers may auto-start sessions (if configured)

---

## Setup Notes

**During bootstrap:**
- Agent should help set up board structure
- Create zones for common workflow states
- Configure zone triggers if desired
- Document zone purposes in this file

**As you evolve:**
- Update zone definitions when workflow changes
- Add new zones as needed
- Document agent behavior expectations
- Keep this file synchronized with actual board

---

## Notes

- This file should be updated whenever board structure changes
- Zone IDs are stable, but positions/sizes may change
- Agent behavior should adapt to your workflow, not dictate it
- Empty template by default—fill in during bootstrap or as needed
