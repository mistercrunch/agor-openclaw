# Implement Missing Agor MCP Tools

**Context:** We need two MCP tools to complete the agor-claw bootstrap workflow.

---

## Missing Tools

### 1. `agor_boards_create`
### 2. `agor_repos_add` (or `agor_repos_create`)

---

## Background

The agor-claw agent loop framework needs these operations during bootstrap:

**From BOOTSTRAP.md:**
```markdown
**Ask the user:**
> "What board should I do most of my work under?"

**Then:**
1. List existing boards: `await agor.boards.list()` ✅ (exists)
2. Check if board exists
3. If not, create it: `await agor.boards.create({...})` ❌ (MISSING)
4. Record board ID in IDENTITY.md
```

Currently, board creation and repo addition must be done manually through the UI or command line. Agents should be able to do this via MCP.

---

## Implementation Requirements

### Tool 1: `agor_boards_create`

**Purpose:** Create a new board programmatically

**Input Parameters:**
```typescript
{
  name: string;              // Board name
  slug?: string;             // URL-friendly slug (auto-generate if not provided)
  description?: string;      // Optional description
  icon?: string;             // Optional emoji icon (default: 📋)
  color?: string;            // Optional hex color (default: #1677ff)
  background_color?: string; // Optional background (default: solid color)
}
```

**Output:**
```typescript
{
  board_id: string;          // UUIDv7
  name: string;
  slug: string;
  created_at: string;        // ISO 8601
  created_by: string;        // User ID
  icon: string;
  color: string;
  // ... other board fields
}
```

**Implementation Notes:**
- Should use existing board creation logic from Agor daemon
- Likely in `apps/agor-daemon/src/services/boards/boards.service.ts`
- Auto-generate slug from name if not provided (slugify)
- Validate slug uniqueness
- Set created_by to current user (from MCP session token)
- Return full board object

**Error Handling:**
- Duplicate slug → 409 Conflict
- Invalid parameters → 400 Bad Request
- Unauthorized → 401/403

---

### Tool 2: `agor_repos_add`

**Purpose:** Add a git repository to Agor

**Input Parameters:**
```typescript
{
  git_url: string;           // Git clone URL (https or git@)
  name?: string;             // Optional name (default: derive from URL)
  slug?: string;             // Optional slug (default: derive from URL)
  clone_path?: string;       // Optional custom path (default: ~/.agor/repos/[slug])
  auto_clone?: boolean;      // Whether to git clone immediately (default: true)
}
```

**Output:**
```typescript
{
  repo_id: string;           // UUIDv7
  name: string;
  slug: string;
  git_url: string;
  path: string;              // Filesystem path
  created_at: string;
  created_by: string;
  clone_status: 'pending' | 'cloning' | 'ready' | 'failed';
  clone_error?: string;      // If clone failed
}
```

**Implementation Notes:**
- Parse git_url to extract repo name/slug
  - `https://github.com/org/repo.git` → name: "repo", slug: "org/repo"
  - `git@github.com:org/repo.git` → same
- Create entry in repos table (see `packages/core/src/db/schema.ts`)
- If `auto_clone: true`, perform `git clone` in background
  - Use `simple-git` library (NEVER subprocess)
  - Clone to `~/.agor/repos/[derived-name]`
  - Update clone_status based on result
- Validate git_url is accessible
- Set created_by to current user

**Error Handling:**
- Invalid git_url → 400 Bad Request
- Clone fails → Set clone_status: 'failed', store error message
- Duplicate slug → 409 Conflict
- Unauthorized → 401/403

**Optional Enhancement:**
- Support polling clone status via `agor_repos_get` with clone_status field
- Or make synchronous (block until clone completes) with timeout

---

## File Locations (Agor Codebase)

**MCP Server:**
```
apps/agor-daemon/src/mcp/
├── mcp-handler.ts           # Main MCP request router
├── tools/                   # Tool implementations
│   ├── boards.ts            # Add agor_boards_create here
│   └── repos.ts             # Add agor_repos_add here (or create file)
└── schemas/                 # JSON schemas for validation
    ├── boards.ts
    └── repos.ts
```

**Services (Business Logic):**
```
apps/agor-daemon/src/services/
├── boards/
│   ├── boards.class.ts      # Board service (has create method)
│   └── boards.service.ts
└── repos/                   # May need to create this service
    ├── repos.class.ts
    └── repos.service.ts
```

