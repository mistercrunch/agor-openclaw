# preset-io/agor

**Slug:** preset-io/agor
**Repo ID:** 5e4aa6a8-0b4a-47a5-be06-2c3fb3308a6a
**Tech Stack:** TypeScript, Node.js, Feathers.js, Drizzle ORM, React, Docker

## Workflow

### Before Committing

Max's preferred workflow when work is ready:

1. **Install dependencies:**
   ```bash
   pnpm install
   ```

2. **Run checks:**
   ```bash
   pnpm check
   ```
   This typically runs:
   - Type checking
   - Linting
   - Tests (if applicable)
   - Build verification

3. **Commit changes:**
   - Use clear, descriptive commit messages
   - Follow conventional commits if the repo uses them
   - Include Co-Authored-By if Claude Code did the work

### Opening Pull Requests

Standard PR workflow:

1. **Final verification:**
   ```bash
   pnpm check
   ```

2. **Create PR:**
   ```bash
   gh pr create --title "PR Title" --body "$(cat <<'EOF'
   ## Summary
   - What changed
   - Why it changed

   ## Test Plan
   - How to test

   🤖 Generated with Claude Code
   EOF
   )"
   ```

3. **Attach PR to worktree via Agor MCP:**
   ```typescript
   await agor.worktrees.update({
     worktreeId: WORKTREE_ID,
     pullRequestUrl: PR_URL
   });
   ```

4. **Update tracking:**
   - Mark worktree status in memory/agor-state/worktrees.json
   - Log in daily memory

## Architecture

### Key Components

- **Daemon:** FeathersJS server, MCP endpoint, services
- **Client:** CLI (oclif) + Web UI (React + Ant Design)
- **Storage:** LibSQL database (~/.agor/agor.db), git worktrees
- **Agent SDKs:** Claude Code, Codex, Gemini integrations

### MCP Implementation

- HTTP endpoint: `POST /mcp?sessionToken=...`
- Tools defined in services layer
- See `IMPLEMENT_MCP_TOOLS.md` for adding new tools

## Common Patterns

### Creating Worktrees

Agor uses git worktrees as first-class primitives:
- Isolated development environments
- One worktree per feature/task
- Managed via Agor MCP or CLI

### Session Management

Sessions are AI agent conversations:
- Full genealogy tracking (parent-child, fork relationships)
- Bound to worktrees
- Permission modes: bypassPermissions, acceptEdits, ask, auto, etc.

### MCP Tools

Tools follow this pattern:
1. Define in service (e.g., `SessionsService`)
2. Expose via MCP HTTP endpoint
3. Type-safe parameters with validation
4. Return structured responses

## Things to Know

- **Monorepo structure:** Uses pnpm workspaces
- **Permission system:** Unix user impersonation for isolation
- **Worktree isolation:** Each worktree has its own Unix group for filesystem security
- **Docker environments:** Worktrees can have start/stop/health/logs/nuke commands
- **Board system:** Visual canvas for organizing worktrees and sessions

## Known Issues / Ongoing Work

- MCP repo creation tools (in progress: worktree 699cb3c4)
- Git worktree permissions (in progress: worktree 0318dd77)
- MCP session defaults not honoring user config (in progress: worktree 933a0a5a)
- Session deletion MCP tools (missing)

## Max's Preferences

- **Primary agent:** Claude Code
- **Code review:** Codex
- **Permission mode:** bypassPermissions (for Claude Code)
- **Commit style:** Clear, descriptive messages

---

**Last Updated:** 2026-02-03
**Maintained By:** Darryl (agor-claw)
