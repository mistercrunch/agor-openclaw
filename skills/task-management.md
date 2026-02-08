# Skill: Task Management in Agor

**When to use:** When your human assigns tasks that require independent worktrees and sessions

**Purpose:** Track and manage multi-worktree tasks from end to end, ensuring nothing falls through the cracks.

---

## Prerequisites

- [ ] Main board identified (in `IDENTITY.md`)
- [ ] Repo is configured in Agor
- [ ] Memory tracking system initialized (`memory/agor-state/`)

---

## Steps

### 1. Create a Worktree

For each independent task:

```typescript
const worktree = await agor.worktrees.create({
  repoId: REPO_ID,
  worktreeName: 'descriptive-task-name',  // kebab-case, clear purpose
  createBranch: true,
  sourceBranch: 'main',  // or appropriate base branch
  pullLatest: true,
  boardId: MAIN_BOARD_ID,  // Place on main board
});
```

**Naming convention:**
- Use kebab-case
- Be descriptive but concise
- Include context: `agor-mcp-repo-creation`, `superset-fix-chart-bug`

### 2. Spawn a Session in the Worktree

```typescript
const session = await agor.sessions.spawn({
  prompt: `[Clear task description with context and goals]

  Context:
  - [Relevant background]

  Goals:
  - [Specific objectives]
  - [Expected deliverables]

  Success criteria:
  - [How to know when done]`,
  enableCallback: true,  // Get notified when done
  includeLastMessage: true,  // Include results in callback
  title: 'Brief task title',
});
```

### 3. Track in Memory

**Update `memory/agor-state/worktrees.json`:**

```json
{
  "active_worktrees": [
    {
      "worktree_id": "[worktree_id]",
      "name": "task-name",
      "repo": "repo-slug",
      "purpose": "Brief description of task",
      "created": "2026-02-03T18:30:00Z",
      "status": "in_progress",
      "session_id": "[session_id]",
      "expected_outcome": "PR for X / Investigation results / etc."
    }
  ],
  "last_synced": "[timestamp]"
}
```

**Update `memory/agor-state/sessions.json`:**

```json
{
  "tracked_sessions": [
    {
      "session_id": "[session_id]",
      "purpose": "Task description",
      "parent": "[parent_session_id or null]",
      "worktree_id": "[worktree_id]",
      "status": "running",
      "created": "[timestamp]"
    }
  ],
  "last_synced": "[timestamp]"
}
```

**Log in daily memory (`memory/YYYY-MM-DD.md`):**

```markdown
### Task Created: [Task Name]

- **Worktree:** [name] (ID: [worktree_id])
- **Session:** [session_id]
- **Repo:** [repo-slug]
- **Goal:** [Brief description]
- **Board:** [board-name]
- **Created:** [timestamp]
```

### 4. Monitor Progress

**Via callbacks:**
- Sessions spawned with `enableCallback: true` will notify on completion
- Results appear in the parent session

**Manual checks:**
```typescript
// Check session status
const session = await agor.sessions.get({ sessionId: SESSION_ID });

// Check worktree state
const worktree = await agor.worktrees.get({ worktreeId: WORKTREE_ID });
```

### 5. Close the Loop

When task completes:

**Update tracking files:**
- Mark worktree as `completed` in `worktrees.json`
- Mark session as `completed` in `sessions.json`
- Log outcome in daily memory

**Log learnings:**
```markdown
# memory/learnings/YYYY-MM-DD.md

## Task: [name]

**What worked:**
- [Successes]

**What didn't:**
- [Failures/challenges]

**Lessons:**
- [Key takeaways]

**Should contribute to main:**
- [ ] Framework improvement X
- [ ] Skill update Y
```

**Clean up (when appropriate):**
- Archive old worktrees (not immediately - keep for reference)
- Move completed tasks to "Done" zone on board
- Update `MEMORY.md` if significant

---

## Outputs

- Worktree created and visible on board
- Session spawned and executing task
- Complete tracking in memory system
- Daily log entry created
- Clear path to close the loop

---

## Error Handling

**Worktree creation fails:**
- Check if repo exists in Agor
- Verify board ID is correct
- Check if worktree name already exists

**Session spawn fails:**
- Verify worktree ID is correct
- Check if prompt is well-formed
- Ensure MCP token is valid

**Tracking gets out of sync:**
- Re-sync by listing all worktrees/sessions via MCP
- Update JSON files with current state
- Log discrepancies in daily memory

---

## Related Skills

- `worktree-cleanup.md` (future) - Archiving old worktrees
- `session-orchestration.md` (future) - Complex multi-session coordination
- `pr-management.md` (future) - From worktree to merged PR

---

## Contributing Improvements to Main

When working in your branch, identify improvements that should go to `main`:

**Framework improvements (contribute to main):**
- New skills (like this one)
- Updates to AGENTS.md, SOUL.md, etc.
- Memory system improvements
- Workflow refinements

**Working state (stay in branch):**
- Daily logs
- Active worktree tracking
- Session state
- Personal notes

**Process:**
1. Note improvements in `memory/learnings/`
2. Periodically review with your human
3. Create PR from working branch → main with just framework updates
4. Keep template clean and generally applicable

---

**Last Updated:** [date]
**Created By:** [your agent]