**Database Schema:**
```
packages/core/src/db/schema.ts
- boards table (already exists)
- repos table (may need to add if not exists)
```

**Types:**
```
packages/core/src/types/
├── boards.ts                # Board types
└── repos.ts                 # Repo types (may need to create)
```

---

## Testing Checklist

### After Implementation:

**Test `agor_boards_create`:**
```typescript
// Via MCP HTTP endpoint
POST http://localhost:3030/mcp?sessionToken=...
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "agor_boards_create",
    "arguments": {
      "name": "Test Board",
      "description": "Created via MCP",
      "icon": "🧪"
    }
  },
  "id": 1
}

// Expected: Returns board object with board_id
// Verify: Check Agor UI, board appears
// Verify: Can list board via agor_boards_list
```

**Test `agor_repos_add`:**
```typescript
// Via MCP HTTP endpoint
POST http://localhost:3030/mcp?sessionToken=...
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "agor_repos_add",
    "arguments": {
      "git_url": "https://github.com/mistercrunch/agor-claw.git",
      "auto_clone": true
    }
  },
  "id": 2
}

// Expected: Returns repo object with repo_id
// Verify: Repo cloned to ~/.agor/repos/agor-claw
// Verify: Can create worktrees from this repo
// Verify: Appears in agor_worktrees_list with repo info
```

**Error Cases:**
- Duplicate board name → 409
- Invalid git URL → 400
- Network issue during clone → clone_status: 'failed'

---

## Success Criteria

✅ Agent can create boards during bootstrap without manual UI interaction
✅ Agent can add repos during bootstrap without manual git clone
✅ agor-claw BOOTSTRAP.md workflow completes fully autonomously
✅ Tests pass for both tools
✅ Error handling is robust
✅ Documentation updated (if MCP tool docs exist)

---

## References

**Existing MCP Tools to Reference:**
- `agor_worktrees_create` - Shows worktree creation pattern
- `agor_boards_list` - Shows board query pattern
- `agor_sessions_spawn` - Shows async operation pattern

**Agor Architecture Docs:**
- `context/concepts/architecture.md` - System design
- `context/concepts/mcp-integration.md` - MCP integration guide
- `apps/agor-daemon/src/mcp/README.md` - MCP implementation docs (if exists)

**Database Schema:**
- `packages/core/src/db/schema.ts` - Table definitions
- Look for `boards` and `repos` (or `repositories`) tables

---

## Implementation Approach

### Option 1: Quick & Simple
1. Add `agor_boards_create` to `apps/agor-daemon/src/mcp/tools/boards.ts`
2. Create `apps/agor-daemon/src/mcp/tools/repos.ts` with `agor_repos_add`
3. Wire up to MCP handler in `mcp-handler.ts`
4. Use existing service layer (boards service, create repos service if needed)
5. Test manually via HTTP endpoint

### Option 2: Full Implementation
1. Create repos service if doesn't exist
2. Add proper validation schemas
3. Add comprehensive error handling
4. Add tests (unit + integration)
5. Update MCP documentation
6. Add to MCP tool listing

**Recommendation:** Start with Option 1 (get it working), then enhance.

---

## Notes

- Use `simple-git` for all git operations (NEVER subprocess)
- Follow existing Agor patterns (look at worktrees implementation)
- Branded types: use `BoardID` and `RepoID` (UUIDv7)
- User attribution: use MCP session token to get current user
- Error messages: clear, actionable (help agent debug issues)

---

## Questions to Answer During Implementation

1. Does a `repos` table already exist in the schema?
2. Is there a repos service, or do we need to create it?
3. Should repo cloning be synchronous or async?
4. Do we need background job queue for clone operations?
5. Should we validate git URL accessibility before cloning?

---

**Once implemented, agor-claw bootstrap will be 100% autonomous!** 🚀

The agent will be able to:
1. Ask user for board name
2. Check if exists, create if not → `agor_boards_create`
3. Ask user for repos
4. Add repos to Agor → `agor_repos_add`
5. Create worktree → `agor_worktrees_create` ✅ (already exists)
6. Spawn POC session → `agor_sessions_spawn` ✅ (already exists)
7. Complete bootstrap without any manual steps!
