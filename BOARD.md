# BOARD.md - Agor Claw Experiment

**Purpose:** Document board structure, zones, and workflow expectations so agents understand how to organize work spatially.

---

## Board Information

- **Board ID:** `9833703b-b9e6-4b7b-b0eb-70f783fba715`
- **Board Name:** Agor Claw Experiment
- **Board URL:** https://agor.live/board/9833703b-b9e6-4b7b-b0eb-70f783fba715
- **Icon:** 👨‍🔬
- **Background:** Gradient (fire orange/red theme)

---

## Zones and Workflow

**Why zones matter:** Zones represent workflow states. The zone a worktree is IN determines its true status—this is your spatial organization of work.

### MAIN SESH

- **Zone ID:** zone-1770143185072
- **Position:** (-460, 220) to (815, 943)
- **Purpose:** Primary orchestrator workspace
- **Workflow State:** control-center
- **Agent Behavior:**
  - This zone contains the main orchestrator session (first-session worktree)
  - Memory management, heartbeats, and meta-work happens here
  - Don't clutter this zone with coding tasks—it's for orchestration only
  - Worktrees here are typically long-lived and not feature-specific

**Zone Trigger:** None (manual work only)

---

### Agor Coding Tasks

- **Zone ID:** zone-1770144681071
- **Position:** (-460, 980) to (195, 2841)
- **Purpose:** Active development work on Agor features
- **Workflow State:** in-progress
- **⭐ START HERE:** All new coding tasks begin in this zone
- **Agent Behavior:**
  - **New worktrees:** Place all coding tasks here initially (use `worktrees_set_zone` after creation)
  - Worktrees here are actively being worked on
  - **Check PR status during heartbeats:**
    - If pull_request_url exists, use `gh pr view <url>` to check state
    - PR merged → Move to "Done" zone
    - PR has requested changes → This is correct zone, address feedback
    - PR failing CI → Check what's broken, may need fixes
  - Check for staleness: flag if no activity in 7+ days
  - When work is complete and ready for PR creation, move to "Create a pull request" zone
  - If abandoned or blocked, move to "Done" zone with notes

**Zone Trigger:** None (manual work)

**Current worktrees:**
- fix-mcp-session-start → PR #533 (may be done)
- agor-mcp-session-defaults → PR #535 (may be done)
- agor-zone-pinning → PR #538 (may be done)
- add-zone-info-to-worktree-mcp → active work

---

### Create a pull request

- **Zone ID:** zone-1770152350171
- **Position:** (240, 1000) to (825, 2838)
- **Purpose:** Work ready for pull request creation
- **Workflow State:** ready-for-pr
- **Agent Behavior:**
  - Run `pnpm install && pnpm check` to verify build
  - Commit any uncommitted changes
  - Create PR if not exists
  - Attach PR URL to worktree metadata using `worktrees.update`
  - After PR created, decide: needs Codex review OR skip to Human review
  - **Decision point:** Use judgment on code complexity:
    - Simple/trivial changes → skip Codex, go straight to Human review
    - Complex logic, security-sensitive, or risky changes → Codex review first
  - Move worktree to next zone when done

**Zone Trigger:** show_picker
```
NEAT! now let's
- pnpm install
- pnpm check
- commit
- open a PR
- attach the new PR to the worktree using Agor MCP

Make a todo list so i can keep track of progress
```

---

### Codex review

- **Zone ID:** zone-1770152375353
- **Position:** (860, 1000) to (1462, 2834)
- **Purpose:** Automated code review by Codex agent
- **Workflow State:** ai-review
- **Agent Behavior:**
  - **IMPORTANT:** This zone is OPTIONAL—use judgment!
  - **When to use Codex review:**
    - Complex algorithmic changes
    - Security-sensitive code (auth, permissions, SQL)
    - Large refactors or architectural changes
    - Performance-critical paths
    - Unfamiliar codebase areas
  - **When to SKIP Codex review:**
    - Trivial changes (typos, formatting, simple renames)
    - Documentation-only changes
    - Small bug fixes with obvious solutions
    - Changes we're very confident about
  - **If using Codex review:**
    - Spawn Codex subsession with branch and PR context
    - Enable callback to get results
    - Wait for review completion before moving to Human review
  - **If skipping:**
    - Move directly to "Human review" zone
    - Add note explaining why review was skipped
  - After review (or skip decision), move to Human review

**Zone Trigger:** show_picker
```
Can you please spawn a Codex subsession to review our code?

- point the the branch: {{ worktree.name }}
- point to the PR: {{ worktree.pull_request_url }}

Pass in the context you think is more relevant to getting a good code review on our effort here. Make sure to set the subsession to callback when done with the results (that's an MCP tool parameter)!
```

---

### Human review

