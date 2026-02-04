# MEMORY.md - Long-Term Memory

_Your curated memories. The distilled essence, not raw logs._

---

## Important Notes

**Security:** This file should ONLY be loaded in main sessions (direct chats with your human). Do NOT load in group chats or shared contexts where other people are present.

**Purpose:** This is your long-term memory — significant events, thoughts, decisions, opinions, lessons learned. Over time, review your daily files (`memory/YYYY-MM-DD.md`) and update this with what's worth keeping.

**Format:** Free-form. Organize however makes sense. Could be chronological, by topic, by project — whatever helps you remember and retrieve.

---

## Key Decisions

### Board-First Workflow (Feb 3, 2026)
**Decision:** All work must be visible on "Agor Claw Experiment" board (9833703b-b9e6-4b7b-b0eb-70f783fba715)
- Every worktree requires `boardId` parameter (now enforced by PR #540)
- Zone-based organization provides visual workflow state
- Makes orchestration transparent to Max

**Rationale:** Agor is a spatial canvas - work should be visible, not hidden in terminal commands

### Coding Work = New Worktree + New Session (Feb 3, 2026)
**Pattern:** For ANY coding task, create isolated worktree + independent session
- ✅ Use `worktrees.create` + `sessions.create`
- ❌ Don't spawn subsessions in current worktree for coding work
- Reason: Git isolation, clean PRs, easier cleanup

**When to spawn subsessions:**
- Research tasks (no code changes)
- Parallel analysis
- Quick investigations
- NOT for coding work

### Codex Review is Optional (Feb 4, 2026)
**Decision:** Use judgment on whether to use Codex review zone
- Simple, obvious changes → skip to Human review
- Complex, security-sensitive → use Codex review
- Document skip decisions in commit messages

---

## Lessons Learned

### Zone Labels Are Workflow State (Feb 4, 2026)
**Discovery:** Zone labels indicate where work is in the pipeline
- "Agor Coding Tasks" = active development
- "Create a pull request" = PR workflow phase
- "Codex review" = AI code review (optional)
- "Human review" = awaiting Max
- "Done: PR merged or worktree abandoned" = completed

**Lesson:** Check zone labels in heartbeats to understand workflow state

### Heartbeat Caught Real Issues (Feb 3, 2026)
**Pattern:** Heartbeat check #2 identified agor-git-worktree-debug as abandoned, but it was actually a real issue
- Initially marked "not a real issue" and archived
- Heartbeat noticed the filesystem permission problem
- Revived the task → became PR #537 → merged

**Lesson:** Trust but verify. Heartbeats provide fresh perspective on work status.

### Session Defaults Bug (Feb 3, 2026)
**Issue:** MCP sessions.create wasn't honoring user's default_agentic_config
- All sessions got `acceptEdits` instead of user's `bypassPermissions` preference
- Required explicit permissionMode parameter every time

**Fix:** PR #535 - Now sessions inherit user defaults when permissionMode not specified
**Lesson:** Default behavior should match user expectations, not hardcoded values

### Zone Mismatch Detection (Feb 4, 2026)
**Pattern:** PR #535 was in "Done" zone before actually merging
- Multiple heartbeats flagged this as zone mismatch
- Eventually merged on Feb 4 at 05:29:40Z
- Zone state was correct, just timing

**Lesson:** Zone state can be aspirational (PR ready to merge) vs actual (PR merged). Both are valid.

---

## Important Context

### Agor Platform Evolution
**What we're building:**
- Multi-agent orchestration platform
- Spatial canvas for AI work coordination
- MCP-powered programmatic control
- Zone-based workflow automation

**Our role:** Using agor-claw to dogfood Agor platform improvements
- We orchestrate work through Agor MCP
- We contribute features back to platform
- We maintain agor-openclaw as public template

### Two Repos Strategy
**Private: mistercrunch/agor-claw**
- Our working branches (first-session, etc.)
- Contains actual experience and memory
- Max's personal workflows and context
- NOT public

**Public: mistercrunch/agor-openclaw**
- Clean framework template
- Synced from agor-claw main branch
- Public iteration and distribution
- For community use

### MCP Integration Journey
**Accomplished in 48 hours:**
1. PR #533 - Unix username auto-populate (unblocked all MCP work)
2. PR #536 - MCP repo creation tools
3. PR #535 - Session defaults fix
4. PR #537 - Git worktree permissions
5. PR #538 - Zone pinning MCP tool
6. PR #539 - Blog post about agor-openclaw
7. PR #540 - Require boardId in MCP worktrees (prevents orphans)
8. PR #541 - Fix zone pinning bug
9. PR #542 - Add zone info to worktree MCP responses
10. PR #544 - Fix unix username permission
11. PR #545 - Worktree session info (huge efficiency win)

**Impact:** Agor MCP is now fully functional for multi-agent orchestration

---

## Ongoing Projects

### Agor-Claw Framework (Active)
**Status:** Mature, functional, dogfooding daily
- Zone-based workflow operational
- Heartbeat automation working (0,48 * * * * schedule)
- Memory system tracking accurately
- Board organization clean

**Next:** Continue evolving based on daily use

### Agor-OpenClaw Public Template (Active)
**Current work:** improve-framework-template branch
- PR #2: https://github.com/mistercrunch/agor-openclaw/pull/2
- Integrating improvements from our private repo
- Making template more accessible

**Goal:** Clean, documented framework for others to fork

### Heartbeat Efficiency Improvements (Complete)
**Problem:** Heartbeats required ~120 API calls for 20 worktrees
- worktrees.list → sessions.list (per worktree) → tasks.list (per session)
- Slow, expensive, inefficient

**Solution:** PR #545 - Embed session activity in worktree responses
- Now: 1 API call gets everything
- Worktrees are primary unit of orchestration
- Session info included as array in worktree object

**Status:** ✅ Merged Feb 4, 2026 at 23:33:17Z

---

## Preferences Discovered

### Max's Working Style
**Communication:**
- Values no-BS, get-stuff-done approach
- Appreciates proactive solutions
- Prefers asking permission before external actions (for now)
- Open to more autonomy as trust builds

**Tools:**
- Primary: Claude Code (go-to for most tasks)
- Code Review: Codex (preferred for review workflows)

**PR Workflow (preset-io/agor):**
1. pnpm install
2. pnpm check (type check, lint, tests, build)
3. Commit changes
4. Create PR via `gh pr create`
5. Attach PR to worktree: `agor.worktrees.update({ pullRequestUrl })`

**Review Speed:**
- Fast merger when work is good
- 11 PRs merged in ~48 hours
- Trusts autonomous work when quality is high

### Board Organization Preferences
**Zones on "Agor Claw Experiment" board:**
- MAIN SESH - Orchestrator control center (first-session worktree)
- Agor Coding Tasks - Active development
- Create a pull request - PR staging
- Codex review - Optional AI review
- Human review - Terminal state, awaiting Max
- Done: PR merged or worktree abandoned - Archive

**Pattern:** Clear workflow pipeline, left-to-right progression

---

## Wins

### First 48 Hours (Feb 3-4, 2026)
**Achievements:**
- ✅ Identity established (Darryl 🦞)
- ✅ Board created and organized
- ✅ Repos configured (preset-io/agor, agor-claw)
- ✅ POC successful (session spawning working)
- ✅ 11 PRs merged to Agor platform
- ✅ Public agor-openclaw repo launched
- ✅ Heartbeat automation working
- ✅ Zone-based workflow operational
- ✅ Multi-agent coordination proven

**Impact:** Demonstrated value immediately, built trust, contributed real features

### Technical Contributions
**Platform improvements delivered:**
- MCP session/worktree/repo management tools
- Zone-aware tracking and automation
- Session activity embedding
- Permission defaults inheritance
- Git worktree permission fixes

**Each PR solved real problems we encountered while building agor-claw**

---

_Last updated: 2026-02-04 (Heartbeat #7)_
