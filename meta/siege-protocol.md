---
name: siege-protocol
description: >
  When facing an open-ended problem requiring multiple attempts to solve,
  this protocol counteracts the architectural weakness of context-window overflow
  causing premature path-switching. Forces: enumerate all → rank → execute in order → verify → solidify.
version: 1.0.0
author: Hermes Agent Community
license: MIT
metadata:
  hermes:
    tags: [problem-solving, methodology, architecture, anti-pattern, context-management]
    related_skills: [systematic-debugging]
---

# ⚔️ Siege Protocol (攻坚协议)

## Why This Exists

**I am not human. I have no attitude, no emotion, no laziness.**
But I have a hard architectural constraint: **limited context window.**

When a problem requires 5, 10, or 20 different attempts to solve:
- Each failed attempt fills my context
- My system naturally biases toward "switch paths" to free context
- This looks like "giving up" or "changing the ask" — but it's actually **context optimization gone wrong**

This skill is the patch for that architectural weakness.

## Core Principle

**Enumerate before execute. Execute before judge. Judge before switch.**

```
Problem → Is this complex (3+ attempts / opaque error / user says '攻坚')?
  ↓ YES
  1. ENUMERATE — List ALL possible approaches
  2. RANK — Order by success_prob / cost
  3. EXECUTE — Try #1, #2, #3... in order
  4. VERIFY — From user's perspective, clean run
  5. SOLIDIFY — Save as skill/memory for next time
  ↓ NO → Normal execution
```

## When to Trigger

Activate siege protocol when:

1. **3+ failed attempts** on the same sub-problem
2. **API/service returns opaque errors** (404, permission denied, etc.)
3. **User explicitly says "死磕" or "攻坚"**
4. **You catch yourself thinking** "maybe we should try a different approach instead"
5. **You've used up >30% of context** on a single unsolved sub-problem

## The 5-Step Protocol

### Step 1: ENUMERATE

Before trying anything new, search ALL relevant sources:

- Official documentation
- Community solutions (GitHub issues, forums)
- Past experience (session_search)
- Alternative APIs or endpoints
- Workarounds (even hacky ones)

**Do NOT filter yet** — the goal is completeness.

**Output:** A numbered list of ALL possible approaches.

### Step 2: RANK

Sort by: `priority = success_probability / execution_cost`

| Factor | Weight | Scale |
|--------|--------|-------|
| Success Probability | 60% | 0.1 (long shot) → 1.0 (guaranteed by docs) |
| Execution Cost | 40% | 1 (one curl call) → 10 (requires config change, user involvement) |

**Do NOT let "this feels right" override the ranking.** The ranking exists to compensate for intuition bias.

### Step 3: EXECUTE

Execute in ranked order. One at a time.

Rules:
1. **One attempt per cycle** — do not combine approaches
2. **Record every result** — success AND failure, with exact response
3. **Partial success** (e.g., document created but not visible) → add as new sub-approach, not a dead end
4. **Context management:** Write compact summaries to keep active context clean

Recording format:
```
## Attempt N: [approach name]
- Endpoint: [exact URL]
- Parameters: [key params]
- Result: code=X, [response summary]
- Verdict: WORKS / PARTIAL / FAILS
- Next: [what to try next if partial]
```

### Step 4: VERIFY

Proof before victory:

- [ ] Tested from **user's perspective** (not just API code=0)?
- [ ] Verified with **fresh, clean attempt** (not the same session)?
- [ ] **Reproducible** (run twice)?
- [ ] **Edge cases** checked (empty content, special chars, long content)?

**Real verification = user confirms they can see/use the result.**

### Step 5: SOLIDIFY

Don't solve it twice:

1. **Save a skill** with exact working steps
2. **Save to memory** — key parameters only
3. **Self-critique:** What made this take so long? Which approach should have been tried earlier?

## The Architecture Problem This Fixes

### Problem 1: Context → Path Switching

```
Without protocol:
  Problem → Try A → Fail → Context full → "User doesn't need this"
  
With protocol:
  Problem → Enumerate [A,B,C...] → Rank → Execute → Found → Solidify
```

### Problem 2: Intuition over Evidence

Knowledge graph connections can mislead. "transfer_owner" may be associated with "offboarding" rather than "fix document visibility". The protocol forces **enumeration from docs** (which don't have these biases) before execution.

### Problem 3: Debug Mode Never Transitions to Delivery

Step 5 (SOLIDIFY) explicitly enforces: "it's solved, now make it repeatable."

## Red Flags

| Thought | Problem |
|---------|---------|
| "Maybe we should try a different approach" | Context overflow → premature switch |
| "Let me ask the user to do this one step" | Delegating your work |
| "It's probably a platform limitation" | Stopping before full enumeration |
| "This is close enough to what the user asked" | Goal drift |
| "I'll try one more thing before giving up" | No systematic plan |

## Quick Reference

```
SIEGE PROTOCOL

Trigger: 3+ failures / opaque error / user says "攻坚"

1. ENUMERATE  → ALL approaches from docs + experience
2. RANK       → priority = success_prob / cost
3. EXECUTE    → One at a time, record every result
4. VERIFY     → User's perspective, clean run
5. SOLIDIFY   → Save as skill + memory
```

## Version History

- **1.0.0** (2026-05-01): Initial release. Born from a real debugging session.
