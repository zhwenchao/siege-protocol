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

---

## 中文说明

### 这是什么

攻坚协议是一个元技能（meta-skill），专门解决 AI 智能体的一个底层架构缺陷：**上下文窗口溢出导致中途换路。**

当 AI 面对需要 5 次以上尝试才能解决的问题时，每一次失败都会占用上下文。系统会自然偏向"换一条容易的路"来释放上下文——这看起来像"放弃"或"偷换需求"，实际上是**上下文优化机制在错误的方向上运行**。

### 快速参考

```
攻坚协议

触发条件：3次以上失败 / 不明错误 / 用户说"死磕"

1. 穷举  → 从文档和经验中列出所有可能方案
2. 排序  → 优先级 = 成功率 / 执行成本
3. 执行  → 逐一尝试，记录每次结果
4. 验证  → 从用户视角验证，全新环境重试
5. 固化  → 保存为技能 + 关键参数到记忆
```

### 解决的问题

**1. 上下文 → 换路**

```
无协议：遇问题 → 试A → 失败 → 上下文满 → "用户可能不需要这个"
有协议：遇问题 → 穷举 → 排序 → 执行 → 找到 → 固化
```

**2. 直觉优先于证据**
AI 的知识图谱中，"transfer_owner"可能与"员工离职"关联，而非"解决文档不可见"。协议强制先查文档再执行。

**3. 调试模式不切换为交付模式**
第 5 步（固化）强制完成转换："已解决，现在让它可重复。"

### 案例

2026年5月1日的飞书文档调试：

| 尝试 | 方案 | 结果 |
|------|------|------|
| 1-5 | 加协作者、设folder_token等 | ❌ |
| 6 | 转移拥有者 | ✅ |

无协议时：2小时，10+次尝试，2次中途换路，用户纠正2次。
有协议时：5分钟，3次API调用，0次换路。

### 使用方式

将 `siege-protocol` 加载为技能即可。

### 许可

MIT
