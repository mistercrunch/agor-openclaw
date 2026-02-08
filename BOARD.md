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

**Board Layout:**
- **Horizontal flow (left-to-right):** Main workflow for all tasks - MAIN SESH → Agor Coding Tasks → Create PR → Codex Review → Human Review → Done
- **Vertical flow (GitHub issues):** Analyze GitHub Issue (top) → Implement the Plan (bottom) → joins horizontal flow at "Create PR"

**Two entry points:**
1. **External prompts/direct tasks:** Start at "Agor Coding Tasks" (horizontal flow)
2. **GitHub issue-driven work:** Start at "Analyze GitHub Issue" (vertical flow)

## Horizontal Flow Zones (Main Workflow)

### MAIN SESH

- **Zone ID:** zone-1770143185072
- **Position:** (-1200, 200) to (75, 923)
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
- **Position:** (-1200, 980) to (227.5, 1820)
- **Purpose:** Active development work on Agor features (externally prompted tasks)
- **Workflow State:** in-progress
- **⭐ START HERE for external/direct tasks:** Tasks that don't originate from GitHub issues
- **Agent Behavior:**
  - **Entry point for:** Direct requests, bug reports via chat, feature ideas, refactors
  - Worktrees here are actively being worked on
  - **Check PR status during heartbeats:**
    - If pull_request_url exists, use `gh pr view <url>` to check state
    - PR merged → Move to "Done" zone
    - PR has requested changes → This is correct zone, address feedback
    - PR failing CI → Check what's broken, may need fixes
  - Check for staleness: flag if no activity in 7+ days
  - When work is complete and ready for PR creation, move to "Create a pull request" zone
  - If abandoned or blocked, move to "Abandoned" zone with notes explaining why

**Zone Trigger:** None (manual work)

---

## Vertical Flow Zones (GitHub Issue Workflow)

### Analyze GitHub Issue

- **Zone ID:** zone-analyze-github-issue
- **Position:** (-1200, 1860) to (-492.5, 2840)
- **Purpose:** Deep analysis and solution design for GitHub issues
- **Workflow State:** analysis
- **⭐ START HERE for GitHub issues:** Issue-driven work begins here
- **Agent Behavior:**
  - **Research-only zone:** NO CODE WRITING allowed here
  - Read and understand the GitHub issue (from `worktree.issue_url`)
  - Search codebase to understand relevant areas
  - Identify root cause or requirements
  - Research existing patterns and conventions
  - Design solution approach with step-by-step implementation plan
  - Document findings and proposed solution (create PLAN.md or similar)
  - When analysis complete, move to "Implement the Plan" zone

**Zone Trigger:** show_picker
```
Please deeply analyze the GitHub issue: {{ worktree.issue_url }}

Your task is to ANALYZE and DESIGN a solution, NOT to write code yet.

1. Read and understand the issue thoroughly
2. Search the codebase to understand the relevant code areas
3. Identify the root cause or requirements
4. Research existing patterns and conventions in the codebase
5. Design a solution approach with step-by-step implementation plan
6. Document your findings and proposed solution

DO NOT write code or make changes yet. Focus on deep analysis and thoughtful design.

When done, summarize your findings and recommend next steps (move to 'Implement the Plan' zone).
```

---

### Implement the Plan

- **Zone ID:** zone-1770517487066
- **Position:** (-460, 1860) to (222.97, 2835)
- **Purpose:** Implementation of analyzed GitHub issues
- **Workflow State:** implementing
- **Agent Behavior:**
  - Execute the plan created in "Analyze GitHub Issue" zone
  - Reference PLAN.md or analysis document
  - Write code to implement the designed solution
  - Follow the step-by-step approach from analysis
  - When implementation complete and ready for PR, move to "Create a pull request" zone
  - If work becomes blocked or should be abandoned, move to "Abandoned" zone with notes
  - **Joins horizontal flow:** After implementation, flows directly to "Create a pull request"

**Zone Trigger:** show_picker
```
Go ahead and implement the plan suggested!
```

---

## Shared Workflow Zones (Both Flows Converge Here)

Both horizontal (Agor Coding Tasks) and vertical (Implement the Plan) flows converge at "Create a pull request" and follow the same path to completion.

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

