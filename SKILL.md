---
name: claude-code-teams
description: Production skill for orchestrating Claude Code's native agent teams feature. Use when: (1) Multi-lens reviews, (2) Competing hypotheses debugging, (3) Full-stack features, (4) Architecture debates, (5) Cross-domain investigations.
metadata:
  openclaw:
    emoji: "🎭"
    requires:
      bins: ["claude"]
      env: ["CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS"]
    version: "1.0.0"
    author: "OpenClaw Community"
    tags: ["agents", "teams", "claude-code", "orchestration", "coordination"]
---

# Claude Code Agent Teams

Production skill for orchestrating Claude Code's native agent teams feature. This skill provides templates, automation scripts, and best practices for coordinating multiple Claude Code agents working together on complex tasks.

## What This Skill Does

Claude Code's agent teams feature lets you spawn multiple AI agents that:
- Share a unified task list and coordinate work
- Send messages to each other via built-in mailbox
- Work in parallel on independent tasks
- Review each other's work before merging

This skill provides:
- **6 templates** for common team patterns
- **Automation scripts** for setup validation, monitoring, and cleanup
- **Best practices** from Claude Code docs + ClaudeFast patterns
- **Real-world examples** with step-by-step walkthroughs
- **Cost optimization** guidance (model mixing, team sizing)

## When to Use This Skill

✅ **Strong Use Cases:**
- **Multi-lens code reviews** - Security, performance, test coverage in parallel
- **Debugging with competing hypotheses** - Multiple agents test different theories
- **Full-stack features** - Frontend, backend, tests coordinated by separate specialists
- **Architecture decisions** - Adversarial debate produces stronger ADRs
- **Cross-domain investigations** - Bottleneck analysis across systems
- **Data-parallel work** - Inventory classification, bulk refactoring

## When NOT to Use This Skill

❌ **Poor Fit:**
- **Simple tasks** - Single agent is faster for straightforward work
- **Sequential dependencies** - Task B can't start until A completes
- **Same-file edits** - Multiple agents editing same file causes conflicts
- **Tight loops** - Agents need frequent back-and-forth (use single session)
- **Learning/exploration** - Single agent with full context learns faster
- **Budget constraints** - Teams multiply token costs (see Cost Management)

**Rule of thumb:** If coordination overhead > parallelization benefit, use single session.

## Quick Start

### 1. Enable Agent Teams Feature

**Option A: Environment Variable**

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

Add to your shell profile (`~/.zshrc`, `~/.bashrc`) to persist.

**Option B: Claude Settings File**

Add to `~/.claude/settings.json`:

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

### 2. Validate Setup

```bash
cd ~/.openclaw/workspace/skills/claude-code-teams
./scripts/validate-setup.sh
```

This checks:
- `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` is set
- `claude` CLI is available
- `tmux` is installed (optional, for split-pane display)

### 3. Choose a Template

Navigate to `templates/` and pick the pattern that matches your task:

- **parallel-review.md** - Multi-lens code review (security + performance + tests)
- **competing-hypotheses.md** - Debug by testing competing theories
- **fullstack-feature.md** - Frontend/backend/tests coordination
- **architecture-decision.md** - ADR with adversarial debate
- **bottleneck-analysis.md** - Cross-domain performance investigation
- **inventory-classification.md** - Data-parallel classification/refactoring

Each template includes:
- When to use this pattern
- Team structure (roles, boundaries)
- Copy-paste ready spawn prompts
- Expected outcomes

### 4. Spawn the Team

Open the template and copy the spawn prompt. Then:

```bash
# Start Claude Code in PTY mode (enables interactive teams)
claude --pty

# In Claude Code, paste the spawn prompt from the template
# Example from parallel-review.md:
# "I need a code review with 3 perspectives: security, performance, and test coverage..."
```

**Pro tip:** Use `delegate` mode in spawn prompt to prevent the lead agent from doing implementation work.

### 5. Monitor Progress

In a separate terminal:

```bash
./scripts/team-monitor.sh
```

