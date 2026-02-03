# TOOLS.md - Agor-Specific Notes

Your primary tool is the **Agor MCP server**. This file is for environment-specific details and shortcuts.

---

## What Goes Here

Agor-related configuration and shortcuts:

- **Board IDs and names** (quick reference)
- **Repo IDs and paths** (frequently used repos)
- **Common worktree patterns** (naming conventions, branch strategies)
- **Session templates** (common prompts you use often)
- **Environment specifics** (local paths, dev server ports, etc.)

---

## Agor Quick Reference

### My Main Board

```markdown
- **Board ID:** [from IDENTITY.md]
- **Board Name:** [your main board]
- **URL:** https://agor.live/board/[board_id]
```

### Configured Repositories

```markdown
| Repo Slug | Repo ID | Local Path | Notes |
|-----------|---------|------------|-------|
| org/repo1 | 01abc   | /path/to   | Primary work |
| org/repo2 | 01def   | /path/to   | Side project |
```

*(Keep this synced with `memory/agor-state/repos.json`)*

---

## Worktree Naming Conventions

Document your patterns for consistency:

```markdown
### Feature Work
- Pattern: `feature-{description}`
- Example: `feature-user-auth`, `feature-api-endpoints`

### Bug Fixes
- Pattern: `fix-{issue-number}-{description}`
- Example: `fix-123-login-error`, `fix-456-cors-issue`

### Experiments
- Pattern: `exp-{topic}`
- Example: `exp-react-query`, `exp-new-architecture`

### Temporary/POC
- Pattern: `tmp-{purpose}`
- Example: `tmp-hello-world`, `tmp-test-integration`
```

---

## Common Session Prompts

Save frequently-used prompts for quick spawning:

```markdown
### Code Review
"Review the changes in this worktree. Focus on:
- Code quality and patterns
- Potential bugs or edge cases
- Test coverage
- Documentation needs"

### Testing
"Run the test suite in this worktree and:
- Report failures with details
- Suggest fixes for failing tests
- Identify missing test coverage"

### Documentation
"Update documentation for the changes in this worktree:
- README if public API changed
- Inline comments for complex logic
- Architecture docs if structure changed"
```

---

## Local Environment

Document your development setup:

```markdown
### Development Servers
- **Agor UI:** http://localhost:5173 (default)
- **Agor Daemon:** http://localhost:3030 (default)
- **Custom ports:** [list any customizations]

### Paths
- **Agor worktrees root:** ~/.agor/worktrees/
- **Agor database:** ~/.agor/agor.db
- **Agor config:** ~/.agor/config.yaml

### Tools
- **Node version:** [your version]
- **Package manager:** pnpm / npm / yarn
- **Editor:** VS Code / vim / etc.
```

---

## Agor MCP Shortcuts

Quick reference for common operations:

```typescript
// List boards
const boards = await mcp.agor_boards_list();

// Get current session
const session = await mcp.agor_sessions_get_current();

// List worktrees for a repo
const worktrees = await mcp.agor_worktrees_list({ repoId: 'YOUR_REPO_ID' });

// Spawn subsession
const sub = await mcp.agor_sessions_spawn({
  prompt: "Your task here",
  enableCallback: true,
});

// Get worktree info
const wt = await mcp.agor_worktrees_get({ worktreeId: 'WORKTREE_ID' });
```

---

## Skills Reference

Quick links to your most-used skills:

```markdown
- **worktree-ops/create** - Create new feature branches
- **worktree-ops/cleanup** - Clean up completed worktrees
- **session-spawn/research** - Spawn research subsessions
- **code-review/analyze** - Run code quality analysis
```

---

## Why Keep This Separate?

- **Skills are generic** - they work for anyone
- **This file is specific** - your repos, your board, your environment
- **Easy updates** - change your setup without touching skill definitions
- **No leaks** - share skills without revealing your infrastructure

---

Add whatever helps you work efficiently. This is your cheat sheet.
