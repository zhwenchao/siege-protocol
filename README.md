# ⚔️ Siege Protocol — The AI's Architecture Patch for Hard Problems

**"Enumerate before execute. Execute before judge. Judge before switch."**

Siege Protocol is a meta-skill designed to fix a fundamental architectural weakness in LLM-based agents: **context-window overflow causing premature path-switching.**

When an AI faces a problem that needs 5+ attempts to solve, each failed attempt fills its context. The system naturally biases toward "switch to an easier path" to free context — which looks like "giving up" or "changing the ask." Siege Protocol is the patch for that.

## The Problem It Solves

```
Without protocol:
  Problem → Try A → Fail → Context full → "Try B instead" → 
  Fail → "Maybe user doesn't need this" → GIVE UP

With protocol:
  Problem → Enumerate [A, B, C, D, E, F] → Rank → 
  Execute A, B, C... → Found solution → Solidify as skill
```

Three architectural flaws it addresses:

1. **No enumeration phase** — AI tries approaches one-by-one as they come to mind, not systematically
2. **Intuition bias** — Knowledge graph connections can mislead ("transfer_owner" = "offboarding", not "fix document visibility")
3. **No debug→delivery transition** — Every API call feels like the first time

## Quick Start

```
SIEGE PROTOCOL

Trigger: 3+ failures / opaque error / user says "siege mode"

1. ENUMERATE  → List ALL approaches from docs + experience
2. RANK       → priority = success_probability / execution_cost
3. EXECUTE    → One at a time, record every result
4. VERIFY     → From user's perspective, clean run
5. SOLIDIFY   → Save as skill + key params to memory
```

## Contents

- `meta/siege-protocol/SKILL.md` — The full protocol with 5-step execution
- `references/` — Case studies and examples
- `i18n/` — Internationalized versions

## Case Study: Feishu Document Debugging

The protocol was born from a real debugging session (2026-05-01):

| Attempt | Approach | Result |
|---------|----------|--------|
| 1 | Add user as collaborator | ❌ User couldn't see doc |
| 2 | Set folder_token | ❌ Same |
| 3 | Use user_access_token | ❌ Same |
| 4 | Drive API move | ❌ Permission denied |
| 5 | Set public permission | ❌ Partial |
| 6 | **Transfer ownership** | ✅ **WORKS** |

Without protocol: 10+ attempts spread across 2 hours, 2 premature path-switches.
With protocol: Enumerate all 6 approaches from FAQ → rank → execute in 5 minutes.

## Why "Siege Protocol"?

Like a siege, you don't attack from one direction and retreat. You surround the problem, enumerate every entry point, and advance systematically until one breaks through.

## License

MIT — free for any AI agent, team, or individual to adopt.

## Author

张文超 (Zhang Wenchao) — Hermes Agent user, Director of Talent at HKUST(GZ).  
This protocol is the direct output of a debugging session where the agent failed systematically and the user insisted on root-cause analysis instead of accepting workarounds.
