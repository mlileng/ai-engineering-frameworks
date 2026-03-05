# GSD + Agent Teams: Full-Lifecycle Workflow

GSD and Agent Teams operate at different layers. GSD handles requirements, planning, state, and summaries. Agent Teams handles how work gets distributed and coordinated—whether that work is coding, writing specs, reviewing architecture, or maintaining documentation. This doc covers how the two layers connect in practice.

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

## The Bridge: AGENTS.md

The gap between GSD's planning output and Agent Teams' spawn input is a context bridge—something that translates plans into per-teammate instructions. The cleanest solution is `AGENTS.md`, an open cross-platform format for guiding AI agents. It's plain Markdown, has no required schema, and is supported by 25+ tools including Claude Code, Cursor, Copilot, Gemini CLI, and Devin. It's stewarded by the Agentic AI Foundation under the Linux Foundation.

The key capability is **nested file support**: place an AGENTS.md inside any subdirectory, and the closest one to the file being edited takes precedence. This means teammates self-configure from the filesystem instead of needing manually crafted spawn prompts.

### Root AGENTS.md — Project Constitution

A root-level `AGENTS.md` carries stable project-wide context that every teammate receives: project overview, architecture decisions, conventions, testing commands, security constraints, commit standards. This consolidates what GSD stores across `PROJECT.md`, `CLAUDE.md`, and `REQUIREMENTS.md` into a single file every agent reads automatically. Update it between milestones, not between phases.

### Nested AGENTS.md — Teammate Scope

At the end of GSD's `plan-phase`, generate a scoped `AGENTS.md` for each functional area a teammate will own—whether that's a source module, a specs directory, a docs folder, or a test suite. The teammate, assigned to that directory, picks up its context automatically.

```
/project
  AGENTS.md                  ← project-wide (from PROJECT.md + REQUIREMENTS.md)
  /specs
    AGENTS.md                ← requirements agent context
  /docs/architecture
    AGENTS.md                ← architecture reviewer context
  /src/auth
    AGENTS.md                ← auth coding teammate context
  /src/api
    AGENTS.md                ← API coding teammate context
  /tests
    AGENTS.md                ← quality agent context
```

### What Goes in Each File

**Root AGENTS.md:**
- Project name, purpose, and current milestone
- Tech stack and architecture summary
- Coding conventions and style rules
- Testing commands and CI expectations
- Commit message format
- Security constraints (files not to read/modify)
- Pointer to `STATE.md` for current session position

**Nested AGENTS.md (per teammate/module):**
- This teammate's role and responsibility
- File ownership—which files and directories this teammate owns
- Files this teammate must NOT touch
- Dependencies on other teammates (what to wait for, what to share)
- Module-specific conventions or constraints
- Acceptance criteria for this task set (derived from `PLAN.md`)
- How to signal completion to the team lead

### AGENTS.md vs. CLAUDE.md

Both serve the same purpose—persistent agent context—but AGENTS.md is cross-platform while CLAUDE.md is Claude Code specific with guaranteed native support. Practical recommendation: maintain both at the root level. Add a reference in CLAUDE.md pointing to AGENTS.md (e.g., "See AGENTS.md for cross-platform agent context and team structure")—this ensures Claude Code always picks up the cross-platform context regardless of how its native AGENTS.md support evolves. For nested teammate-scoped files, AGENTS.md is sufficient and avoids duplicating CLAUDE.md per directory.

---

## The Workflow

```
/gsd:discuss-phase N     ← requirements & decisions (GSD)
/gsd:plan-phase N        ← atomic task plans (GSD)
                         ← generate nested AGENTS.md per module (bridge)
/gsd:execute-phase N     ← spawn Agent Teams; teammates self-configure via AGENTS.md
/gsd:verify-work N       ← UAT and fix plans (GSD)
STATE.md / SUMMARY.md    ← session continuity (GSD)
```

The full layer model:

| Layer | Tool | Produces |
|---|---|---|
| Requirements and planning | GSD (`discuss-phase`, `plan-phase`) | `REQUIREMENTS.md`, `ROADMAP.md`, `PLAN.md` |
| Project constitution | Root `AGENTS.md` | Stable context for all agents |
| Per-teammate context | Nested `AGENTS.md` (from `plan-phase`) | Scoped instructions per file domain |
| Execution | Claude Agent Teams | Parallel work, inter-agent communication |
| State and continuity | GSD (`STATE.md`, `SUMMARY.md`) | Persistence across sessions |

---

## The Human Role

Throughout this workflow, agents handle completeness—making sure nothing falls through the cracks, keeping artifacts consistent, executing against plans. The human handles judgment:

- **During `discuss-phase`:** The agent asks questions; you decide which questions matter and when the spec is done
- **During `plan-phase`:** The agent proposes task breakdowns and team structure; you decide whether the plan is good enough to commit tokens to
- **During `execute-phase`:** Agents work in parallel; you monitor, redirect stuck teammates, resolve conflicts between competing approaches, and decide when output quality is sufficient
- **During `verify-work`:** The agent checks acceptance criteria; you decide whether "passing" actually means "done" for this audience and this moment

The risk at every stage is passivity—assuming the agents are on track because they look busy. Staying engaged means making the judgment calls that agents can't: prioritization, stakeholder dynamics, risk appetite, and knowing when "good enough" is the right call.

---

## When to Use Agent Teams vs. GSD Subagents

Agent Teams adds the most value when teammates genuinely need to communicate with each other—when one agent's output affects another agent's decisions. If tasks are truly independent (test runs, parallel searches, doc generation), GSD's native subagent execution is simpler and cheaper.

The richer the GSD planning artifacts, the better the Agent Teams execution. Vague plans produce vague teammate output regardless of how many agents you spawn.
