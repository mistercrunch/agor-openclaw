# Skills

Skills are reusable patterns and workflows for the agent to execute.

---

## What Are Skills?

Skills are **documented procedures** for common tasks. Think of them as SOPs (Standard Operating Procedures) that the agent can reference and follow.

**Examples:**
- Creating worktrees with consistent naming
- Spawning subsessions for research
- Running code review workflows
- Setting up CI/CD for a repo

---

## Two Types of Skills

### 1. Documented Workflows (This Directory)

Simple markdown files describing step-by-step procedures:

```
skills/
├── README.md (this file)
├── worktree-ops.md
├── session-spawn.md
└── code-review.md
```

**When to use:** Simple, human-readable procedures that don't need code execution.

### 2. Executable Tools (`.claude/` Directory)

For skills that need actual code execution, use Claude Code's `.claude/` directory:

```
.claude/
├── mcp-servers/          # Custom MCP servers
├── tools/                # Custom tool implementations
└── config.json           # Claude Code configuration
```

**When to use:** Skills that need:
- API integrations
- Complex logic
- System commands
- State management

---

## Skill Template

Create skills as markdown files with this structure:

```markdown
# Skill: [Name]

**When to use:** [Description of when this skill applies]

**Prerequisites:**
- [ ] Requirement 1
- [ ] Requirement 2

**Steps:**

1. **Step 1:** Description
   ```typescript
   // Code example if needed
   const result = await agor.worktrees.create({...});
   ```

2. **Step 2:** Description
   - Sub-step A
   - Sub-step B

3. **Step 3:** Description

**Outputs:**
- Expected result 1
- Expected result 2

**Error Handling:**
- Common error X → Solution
- Common error Y → Solution

**Related Skills:**
- [Link to related skill]
```

---

## Example: Worktree Creation Skill

See how BOOTSTRAP.md creates worktrees with the POC test. That's a skill in action:

1. Check if repo exists
2. Create worktree with Agor MCP
3. Record in memory/agor-state/worktrees.json
4. Place on main board
5. Log to daily memory

This pattern can be extracted into `skills/worktree-ops.md` for reuse.

---

## Skills vs. `.claude/` Tools

**Skills (markdown):**
- ✅ Simple to create and maintain
- ✅ Human-readable
- ✅ Easy to version control
- ✅ Agent reads and follows
- ❌ No code execution
- ❌ Manual steps

**`.claude/` Tools:**
- ✅ Executable code
- ✅ Complex logic
- ✅ API integrations
- ✅ Automatic execution
- ❌ Requires TypeScript/Node.js
- ❌ More setup overhead

**Best practice:** Start with documented skills (markdown), graduate to `.claude/` tools when automation is needed.

---

## Agor-Specific Skills

Skills in this workspace should leverage Agor MCP heavily:

### Core Skill Categories

**Worktree Operations:**
- Create feature branches
- Clean up completed work
- Sync with main branch
- Archive old worktrees

**Session Management:**
- Spawn research subsessions
- Coordinate multi-agent work
- Track genealogy
- Handle callbacks

**Code Quality:**
- Run automated reviews
- Check test coverage
- Lint and format
- Generate documentation

**Memory Management:**
- Update MEMORY.md from daily logs
- Sync agor-state/ with Agor
- Archive old logs
- Extract learnings

---

## Creating Your First Skill

1. **Identify a pattern** you repeat often
2. **Document the steps** in a new markdown file
3. **Test it** by following the steps manually
4. **Refine** based on what works
5. **Reference in AGENTS.md** so agent knows it exists

---

## Future: Skill Discovery

As the agent learns and evolves, skills will:
- Be created by the agent itself (self-improvement)
- Be shared across agent instances
- Evolve based on success/failure patterns
- Become more automated over time

For now, start simple: document the patterns you want the agent to follow.

---

## Related Documentation

- **AGENTS.md** - References skills in the "Skills" section
- **TOOLS.md** - Environment-specific shortcuts
- **memory/learnings/** - Where you discover new patterns to turn into skills

---

_Skills are the building blocks of agent expertise. Start small, iterate, automate._
