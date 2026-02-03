# Agor State Tracking

This directory tracks Agor resources managed by the agent.

---

## Files

### repos.json
Configured repositories in Agor that the agent works with.

**Structure:**
```json
{
  "configured_repos": [
    {
      "repo_id": "UUIDv7",
      "name": "repo-name",
      "slug": "org/repo-name",
      "notes": "Optional context"
    }
  ],
  "last_updated": "ISO 8601 timestamp"
}
```

### worktrees.json
Active worktrees the agent is managing.

**Structure:**
```json
{
  "active_worktrees": [
    {
      "worktree_id": "UUIDv7",
      "name": "worktree-name",
      "purpose": "What this worktree is for",
      "repo_id": "UUIDv7",
      "created": "ISO 8601 timestamp",
      "notes": "Optional context"
    }
  ],
  "last_synced": "ISO 8601 timestamp"
}
```

### sessions.json
Active sessions and their genealogy (parent-child relationships).

**Structure:**
```json
{
  "tracked_sessions": [
    {
      "session_id": "UUIDv7",
      "purpose": "What this session is doing",
      "worktree_id": "UUIDv7 or null",
      "parent": "parent session_id or null",
      "created": "ISO 8601 timestamp",
      "status": "running | completed | failed",
      "notes": "Optional context"
    }
  ],
  "genealogy": {
    "session_id": {
      "parent": "parent_id or null",
      "children": ["child_id", ...],
      "depth": 0
    }
  },
  "last_synced": "ISO 8601 timestamp"
}
```

---

## Usage

The agent should:
1. **Initialize** these files during bootstrap (copy from .template files)
2. **Sync** at session start by querying Agor MCP
3. **Update** after creating/modifying resources
4. **Reference** when making decisions about work

---

## Templates

The `.template` files show the structure. Copy and customize during bootstrap.