- **Zone ID:** zone-1770152859108
- **Position:** (1520, 1000) to (2410, 2826)
- **Purpose:** Awaiting human review and merge
- **Workflow State:** terminal (agent waits)
- **Agent Behavior:**
  - This is a terminal state for automated agents
  - Don't take automated actions on worktrees here
  - Mark as "awaiting human" in memory
  - **Check PR status during heartbeats:**
    - Use `gh pr view <pull_request_url>` to check state
    - If PR merged → Move to "Done" zone immediately
    - If PR has requested changes → Flag for attention, may need to move back to "Agor Coding Tasks"
    - If PR has failing CI → Note in heartbeat report
    - If PR approved but not merged → Just waiting
  - Only modify worktrees if PR status changes
  - After PR merged, automatically move to "Done" zone

**Zone Trigger:** None (human action required)

---

### Done: PR merged or worktree abandoned

- **Zone ID:** zone-1770155449351
- **Position:** (-460, 2940) to (1173, 4429)
- **Purpose:** Completed or abandoned work
- **Workflow State:** done
- **Agent Behavior:**
  - Mark as completed in memory
  - Archive from active tracking
  - Don't report in heartbeat checks (noise reduction)
  - Clean up stale worktrees if filesystem_status is not "ready"
  - These worktrees are historical record—don't modify unless asked
  - If PR was merged, celebrate! 🎉
  - If abandoned, record why in memory for learning

**Zone Trigger:** None (terminal state)

---

## Workflow Transitions

```
┌──────────────┐
│  MAIN SESH   │  (Orchestrator control center)
└──────────────┘

┌──────────────────┐
│ Agor Coding Tasks│  (Active development)
└─────────┬────────┘
          │
          ▼
┌──────────────────────┐
│ Create a pull request│  (Finalize & create PR)
└─────────┬────────────┘
          │
          ├─────────────┐
          │             │
          ▼             ▼
    ┌──────────┐   ┌────────────────┐
    │  Codex   │   │ Human review   │  (Skip Codex if simple)
    │  review  │   │                │
    └────┬─────┘   └────────────────┘
         │                  ▲
         └──────────────────┘
                    │
                    ▼
          ┌─────────────────┐
          │      Done       │  (Merged or abandoned)
          └─────────────────┘
```

**Key decision point:** After "Create a pull request", use judgment to either:
- → Codex review (complex/risky code)
- → Human review (simple/confident changes)

---

## Agent Instructions

### For Heartbeat Checks

1. **Load board state:**
   ```typescript
   const board = await agor.boards.get({ boardId: MAIN_BOARD_ID });
   ```

2. **Get worktrees:**
   ```typescript
   const worktrees = await agor.worktrees.list();
   const myWorktrees = worktrees.data.filter(wt => wt.board_id === MAIN_BOARD_ID);
   ```

3. **Match worktrees to zones:**
   ```typescript
   const isInZone = (wt, zone) =>
     wt.x >= zone.x && wt.x <= (zone.x + zone.width) &&
     wt.y >= zone.y && wt.y <= (zone.y + zone.height);

   myWorktrees.forEach(wt => {
     const zone = Object.values(board.objects).find(z =>
       z.type === 'zone' && isInZone(wt, z)
     );
     // Take action based on zone.label
   });
   ```

4. **Zone-based actions:**
   - **"Agor Coding Tasks"**: Check for staleness (7+ days no activity)
   - **"Create a pull request"**: Check if PR exists, if not flag
   - **"Codex review"**: Check if review completed
   - **"Human review"**: Just report status, don't modify
   - **"Done"**: Don't report (noise reduction)
   - **"MAIN SESH"**: Check health of orchestrator

5. **Trust zone labels as source of truth for workflow state**

### For New Work

1. **Create worktree with boardId:**
   ```typescript
   const wt = await agor.worktrees.create({
     repoId,
     worktreeName: 'feature-name',
     boardId: MAIN_BOARD_ID, // REQUIRED!
     createBranch: true,
     sourceBranch: 'main',
   });
   ```

2. **Move to starting zone immediately:**
   - **ALL coding tasks:** → "Agor Coding Tasks" zone (zone-1770144681071)
   - **Orchestration/meta work:** → "MAIN SESH" zone (zone-1770143185072)
   ```typescript
   await agor.worktrees_set_zone({
     worktreeId: wt.worktree_id,
     zoneId: 'zone-1770144681071', // Agor Coding Tasks
   });
   ```

3. **Move between zones as work progresses through workflow**

---

## Notes

- **Codex review is a judgment call, not mandatory**
- Simple changes can skip straight to Human review
- Use your discretion based on code complexity and risk
- When in doubt, err on the side of Codex review for safety
- Document skip decisions in worktree notes for learning

---

**Last Updated:** 2026-02-04
**Board Owner:** Max (agorpg)
