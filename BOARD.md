# BOARD.md - Your Agor Board Configuration

**Purpose:** Document board structure, zones, and workflow expectations so agents understand how to organize work spatially.

---

## Board Information

<!-- CUSTOMIZE: Fill in with your board details from IDENTITY.md -->

- **Board ID:** `[Your board ID from IDENTITY.md]`
- **Board Name:** [Your board name]
- **Board URL:** https://agor.live/board/[board_id]
- **Icon:** [Your board icon]
- **Background:** [Your board style]

---

## Zones and Workflow

**Why zones matter:** Zones represent workflow states. The zone a worktree is IN determines its true status -- this is your spatial organization of work.

**Board Layout:**
- **Horizontal flow (left-to-right):** Main workflow for all tasks - MAIN SESH -> Agor Coding Tasks -> Create PR -> Codex Review -> Human Review -> Done
- **Vertical flow (GitHub issues):** Analyze GitHub Issue (top) -> Implement the Plan (bottom) -> joins horizontal flow at "Create PR"

**Two entry points:**
1. **External prompts/direct tasks:** Start at "Agor Coding Tasks" (horizontal flow)
2. **GitHub issue-driven work:** Start at "Analyze GitHub Issue" (vertical flow)

## Horizontal Flow Zones (Main Workflow)

### MAIN SESH

- **Zone ID:** zone-1770143185072
- **Purpose:** Primary orchestrator workspace
- **Workflow State:** control-center
- **Agent Behavior:**
  - This zone contains the main orchestrator session
  - Memory management, heartbeats, and meta-work happens here
  - Don't clutter this zone with coding tasks -- it's for orchestration only
  - Worktrees here are typically long-lived and not feature-specific

**Zone Trigger:** None (manual work only)

---

### Agor Coding Tasks

- **Zone ID:** zone-1770144681071
- **Purpose:** Active development work (externally prompted tasks)
- **Workflow State:** in-progress
- **Start here for:** Direct requests, bug reports via chat, feature ideas, refactors
- **Agent Behavior:**
  - Worktrees here are actively being worked on
  - Check for staleness: flag if no activity in 7+ days
  - When work is complete and ready for PR creation, move to "Create a pull request" zone
  - If abandoned or blocked, move to "Abandoned" zone with notes explaining why

**Zone Trigger:** None (manual work)

---

## Vertical Flow Zones (GitHub Issue Workflow)

### Analyze GitHub Issue

- **Zone ID:** zone-analyze-github-issue
- **Purpose:** Deep analysis and solution design for GitHub issues
- **Workflow State:** analysis
- **Start here for:** Issue-driven work
- **Agent Behavior:**
  - **Research-only zone:** NO CODE WRITING allowed here
  - Read and understand the GitHub issue (from `worktree.issue_url`)
  - Search codebase, identify root cause, design solution approach
  - Document findings (create PLAN.md or similar)
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
- **Purpose:** Implementation of analyzed GitHub issues
- **Workflow State:** implementing
- **Agent Behavior:**
  - Execute the plan created in "Analyze GitHub Issue" zone
  - Reference PLAN.md or analysis document
  - When implementation complete, move to "Create a pull request" zone
  - **Joins horizontal flow:** After implementation, flows to "Create a pull request"

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
- **Purpose:** Work ready for pull request creation
- **Workflow State:** ready-for-pr
- **Agent Behavior:**
  - Run build/lint/test checks to verify work
  - Commit any uncommitted changes
  - Create PR if not exists
  - Attach PR URL to worktree metadata using `worktrees.update`
  - After PR created, decide: needs Codex review OR skip to Human review
  - Move worktree to next zone when done

**Zone Trigger:** show_picker
```
ok now let's:
- pnpm i
- pnpm check
- commit
- open a PR
- attach the pull request to the worktree using MCP

Please maintain a todo list so I can track progress!
```

---

### Codex review