This shows:
- Task list status (pending → in progress → complete)
- Active teammates and their current work
- Recent messages between agents

Or use Claude Code's built-in `Ctrl+T` to toggle task list view.

### 6. Review and Merge

Once teammates complete their tasks:
1. Review each teammate's output (files changed, recommendations)
2. Resolve any conflicts (rare if file boundaries were clear)
3. Merge approved changes

### 7. Cleanup

```bash
./scripts/team-cleanup.sh
```

This gracefully shuts down teammates before exiting Claude Code.

## Templates

### 1. Parallel Review (`parallel-review.md`)
**Use when:** You need multiple expert perspectives on a codebase or PR.

**Pattern:** Spawn 3 reviewers (security, performance, test coverage) who work independently then synthesize findings.

**Outcome:** Comprehensive review covering multiple domains in parallel.

---

### 2. Competing Hypotheses (`competing-hypotheses.md`)
**Use when:** Debugging a complex issue with multiple plausible causes.

**Pattern:** Spawn 2-3 agents, each assigned a different hypothesis to investigate. They report findings, then debate.

**Outcome:** Faster root cause identification through parallel investigation.

---

### 3. Full-Stack Feature (`fullstack-feature.md`)
**Use when:** Building a feature that spans frontend, backend, and tests.

**Pattern:** Spawn 3 specialists (frontend, backend, test engineer) with clear interfaces between them.

**Outcome:** Parallel implementation with coordinated integration.

---

### 4. Architecture Decision (`architecture-decision.md`)
**Use when:** Making a significant architectural choice (database, framework, design pattern).

**Pattern:** Spawn 2 agents with opposing positions. They debate, then synthesize into ADR.

**Outcome:** Stronger decision with explored alternatives and trade-offs.

---

### 5. Bottleneck Analysis (`bottleneck-analysis.md`)
**Use when:** Investigating performance issues across multiple systems.

**Pattern:** Spawn domain experts (database, network, application, frontend) to investigate in parallel.

**Outcome:** Faster identification of bottlenecks across stack.

---

### 6. Inventory Classification (`inventory-classification.md`)
**Use when:** Bulk work on independent items (refactoring, classification, migration).

**Pattern:** Divide items across N agents, each processes their subset independently.

**Outcome:** Parallelized work with linear speedup (e.g., 3 agents = 3x faster).

## Best Practices

### Clear File Boundaries
```markdown
❌ Bad: "Alice and Bob both work on user-service.ts"
✅ Good: "Alice owns user-service.ts, Bob owns user-repository.ts"
```

Prevent merge conflicts by giving each teammate exclusive file/directory ownership.

### Use Delegate Mode
```markdown
# In spawn prompt:
"You are the lead coordinator. Use delegate mode: assign tasks to teammates but don't implement yourself."
```

Prevents lead from racing ahead while teammates are still working.

### Size Tasks Appropriately
- **Too small:** High coordination overhead
- **Too large:** Risk of context loss, long-running tasks
- **Just right:** 5-6 self-contained tasks per teammate

### Wait for Completion
```markdown
"Wait for all teammates to finish their assigned tasks before proceeding to synthesis."
```

Don't let lead merge results while teammates are mid-task.

### Start with Research/Review
```markdown
# Before spawning builders, spawn researchers:
"Before implementation, spawn 2 agents to research: (1) existing patterns, (2) edge cases"
```

Builds shared understanding before divergent work.

### Cross-Role Reviews
- Builders review specs (before implementing)
- Reviewers check builds (after completing)

Catches misunderstandings early.

## Troubleshooting

See `references/troubleshooting.md` for detailed solutions. Common issues:

**"Teammates aren't spawning"**
- Check `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=true` is set
- Restart Claude Code after setting env var

**"Agents are editing the same file"**
- Fix file boundaries in spawn prompt
- Reassign tasks to eliminate overlaps

**"Lead is implementing instead of coordinating"**
- Add "Use delegate mode: coordinate but don't implement" to spawn prompt

**"Task list is overwhelming"**
- Break down into smaller, self-contained tasks (5-6 per teammate)
- Use dependencies to sequence work

