# AI Engineering Maturity Model

A framework for assessing how mature your AI-assisted engineering practice is—across tools, processes, and organizational readiness. This is tool-agnostic. Whether you're using Claude Code, Cursor, Copilot, or something else, the progression applies.

The [7 Levels of Claude Code](./claude-code/7-levels-of-claude-code.md) describes how deeply an individual uses one tool. This model asks a different question: **how mature is your overall practice of building software with AI?**

You can be at Level 6 in Claude Code and still be at Level 2 here if you have no framework discipline. The two models are complementary.

---

## Level 1: Ad Hoc

AI tools are available but usage is reactive and unstructured. Engineers figure it out individually. There are no shared standards, no agreed-upon tools, and no way to tell who's getting value and who isn't.

**You're here if:** AI tool adoption varies wildly across the team, nobody has documented what works, and "using AI" means pasting things into a chat window.

---

## Level 2: Personal Workflows

Individual engineers have repeatable patterns. They've configured their tools, written prompts they reuse, and built personal muscle memory. But this knowledge lives in their heads—it doesn't transfer to teammates or survive someone changing roles.

**You're here if:** Your best engineers are productive with AI but new hires start from scratch, and there's no shared playbook.

**What helps at this level:** Claude.md files, personal prompt libraries, Copilot/Cursor configuration committed to repos.

---

## Level 3: Connected Tooling

AI is integrated into actual systems, not just chat windows. Engineers interact with their real tools—GitHub, CI/CD, databases, project management—through AI rather than alongside it. The feedback loop tightens because AI can read context and write results back without manual copy-paste.

**You're here if:** Your AI tools connect to your development infrastructure and can take actions, not just generate text.

**What helps at this level:** MCP servers, IDE integrations, API connections, slash commands and hooks that codify team workflows.

---

## Level 4: Structured Planning

Projects use spec-driven frameworks to manage AI-assisted work. Instead of ad hoc prompting, there's a defined flow from requirements through planning to execution. Context is externalized into files rather than trapped in conversation windows. This is where you stop losing work to context rot.

**You're here if:** You use a framework (GSD, BMAD, cc-sdd, or similar) that separates planning from execution and produces artifacts that persist across sessions.

### Framework Landscape

Choosing the right framework depends on project scope and team context.

**GSD (Get Shit Done)** — Context engineering layer focused on preventing context rot. Externalizes state into files, splits work into small plans, executes each in a fresh context window. Lightweight, fast to set up, pragmatic. Best for projects under one week or single-role scope.

**BMAD Method** — Full role-based agile framework with specialized agents (Analyst, PM, Architect, Developer, QA). Scale-adaptive—adjusts planning depth based on project complexity. Document handoff chain (PRD to Architecture to Stories to Implementation) plays well with long-context reasoning. Higher upfront learning investment. Best for projects spanning multiple weeks or requiring stakeholder-facing artifacts like PRDs and architecture docs.

**GitHub Spec Kit / cc-sdd** — Lightweight end of the spectrum. Templates and commands where you remain the orchestrator. Kiro-inspired structured requirements-to-design-to-tasks workflow. Good for formalizing requirements on smaller scopes without process overhead.

**SuperClaude** — Less a lifecycle framework, more a capability layer. 30 commands, 16 specialized agents, 7 behavioral modes. Enriches available behaviors rather than imposing project lifecycle structure.

**Ralph Loop** — Not a standalone framework but an execution loop primitive. Agents iterate until explicit success criteria are met, with failure as structured feedback. Pairs well with BMAD or GSD—they provide the "what and why," Ralph provides the autonomous "how."

### When to Graduate Frameworks

- Start with GSD for single-role, short-scope projects
- Graduate to BMAD when the project spans multiple weeks, multiple roles, or requires stakeholder-facing documentation
- Use lightweight options (cc-sdd, Spec Kit) when you want structure without ceremony
- Ralph Loop layers on top of any framework when you need autonomous retry behavior

---

## Level 5: Coordinated Execution

Multiple AI agents work on the same project with defined roles, handoffs, and ownership. This isn't just parallel execution—it's coordinated work where agents share findings, respect file boundaries, and build on each other's output. The human's role shifts from doer to supervisor and strategic decision-maker.

**You're here if:** You run multi-agent workflows with explicit team structures, file ownership, and inter-agent communication patterns.

**What helps at this level:** Agent Teams with rich spawn prompts, `AGENTS.md` files (root for project constitution, nested per module for teammate scope) bridging planning and execution, split-pane monitoring (tmux), clearly defined file ownership maps.

### Subagents vs. Teams

Use **subagents** for focused, independent tasks that only need to report back (searches, test runs, doc generation). Use **agent teams** when agents genuinely need to share findings and coordinate—when one agent's output affects another agent's decisions.

---

## Level 6: Autonomous Pipelines

Set it up, walk away, come back to completed work. Self-correcting loops execute tasks against explicit acceptance criteria, move to the next task in a fresh context window, and repeat until done. Failure triggers structured retry, not a dead stop.

**You're here if:** You define acceptance criteria upfront and AI handles planning, execution, verification, and error recovery with minimal human intervention.

**What helps at this level:** Ralph Loop for autonomous retry, GSD for planning the pipeline, clear acceptance criteria for every task, fresh context windows to avoid degradation over long runs.

---

## The Progression

| Level | Human Role | AI Role | Key Shift |
|---|---|---|---|
| 1. Ad Hoc | Does everything, AI assists occasionally | Reactive helper | — |
| 2. Personal Workflows | Directs AI with personal patterns | Personalized executor | Individual productivity |
| 3. Connected Tooling | Orchestrates AI across real systems | Integrated actor | System integration |
| 4. Structured Planning | Reviews and approves AI-generated plans | Spec-driven planner and executor | Framework discipline |
| 5. Coordinated Execution | Supervises multi-agent work | Coordinated team | Parallel work with handoffs |
| 6. Autonomous Pipelines | Defines criteria, reviews results | Self-correcting system | Human exits the loop |

Most engineering teams find their sustainable sweet spot at Levels 3–4. Levels 5–6 become essential for large projects or teams that need to scale AI-assisted output beyond what one engineer can supervise.

**Don't skip levels.** Each one builds capability that the next level depends on. Teams that jump to multi-agent execution without framework discipline (Level 4) waste tokens on uncoordinated work. Teams that try autonomous pipelines without connected tooling (Level 3) can't close the feedback loop.

---

## Open Questions

- What are the right token cost thresholds for when Agent Teams ROI justifies the overhead vs. sequential execution?
- Does BMAD's role-based handoff pattern (Scrum Master to Developer) add value or redundant ceremony when layered on Agent Teams?
- Can Ralph Loop's retry logic be applied at the individual teammate level, or only at the team lead level?
- What's the right granularity for nested AGENTS.md files—one per module, per phase, or per teammate role?
- Can GSD's `plan-phase` automatically generate nested AGENTS.md files, or does this require a custom command?
- When a teammate modifies its scope mid-execution, should it update its own AGENTS.md or flag to the lead?
- What triggers the decision to use Agent Teams vs. GSD's native subagent execution for a given phase?
- How does STATE.md handle mid-phase interruptions when Agent Teams are running—partial teammate completions?
