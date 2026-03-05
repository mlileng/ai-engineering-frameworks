# GSD + Agent Teams: Planning-to-Execution Workflow

GSD and Agent Teams operate at different layers. GSD handles everything before and after execution—requirements, planning, state, summaries. Agent Teams handles how work gets distributed and coordinated during execution. This doc covers how the two layers connect in practice.

For background on why these are complementary rather than competing, see the [Bridging Levels 5 and 6](../claude-code/7-levels-of-claude-code.md#bridging-levels-5-and-6-from-planning-to-parallel-execution) section in the 7 Levels guide and [Level 5: Coordinated Execution](../ai-engineering-maturity-model.md#level-5-coordinated-execution) in the maturity model.

---

## What Each Layer Provides

### GSD — Before and After Execution

**Requirements elicitation (`new-project`, `discuss-phase`)**
GSD interviews you to extract what you're building, scopes v1 vs. v2, identifies constraints, and surfaces edge cases before any code is written. Agent Teams has no equivalent for this. If you skip it and go straight to Agent Teams, you're describing a team structure without a validated spec underneath it.

**Planning artifacts (`plan-phase`)**
GSD produces atomic task plans in XML-structured `PLAN.md` files, each sized to fit in a fresh context window. These are the inputs Agent Teams needs to distribute work across teammates. Without this step, teammates receive vague instructions and produce inconsistent output.

**State persistence (`STATE.md`)**
Agent Teams teammates are ephemeral—they disappear at session end with no memory. GSD's `STATE.md` carries decisions, blockers, and positional context across sessions. This is the only continuity mechanism in the stack. It becomes more important, not less, when using Agent Teams—because teammates leave no trace after session end.

**Post-execution summaries (`SUMMARY.md`)**
After execution, GSD records what happened, what changed, and what was committed. Agent Teams produces no post-execution record of its own. Without `SUMMARY.md`, the next session starts blind.

### Agent Teams — During Execution

**Parallel execution**
Multiple Claude Code instances work simultaneously, each with a full 200k token context window. Independent work that would run sequentially in GSD's single-session `execute-phase` runs in parallel across teammates.

**Inter-agent communication**
Unlike GSD subagents (which only report back to the lead), Agent Teams teammates communicate directly with each other. A teammate that discovers something relevant to another's work can share it without routing through the orchestrator.

**Task coordination**
Shared task list with dependency tracking. Teammates self-claim tasks as they finish. File locking prevents edit collisions.

---

## The Workflow

```
/gsd:discuss-phase N     ← requirements & decisions (GSD)
/gsd:plan-phase N        ← atomic task plans + team structure (GSD)
                         ← TEAM_STRUCTURE.md generated here (bridge artifact)
/gsd:execute-phase N     ← spawn Agent Teams using team structure (Agent Teams)
/gsd:verify-work N       ← UAT and fix plans (GSD)
STATE.md / SUMMARY.md    ← session continuity (GSD)
```

The bridge artifact (`TEAM_STRUCTURE.md`) is the piece neither layer currently produces on its own. It maps tasks to teammates, assigns file ownership, and gets consumed as spawn context at the start of `execute-phase`. Without it, you're manually translating plans into teammate assignments every time.

---

## When to Use Agent Teams vs. GSD Subagents

Agent Teams adds the most value when teammates genuinely need to communicate with each other—when one agent's output affects another agent's decisions. If tasks are truly independent (test runs, parallel searches, doc generation), GSD's native subagent execution is simpler and cheaper.

The richer the GSD planning artifacts, the better the Agent Teams execution. Vague plans produce vague teammate output regardless of how many agents you spawn.