**"Costs are higher than expected"**
- See Cost Management section
- Consider model mixing (Opus lead, Sonnet teammates)
- Reduce team size (3 often better than 6)

## Cost Management

Agent teams multiply token usage. Optimize with:

### Model Mixing
```bash
# Opus for lead (strategic decisions)
claude --model opus

# Then in spawn prompt:
"Spawn 3 teammates using Sonnet (they handle tactical work)"
```

**Impact:** ~40% cost reduction vs all-Opus team

### Minimize Broadcasts
```markdown
❌ "Broadcast to all: status update"
✅ "Message Alice directly: her task is blocking yours"
```

Broadcasts copy messages to all teammates (N × tokens).

### Right-Size Teams
- **3 teammates:** Usually optimal (parallelism without excessive coordination)
- **6 teammates:** Only for highly parallelizable work (inventory classification)
- **1 teammate:** Just use single session instead

**Rule:** More agents = more coordination overhead. Add teammates only when work is truly parallel.

### Estimated Costs
| Team Size | Duration | Model | Estimated Cost |
|-----------|----------|-------|----------------|
| 1 lead + 3 teammates | 30 min | Opus lead, Sonnet team | $15-25 |
| 1 lead + 3 teammates | 30 min | All Opus | $25-40 |
| 1 lead + 6 teammates | 30 min | Opus lead, Sonnet team | $25-40 |

*Actual costs depend on task complexity and message volume.*

See `references/cost-management.md` for detailed optimization strategies.

## Automation Hooks

Optional hooks for quality gates and auto-assignment:

### TeammateIdle.sh
Automatically assigns follow-up tasks when a teammate becomes idle.

```bash
# Enable in Claude Code:
export CLAUDE_CODE_HOOK_TEAMMATE_IDLE="~/.openclaw/workspace/skills/claude-code-teams/hooks/TeammateIdle.sh"
```

### TaskCompleted.sh
Enforces quality gates when a teammate marks a task complete (e.g., tests must pass).

```bash
export CLAUDE_CODE_HOOK_TASK_COMPLETED="~/.openclaw/workspace/skills/claude-code-teams/hooks/TaskCompleted.sh"
```

See `hooks/` directory for example implementations.

## Examples

Real-world walkthroughs with step-by-step instructions:

- **PR Review (`examples/pr-review-example.md`)** - Security + performance + test review in parallel
- **Bug Hunt (`examples/bug-hunt-example.md`)** - Competing hypotheses pattern for root cause analysis
- **Feature Build (`examples/feature-build-example.md`)** - Full-stack coordination (React + FastAPI + tests)

## References

- **`references/best-practices.md`** - When to use teams vs single session, task sizing, file boundaries
- **`references/cost-management.md`** - Token optimization, model mixing, team sizing strategies
- **`references/troubleshooting.md`** - Common issues from Claude Code docs + solutions
- **`references/prompting-guide.md`** - How to craft effective teammate spawn prompts

## Comparison with Other Approaches

| Approach | Best For | Avoid When |
|----------|----------|------------|
| **Single Claude Code session** | Sequential work, learning, exploration | Truly parallel independent work |
| **Claude Code teams** (this skill) | Parallel independent tasks, multi-lens reviews | Sequential dependencies, same-file edits |
| **OpenClaw subagents** | Background work, long-running tasks | Interactive coordination, shared state |
| **Multiple terminal sessions** | Completely isolated tasks | Need coordination, shared context |

## Contributing

Found a useful template or pattern? Contributions welcome:
1. Fork repo
2. Add template or example
3. Include: when to use, team structure, spawn prompt, expected outcomes
4. Submit PR with example output

## License

MIT License - see LICENSE file for details.

## Credits

Built with insights from:
- Claude Code official documentation
- ClaudeFast best practices
- OpenClaw agent-team-orchestration skill
- Community contributions

---

**Need help?** See `references/troubleshooting.md` or open an issue.

**Want to contribute?** See examples/ directory for template format.
