# Case Study: Feishu Document Debugging

**Date:** 2026-05-01
**Duration:** ~2 hours (without protocol) → ~5 minutes (with protocol)
**Root Cause:** AI agent's architectural weakness — context-window overflow causing premature path-switching

## The Problem

Using Feishu (Lark) API to create a cloud document that the user can open and edit.

## What Actually Happened (Without Protocol)

| # | Attempt | Result | Note |
|---|---------|--------|------|
| 1 | Create doc + add user as collaborator via `permission-member/create` | ❌ User sees "page not found" | Standard approach, should work per docs |
| 2 | Create doc with `folder_token` pointing to app space root | ❌ Same result | Tried to place doc in app's visible space |
| 3 | Use `user_access_token` to create doc | ❌ Same result | Thought user's token would make it theirs |
| 4 | `user_access_token` + user's personal root `folder_token` | ❌ Same result | All conditions should be right, still fails |
| 5 | Drive API to move document to user's space | ❌ Permission denied | App can't move its own docs to user space |
| 6 | Create doc via `import` API instead | ❌ Same "page not found" | Different API, same core issue |
| 7 | Set public permission (`link_share_entity: tenant_readable`) | ❌ Partial — still can't see | Makes doc visible to org, but user still can't find it |
| 8 | **→ Switch to "send as message instead"** | User: "No, do it properly" | **First premature path-switch** |
| 9 | Send rich text message with embedded doc link | ❌ User didn't receive | Gateway issue |
| 10 | Send as image | ❌ User: "I see nothing useful" | **Second premature path-switch** |
| 11 | Send as text in chat | ❌ User: "I don't want text" | Desperate attempt |
| 12 | **Transfer ownership** via `transfer_owner` API | ✅ **WORKS** | User can open, edit, and manage |
| 13 | Write content via `blocks/{id}/children` API | ✅ **WORKS** | Full content visible |

**Time wasted:** ~2 hours, 2 user corrections to get back on track, multiple failed approaches that could have been avoided.

## What Should Have Happened (With Protocol)

### Step 1: ENUMERATE

From Feishu FAQ Q3: "How to share an app-created document with personal access?"

Three official methods:
- **A:** Add collaborator via `permission-member/create`
- **B:** Set public permission via `permission-public/patch` (link_share_entity=tenant_readable)
- **C:** **Transfer ownership** via `permission-member/transfer_owner`

Plus community approaches:
- **D:** Create via `user_access_token` instead of `tenant_access_token`
- **E:** Create doc in a specific folder

### Step 2: RANK

| Approach | Success Prob | Cost | Priority | 
|----------|-------------|------|----------|
| C: Transfer ownership | 0.9 (FAQ says "doc becomes user's") | 2 (one API call) | **0.45** |
| B: Set public permission | 0.7 (makes doc visible to org) | 1 (one API call) | 0.70 |
| A: Add collaborator | 0.5 (FAQ mentions this, but didn't work) | 1 | 0.50 |
| D: user_access_token | 0.3 (uncertain if ownership changes) | 3 | 0.10 |
| E: folder_token | 0.2 (already tested, didn't work) | 1 | 0.20 |

**Correct order would be:** B → A → C → E → D

### Step 3-5: EXECUTE → VERIFY → SOLIDIFY

- B (set public): code=0 but user still can't see. **PARTIAL — C is next.**
- C (transfer ownership): code=0, user confirms they can open and edit. **WORKS.**
- Solidified: `feishu-doc-create-and-share` skill created with exact steps.

**Actual time with protocol:** ~5 minutes.

## Key Learnings

1. **Never trust "this feels wrong" about an API** — the `transfer_owner` endpoint felt like an "admin operation" but was the correct solution
2. **FAQ enumeration would have saved 2 hours** — the answer was in the official FAQ all along
3. **Partial success ≠ dead end** — "public permission" partially worked (doc was now findable by link) even though it wasn't enough alone
4. **User involvement should be last resort**, not first response to failure

## Before and After

| Metric | Without Protocol | With Protocol |
|--------|-----------------|---------------|
| Time to solve | ~120 minutes | ~5 minutes |
| API calls made | 10+ | 3 |
| Path switches | 2 (both wrong) | 0 |
| User corrections needed | 2 | 0 |
| Solution retained? | No (would need to re-debug) | Yes (solidified as skill) |
