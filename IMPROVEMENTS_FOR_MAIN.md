# Improvements to Contribute Back to agor-openclaw Main

This file tracks improvements made in this workspace that should be contributed back to the main agor-openclaw template repository.

---

## 1. Orchestrator Role Clarification (AGENTS.md)

**Added:** Clear distinction between orchestrator role vs. worker sessions

**Key concepts:**
- Orchestrator delegates coding work, doesn't do it directly
- Coding tasks: NEW worktree + NEW session pattern
- Local tasks: do yourself, spawn subsession, or fork
- Session continuity: one worktree = one session tree

**Why it matters:**
- Prevents confusion about when to spawn vs. create vs. do work directly
- Establishes clear patterns for multi-agent coordination
- Reduces mistakes like doing coding work in orchestrator session

**Location:** AGENTS.md "How You Operate" section

---

## 2. Critical Coding Workflow Section (AGENTS.md / CLAUDE.md)

**Added:** Explicit warning about coding work requiring isolation

**Content:**
```
### 🚨 CRITICAL: Coding Work Requires Isolation

When doing ANY coding work:
1. ALWAYS create NEW worktree (not spawn in existing)
2. ALWAYS create NEW session in that worktree (not spawn subsession)
3. ALWAYS specify boardId (REQUIRED - prevents orphaned worktrees)
```

**Why it matters:**
- Single biggest source of errors in early usage
- Prevents orphaned worktrees (invisible on boards)
- Prevents subsession cascades and callback chaos
- Makes it crystal clear: coding = isolation

**Location:** AGENTS.md under "Agor MCP: Your Primary Tool"

---

## 3. Session Operation Types (AGENTS.md)

**Added:** Clear explanation of create vs. spawn vs. fork

**Content:**
- `sessions.create` = NEW independent session (fresh context)
- `sessions.spawn` = child subsession (fresh context, callback)
- `sessions.prompt` with fork = sibling session (reuse context)

**Why it matters:**
- These are fundamentally different operations
- Wrong choice leads to wrong context window, wrong genealogy
- Critical for understanding when to use which

**Location:** AGENTS.md "Task Delegation Rules"

---

## 4. boardId Requirement Documentation

**Added:** Emphasis that boardId is REQUIRED for worktrees

**Why it matters:**
- Prevents orphaned worktrees (major UX issue)
- Should be reflected in template before users encounter it
- Once PR #540 merges, MCP will enforce this

**Note:** This became a code fix (PR #540) but template should warn about it

**Location:** All worktree creation examples in AGENTS.md

---

## 5. Learnings Documentation Pattern

**Added:** `memory/learnings/YYYY-MM-DD.md` structure

**Purpose:** Capture mistakes, lessons, patterns discovered

**Example content:**
- What went wrong
- Root cause analysis
- What should have been done
- Actions taken
- Improvement proposals

**Why it matters:**
- Helps agents learn from mistakes
- Creates feedback loop for template improvements
- Surfaces patterns for better documentation

**Location:** New pattern to add to AGENTS.md "Memory System" section

---

## 6. Heartbeat Board Scoping

**Added:** Scope heartbeat checks to main board only

**Key concepts:**
- Only check resources on YOUR main board (from IDENTITY.md)
- Don't check other users' boards unless explicitly asked
- Prevents noise from unrelated work in multi-user environments

**Why it matters:**
- In shared Agor instances, checking all boards is overwhelming
- Agent should focus on its own workspace
- Reduces false positives and irrelevant alerts
- Respects multi-user/multi-board boundaries

**Location:** HEARTBEAT.md "Agor Resource Checks" section

---

## 7. Zone-Aware Status Understanding

**Added:** Zone information as primary status indicator

**Key insight:** Zone names are HIGHLY informative about work state
- Users organize worktrees spatially on boards
- Zone names reflect user's perception (e.g., "In Progress", "Ready for PR", "Done", "Blocked")
- Spatial position = workflow state
- **Read board zones FIRST** before analyzing individual worktrees

**Why it matters:**
- Zones encode user intent and workflow state
- More accurate than worktree metadata alone
- Enables contextual status understanding
- Agent can detect mismatches (e.g., completed work in "In Progress" zone)

**Example workflow states from zones:**
- "Design!" → Planning phase
- "In Progress" → Active development
- "Open a PR" → Ready for PR creation
- "Codex review" → Code review needed
- "Human review" / "Trash" → Terminal states

**Implementation:**
```typescript
// 1. Get board with zones
const board = await agor.boards.get({ boardId: MAIN_BOARD_ID });

// 2. Analyze zone distribution
for (const [zoneId, zone] of Object.entries(board.objects)) {
  if (zone.type === 'zone') {
    // Find worktrees in this zone (by position)
    // Zone label = workflow state
  }
}

// 3. Check for state mismatches
// E.g., PR merged but worktree still in "In Progress" zone
```

**Location:** HEARTBEAT.md "Board & Zone Analysis" section

---

## Implementation Plan

When contributing these back to main:

1. **Review current main branch** - check what's changed since fork
2. **Extract generic patterns** - remove Max-specific references
3. **Test with fresh clone** - ensure instructions work from scratch
4. **Create PR** with clear rationale for each change
5. **Document breaking changes** if any (e.g., boardId requirement)

---

## Notes

- Keep AGENTS.md concise - context density matters
- Every addition should solve a real confusion point
- Test instructions with "fresh eyes" - assume no prior Agor knowledge
- Preserve the template's flexibility - these are guidelines, not rigid rules