- **Zone ID:** zone-1770152375353
- **Purpose:** Automated code review by Codex agent
- **Workflow State:** ai-review
- **Agent Behavior:**
  - **IMPORTANT:** This zone is OPTIONAL -- use judgment!
  - Complex/risky code -> use Codex review
  - Simple/confident -> skip to Human review
  - Spawn Codex subsession with branch and PR context
  - After review complete, move to Human review

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
- **Purpose:** Awaiting human review and merge
- **Workflow State:** terminal (agent waits)
- **Agent Behavior:**
  - This is a terminal state for automated agents
  - Don't take automated actions on worktrees here
  - Check PR status during heartbeats:
    - If PR merged -> Move to "Done" zone immediately
    - If PR has requested changes -> Flag for attention
  - Only modify worktrees if PR status changes

**Zone Trigger:** None (human action required)

---

### Done: PR merged

- **Zone ID:** zone-1770155449351
- **Purpose:** Successfully completed work (PR merged)
- **Workflow State:** done
- **Agent Behavior:**
  - Mark as completed in memory
  - Don't report in heartbeat checks (noise reduction)

**Zone Trigger:** None (terminal state)

---

### Abandoned

- **Zone ID:** zone-1770517752209
- **Purpose:** Work that was abandoned, blocked, or deemed not worth pursuing
- **Workflow State:** abandoned
- **Agent Behavior:**
  - Mark as abandoned in memory
  - Don't report in heartbeat checks (noise reduction)
  - Record why it was abandoned for learning

**Zone Trigger:** None (terminal state)

---

## Workflow Transitions

```
                    +----------------+
                    |   MAIN SESH    |  (Orchestrator - top of board)
                    +----------------+

HORIZONTAL FLOW (External/Direct Tasks):
+--------------------+
| Agor Coding Tasks  |  (Direct implementation)
+---------+----------+
          |
          +---------------------------+
                                      |
VERTICAL FLOW (GitHub Issues):        |
+--------------------+                |
| Analyze GitHub     |                |
| Issue              |                |
+---------+----------+                |
          |                           |
          v                           |
+--------------------+                |
| Implement the      |----------------+
| Plan               |
+---------+----------+
          |
          v
+------------------------+
| Create a pull request  |  (Both flows converge)
+---------+--------------+
          |
          +---------------+
          |               |
          v               v
    +----------+   +----------------+
    |  Codex   |   | Human review   |  (Skip Codex if simple)
    |  review  |   |                |
    +----+-----+   +----------------+
         |                ^
         +----------------+
                    |
                    v
          +-----------------+
          |  Done (merged)  |
          +-----------------+

          +-----------------+
          |   Abandoned     |  (Can branch off from any zone)
          +-----------------+
```

**Two Entry Points:**
1. **External/Direct tasks** -> "Agor Coding Tasks" (horizontal flow)
2. **GitHub issues** -> "Analyze GitHub Issue" -> "Implement the Plan" (vertical flow)

---

## Agent Instructions

### For Heartbeat Checks

Zone information is **directly available** in worktree MCP responses as `zone_id` and `zone_label` fields:

```
Use agor_worktrees_list to get all worktrees.
Each worktree includes zone_id and zone_label fields automatically.

Take actions based on zone_label:
- "Done: PR merged or worktree abandoned" -> Mark completed, archive
- "Create a pull request" + missing pull_request_url -> Create PR
- "Agor Coding Tasks" + stale last_updated -> Flag for attention
```

### For New Work

1. Create worktree with required `boardId`
2. Use `agor_worktrees_set_zone` to place in appropriate starting zone
3. Zone triggers may auto-start sessions (if configured)

---

## Notes

- **Codex review is a judgment call, not mandatory**
- Simple changes can skip straight to Human review
- Zone IDs are stable, but positions/sizes may change
- Agent behavior should adapt to your workflow, not dictate it

---

**Last Updated:** [date]