### Done: PR merged

- **Zone ID:** zone-1770155449351
- **Position:** (-1200, 2900) to (952.8, 4089)
- **Purpose:** Successfully completed work (PR merged)
- **Workflow State:** done
- **Agent Behavior:**
  - Mark as completed in memory
  - Archive from active tracking
  - Don't report in heartbeat checks (noise reduction)
  - These worktrees are historical record—don't modify unless asked
  - Celebrate! 🎉
  - Record learnings and patterns discovered

**Zone Trigger:** None (terminal state)

---

### Abandoned

- **Zone ID:** zone-1770517752209
- **Position:** (1000, 2900) to (2410, 4089)
- **Purpose:** Work that was abandoned, blocked, or deemed not worth pursuing
- **Workflow State:** abandoned
- **Agent Behavior:**
  - Mark as abandoned in memory
  - Archive from active tracking
  - Don't report in heartbeat checks (noise reduction)
  - Record why it was abandoned for learning
  - Clean up stale worktrees if filesystem_status is not "ready"
  - These worktrees serve as historical record of what didn't work

**Zone Trigger:** None (terminal state)

---

## Workflow Transitions

```
                    ┌──────────────┐
                    │  MAIN SESH   │  (Orchestrator - top of board)
                    └──────────────┘

HORIZONTAL FLOW (External/Direct Tasks):
┌──────────────────┐
│ Agor Coding Tasks│  (Direct implementation)
└─────────┬────────┘
          │
          │
          └──────────────────┐
                             │
VERTICAL FLOW (GitHub Issues):        │
┌────────────────────┐                │
│ Analyze GitHub     │                │
│ Issue              │                │
└─────────┬──────────┘                │
          │                           │
          ▼                           │
┌──────────────────┐                  │
│ Implement the    │──────────────────┘
│ Plan             │
└─────────┬────────┘
          │
          ▼
┌──────────────────────┐
│ Create a pull request│  (Both flows converge)
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
          │  Done (merged)  │
          └─────────────────┘

          ┌─────────────────┐
          │   Abandoned     │  (Can branch off from any zone)
          └─────────────────┘
```

**Two Entry Points:**
1. **External/Direct tasks** → "Agor Coding Tasks" (horizontal flow)
2. **GitHub issues** → "Analyze GitHub Issue" → "Implement the Plan" (vertical flow)

**Key decision points:**
1. **Start:** Choose entry point based on task origin
   - GitHub issue → Analyze GitHub Issue zone
   - Direct request/idea → Agor Coding Tasks zone
2. **After implementation:** Both flows converge at "Create a pull request"
3. **After PR creation:** Use judgment to either:
   - → Codex review (complex/risky code)
   - → Human review (simple/confident changes)
4. **Abandonment:** From any zone, if work is blocked/not worth pursuing → Abandoned zone

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
   - **"MAIN SESH"**: Check health of orchestrator
   - **"Analyze GitHub Issue"**: Check if analysis is complete, ready to move to "Implement the Plan"
   - **"Implement the Plan"**: Check if implementation complete, ready to move to "Create a pull request"
   - **"Agor Coding Tasks"**: Check for staleness (7+ days no activity), ready for PR
   - **"Create a pull request"**: Check if PR exists, if not flag
   - **"Codex review"**: Check if review completed
   - **"Human review"**: Just report status, don't modify
   - **"Done"**: Don't report (noise reduction, terminal state)
   - **"Abandoned"**: Don't report (noise reduction, terminal state)

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
   - **GitHub issues (with issue_url):** → "Analyze GitHub Issue" zone (zone-analyze-github-issue)
   - **Direct coding tasks/external prompts:** → "Agor Coding Tasks" zone (zone-1770144681071)
   - **Orchestration/meta work:** → "MAIN SESH" zone (zone-1770143185072)
   ```typescript
   // For GitHub issues
   await agor.worktrees_set_zone({
     worktreeId: wt.worktree_id,
     zoneId: 'zone-analyze-github-issue',
   });

   // For direct tasks
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

**Last Updated:** 2026-02-08
**Board Owner:** Max (agorpg)
