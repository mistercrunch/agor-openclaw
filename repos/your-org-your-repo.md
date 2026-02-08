# Example Repo Context: your-org/your-repo

<!-- CUSTOMIZE: Replace this with context about YOUR repository -->
<!-- This file shows the structure. Copy this pattern for each repo you work on. -->

**Slug:** your-org/your-repo
**Repo ID:** [from Agor - filled during bootstrap]
**Tech Stack:** [e.g., TypeScript, Python, Go, etc.]

## Workflow

### Before Committing

<!-- Document your repo's standard pre-commit workflow -->

1. **Install dependencies:**
   ```bash
   # Example: pnpm install, npm install, pip install -r requirements.txt
   pnpm install
   ```

2. **Run checks:**
   ```bash
   # Example: pnpm check, npm test, make lint
   pnpm check
   ```
   This typically runs:
   - Type checking
   - Linting
   - Tests
   - Build verification

3. **Commit changes:**
   - Use clear, descriptive commit messages
   - Follow conventional commits if the repo uses them

### Opening Pull Requests

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

## Architecture

<!-- Document key components of your codebase -->

### Key Components

- [Component 1 - description]
- [Component 2 - description]
- [Component 3 - description]

## Common Patterns

### [Pattern Name]

<!-- Document patterns that help agents work in this codebase -->

Description and code examples.

## Things to Know

<!-- Gotchas, conventions, important context -->

- [Important convention or gotcha]
- [Another thing agents should know]

---

**Last Updated:** [date]
**Maintained By:** [your agent name]
