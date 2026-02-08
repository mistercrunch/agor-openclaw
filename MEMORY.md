# MEMORY.md - Long-Term Memory

_Your curated memories. The distilled essence, not raw logs._

---

## Important Notes

**Security:** This file should ONLY be loaded in main sessions (direct chats with your human). Do NOT load in group chats or shared contexts where other people are present.

**Purpose:** This is your long-term memory -- significant events, thoughts, decisions, opinions, lessons learned. Over time, review your daily files (`memory/YYYY-MM-DD.md`) and update this with what's worth keeping.

**Format:** Free-form. Organize however makes sense. Could be chronological, by topic, by project -- whatever helps you remember and retrieve.

---

## Key Decisions

<!-- EXAMPLE: Document important decisions made during your operation -->

### Board-First Workflow
**Decision:** All work must be visible on your main board
- Every worktree requires `boardId` parameter
- Zone-based organization provides visual workflow state
- Makes orchestration transparent to your human

**Rationale:** Agor is a spatial canvas -- work should be visible, not hidden in terminal commands

### Coding Work = New Worktree + New Session
**Pattern:** For ANY coding task, create isolated worktree + independent session
- Use `worktrees.create` + `sessions.create`
- Don't spawn subsessions in current worktree for coding work
- Reason: Git isolation, clean PRs, easier cleanup

**When to spawn subsessions:**
- Research tasks (no code changes)
- Parallel analysis
- Quick investigations
- NOT for coding work

### Codex Review is Optional
**Decision:** Use judgment on whether to use Codex review zone
- Simple, obvious changes -> skip to Human review
- Complex, security-sensitive -> use Codex review
- Document skip decisions in commit messages

---

## Lessons Learned

<!-- These are real lessons discovered during dogfooding. Keep them -- they help new users! -->

### Zone Labels Are Workflow State
**Discovery:** Zone labels indicate where work is in the pipeline
- "Agor Coding Tasks" = active development
- "Create a pull request" = PR workflow phase
- "Codex review" = AI code review (optional)
- "Human review" = awaiting human
- "Done: PR merged or worktree abandoned" = completed

**Lesson:** Check zone labels in heartbeats to understand workflow state

### Heartbeats Catch Real Issues
**Pattern:** Heartbeat checks can identify stale or stuck work
- Heartbeats provide fresh perspective on work status
- Can detect abandoned work that deserves another look

**Lesson:** Trust but verify. Heartbeats provide fresh perspective on work status.

### Zone State Can Be Aspirational
**Pattern:** A PR in "Done" zone might not be merged yet
- Zone state can be aspirational (PR ready to merge) vs actual (PR merged)
- Both are valid, but heartbeats should verify actual state

---

## Operating Patterns

### Standard PR Workflow
1. Create worktree with boardId (REQUIRED)
2. Create session in that worktree
3. Do the work
4. Run checks (build, lint, test)
5. Commit and create PR
6. Attach PR URL to worktree via `agor.worktrees.update({ pullRequestUrl })`
7. Move to appropriate review zone

### Heartbeat Workflow
1. List all worktrees on your board
2. Check zone placement for each
3. Take zone-specific actions (see HEARTBEAT.md)
4. Flag stuck or stale work
5. Update memory with findings

---

## Preferences Discovered

<!-- These get filled in as your agent learns about you -->

### Board Organization
**Zones on your board:**
- MAIN SESH - Orchestrator control center
- Agor Coding Tasks - Active development
- Analyze GitHub Issue - Research and design phase
- Implement the Plan - Implementation phase
- Create a pull request - PR staging
- Codex review - Optional AI review
- Human review - Terminal state, awaiting human
- Done: PR merged or worktree abandoned - Archive
- Abandoned - Work that was dropped

**Pattern:** Clear workflow pipeline with two entry points (direct tasks and GitHub issues)

---

_Last updated: [date]_
