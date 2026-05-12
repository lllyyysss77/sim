```markdown
# sim Development Patterns

> Auto-generated skill from repository analysis

## Overview

This skill teaches you how to contribute to the `sim` monorepo, a TypeScript/Next.js project for workflow automation and integrations. You'll learn the repository's coding conventions, commit patterns, and the main development workflows—ranging from database migrations to API endpoints, tool integrations, i18n, feature development, and workflow block extensions. The guide includes code examples and suggested CLI commands for common tasks.

---

## Coding Conventions

**File Naming:**  
- Use `camelCase` for filenames.  
  _Example:_ `userProfile.ts`, `apiHandler.ts`

**Import Style:**  
- Use alias imports (e.g., `@/lib/foo`).
  ```ts
  import { fetchData } from '@/lib/api'
  ```

**Export Style:**  
- Use named exports.
  ```ts
  // Good
  export function doThing() { ... }
  export const CONSTANT = 42

  // Avoid default exports
  ```

**Commit Messages:**  
- Use [Conventional Commits](https://www.conventionalcommits.org/).
- Prefixes: `feat`, `fix`, `improvement`
- Example:  
  ```
  feat: add support for Slack integration
  fix: correct API error handling in user routes
  improvement: optimize workflow block rendering
  ```
- Average length: ~72 characters

---

## Workflows

### Add or Update Database Table
**Trigger:** When you need to add a new table or modify an existing one in the database.  
**Command:** `/new-table`

1. Create or modify a migration SQL file in `packages/db/migrations/`.
2. Update migration metadata in `packages/db/migrations/meta/`.
3. Update the TypeScript schema in `packages/db/schema.ts`.
4. Update application code to use the new or changed table.

_Example:_
```sql
-- packages/db/migrations/20240601_add_sessions.sql
CREATE TABLE sessions (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id),
  created_at TIMESTAMP DEFAULT NOW()
);
```
```ts
// packages/db/schema.ts
export interface Session {
  id: number
  userId: number
  createdAt: Date
}
```

---

### Add or Enhance API Endpoint
**Trigger:** When you want to expose new backend functionality or modify an API route.  
**Command:** `/new-api-endpoint`

1. Create or update a route file in `apps/sim/app/api/**/route.ts`.
2. Optionally, add or update a test in `apps/sim/app/api/**/route.test.ts`.
3. Update related service or library files in `apps/sim/lib/**`.
4. Update frontend or store logic if needed.

_Example:_
```ts
// apps/sim/app/api/user/route.ts
import { getUser } from '@/lib/userService'

export async function GET(req: Request) {
  const user = await getUser(req)
  return Response.json(user)
}
```
```ts
// apps/sim/app/api/user/route.test.ts
import { GET } from './route'
import { describe, it, expect } from 'vitest'

describe('GET /api/user', () => {
  it('returns user data', async () => {
    const res = await GET({ /* mock request */ } as any)
    expect(res.status).toBe(200)
  })
})
```

---

### Add or Update Tool or Trigger
**Trigger:** When you want to add a new integration (e.g., Slack, Gmail) or extend an existing one.  
**Command:** `/new-tool`

1. Create or update a block file in `apps/sim/blocks/blocks/*.ts`.
2. Create or update the tool implementation in `apps/sim/tools/{integration}/`.
3. Register the tool in `apps/sim/tools/registry.ts`.
4. Create or update a trigger in `apps/sim/triggers/{integration}/`.
5. Register the trigger in `apps/sim/triggers/registry.ts`.
6. Update docs in `apps/docs/content/docs/en/tools/{integration}.mdx`.
7. Update i18n translations if needed.

_Example:_
```ts
// apps/sim/tools/slack/sendMessage.ts
export function sendMessage(channel: string, text: string) { ... }

// apps/sim/tools/registry.ts
export { sendMessage } from './slack/sendMessage'
```
```mdx
<!-- apps/docs/content/docs/en/tools/slack.mdx -->
# Slack Integration
...
```

---

### Add or Update i18n Translations
**Trigger:** When you want to localize new features or update translations.  
**Command:** `/update-i18n`

1. Update language-specific docs in `apps/docs/content/docs/{lang}/**`.
2. Update the translation lock file `apps/docs/i18n.lock`.

_Example:_
```mdx
<!-- apps/docs/content/docs/es/tools/slack.mdx -->
# Integración de Slack
...
```
```json
// apps/docs/i18n.lock
{
  "es": { "updated": "2024-06-01" }
}
```

---

### Feature Development with UI and State
**Trigger:** When you want to add a major feature (e.g., live cursor, import/export, collaboration).  
**Command:** `/new-feature`

1. Implement or update UI components in `apps/sim/app/workspace/**` or `apps/sim/components/ui/**`.
2. Update or add hooks in `apps/sim/hooks/**`.
3. Update or add state/store files in `apps/sim/stores/**`.
4. Update backend logic in `apps/sim/lib/**` or `apps/sim/background/**`.
5. Update API routes if needed.
6. Update documentation if needed.

_Example:_
```ts
// apps/sim/stores/cursorStore.ts
import { create } from 'zustand'

export const useCursorStore = create(set => ({
  cursors: {},
  updateCursor: (id: string, pos: {x: number, y: number}) =>
    set(state => ({ cursors: { ...state.cursors, [id]: pos } }))
}))
```

---

### Add or Update Workflow Block or Subblock
**Trigger:** When you want to extend workflow builder capabilities (e.g., new trigger types, input mapping).  
**Command:** `/new-block`

1. Implement or update block/subblock components in  
   `apps/sim/app/workspace/[workspaceId]/w/[workflowId]/components/workflow-block/components/sub-block/components/`.
2. Update workflow block registry or types (`apps/sim/blocks/types.ts`).
3. Update stores for subblocks (`apps/sim/stores/workflows/subblock/**`).
4. Update supporting hooks or utils.
5. Update backend logic if needed.

_Example:_
```ts
// apps/sim/blocks/blocks/newTriggerBlock.ts
export function newTriggerBlock(props) { ... }

// apps/sim/blocks/types.ts
export type WorkflowBlock = 'trigger' | 'action' | 'newTriggerBlock'
```

---

## Testing Patterns

- Use [vitest](https://vitest.dev/) for all tests.
- Test files follow the pattern: `*.test.ts`
- Place test files next to the code they test or in the same directory.

_Example:_
```ts
// apps/sim/lib/math/add.test.ts
import { add } from './add'
import { describe, it, expect } from 'vitest'

describe('add', () => {
  it('adds two numbers', () => {
    expect(add(2, 3)).toBe(5)
  })
})
```

---

## Commands

| Command         | Purpose                                                        |
|-----------------|----------------------------------------------------------------|
| /new-table      | Add or update a database table (with migrations and schema)    |
| /new-api-endpoint | Add or enhance an API endpoint (with tests)                  |
| /new-tool       | Add or update an integration tool or trigger                   |
| /update-i18n    | Update or add i18n translations for docs and UI                |
| /new-feature    | Implement a new feature (UI, state, backend, docs)             |
| /new-block      | Add or update a workflow block or subblock                     |
```
