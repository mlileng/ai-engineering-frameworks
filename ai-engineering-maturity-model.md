# AI Engineering Maturity Model

A framework for assessing how mature your AI-assisted engineering practice is—across tools, processes, and organizational readiness. This is tool-agnostic. Whether you're using Claude Code, Cursor, Copilot, or something else, the progression applies.

The [7 Levels of Claude Code](./claude-code/7-levels-of-claude-code.md) describes how deeply an individual uses one tool. This model asks a different question: **how mature is your overall practice of building software with AI?**

You can be at Level 6 in Claude Code and still be at Level 2 here if you have no framework discipline. The two models are complementary.

### The Human Role Across Levels

As you move up levels, the human role doesn't decrease in importance—it changes in kind. At Level 1 you're doing the work. By Level 5 you're making the calls that shape the work. The through-line is: **agents handle completeness, humans handle judgment.**

An agent can make sure nothing falls through the cracks. It can surface every overdue commitment, check every spec for consistency, and keep documentation in sync. What it can't do is decide which cracks matter—which risk to accept, which stakeholder to prioritize, when "good enough" is the right call.

The specific things that stay human at every level:

- **Prioritization** — which problem matters most right now, given business context agents don't have
- **Stakeholder judgment** — knowing that a particular leader cares about X, or that a team needs a win more than another process
- **Risk appetite** — deciding whether to ship with a known gap or delay for completeness
- **Quality bar** — agents can check criteria, but someone decides what "good enough" means for this audience at this moment
- **Saying no** — agents are completion-oriented by design; they don't push back on scope the way a good PM does

The risk at every level is assuming the agent's confidence equals correctness. Higher maturity means trusting agents with more of the work—not trusting them with the judgment about what work matters.

---

## Level 1: Ad Hoc

AI tools are available but usage is reactive and unstructured. Engineers figure it out individually. There are no shared standards, no agreed-upon tools, and no way to tell who's getting value and who isn't.

**You're here if:** AI tool adoption varies wildly across the team, nobody has documented what works, and "using AI" means pasting things into a chat window.

**Human role:** Everything. You do the work; AI occasionally helps with isolated tasks.

---

## Level 2: Personal Workflows

Individual engineers have repeatable patterns. They've configured their tools, written prompts they reuse, and built personal muscle memory. But this knowledge lives in their heads—it doesn't transfer to teammates or survive someone changing roles.

**You're here if:** Your best engineers are productive with AI but new hires start from scratch, and there's no shared playbook.

**Human role:** Directing and refining. You decide what to build and how; AI executes within your patterns. You're still the bottleneck for every decision.

**What helps at this level:** Claude.md files, personal prompt libraries, Copilot/Cursor configuration committed to repos.

---

## Level 3: Connected Tooling

AI is integrated into actual systems, not just chat windows. Engineers interact with their real tools—GitHub, CI/CD, databases, project management—through AI rather than alongside it. The feedback loop tightens because AI can read context and write results back without manual copy-paste.

**You're here if:** Your AI tools connect to your development infrastructure and can take actions, not just generate text.

**Human role:** Orchestrating and validating. You connect the systems and define the workflows; AI operates within them. You validate that actions taken across systems are correct—the feedback loop is tighter, but you're still reviewing the output.

**What helps at this level:** MCP servers, IDE integrations, API connections, slash commands and hooks that codify team workflows.

---

## Level 4: Structured Planning

Projects use spec-driven frameworks to manage AI-assisted work. Instead of ad hoc prompting, there's a defined flow from requirements through planning to execution. Context is externalized into files rather than trapped in conversation windows. This is where you stop losing work to context rot.

**You're here if:** You use a framework (GSD, BMAD, cc-sdd, or similar) that separates planning from execution and produces artifacts that persist across sessions.

**Human role:** Setting the frame and approving plans. The agent interviews you for requirements, proposes plans, and executes them—but you decide when the spec is done, which tradeoffs to make, and whether the plan is good enough to execute. This is the first level where agents do work you used to do yourself (requirements drafting, test strategy), but you still approve every artifact before it moves forward.

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

Multiple AI agents work on the same project with defined roles, handoffs, and ownership. This isn't just parallel execution—it's coordinated work where agents share findings, respect file boundaries, and build on each other's output.

**You're here if:** You run multi-agent workflows with explicit team structures, file ownership, and inter-agent communication patterns.

**Human role:** Supervising and making judgment calls. You're no longer doing the work or even directing each step—you're watching multiple agents work and intervening when they need decisions only you can make. You set priorities, resolve conflicts between competing approaches, decide when output quality is sufficient, and redirect agents that are heading down unproductive paths. The risk here is passivity—assuming everything is fine because the agents look busy.

**What helps at this level:** Agent Teams with rich spawn prompts, `AGENTS.md` files (root for project constitution, nested per module for teammate scope) bridging planning and execution, split-pane monitoring (tmux), clearly defined file ownership maps.

### Subagents vs. Teams

Use **subagents** for focused, independent tasks that only need to report back (searches, test runs, doc generation). Use **agent teams** when agents genuinely need to share findings and coordinate—when one agent's output affects another agent's decisions.

### Beyond Code: Full-Lifecycle Agent Teams

Agent teams don't have to be all coders. The same coordination patterns—`AGENTS.md` scoping, shared task lists, inter-agent messaging—apply to non-coding project lifecycle work. A team might include:

- A **requirements agent** scoped to `/specs` that interviews stakeholders, drafts PRDs, and validates spec completeness
- An **architecture agent** scoped to `/docs/architecture` that reviews proposed changes against established patterns and flags drift
- A **documentation agent** scoped to `/docs` that keeps docs in sync with code changes
- A **quality agent** scoped to `/tests` that designs test strategies, writes acceptance criteria, and runs verification
- **Coding agents** scoped to their respective modules, doing the implementation work

Each gets a nested `AGENTS.md` defining its role, file ownership, and how it coordinates with the others. The planning framework (GSD, BMAD) produces the specs; the agents—coding and non-coding—execute against them.

The human role in mixed teams is the same as in code-only teams but with higher stakes: you're the one who decides whether the requirements agent has asked the right questions, whether the architecture agent's concerns outweigh the delivery timeline, and whether the documentation is aimed at the right audience. Agents handle completeness across all these domains. You handle the judgment calls that connect them.

What agents can genuinely own in non-coding roles:

- Keeping documentation in sync with code changes
- Mechanical consistency checks (style, formatting, naming conventions across specs)
- Tracking commitments against deadlines and surfacing what's overdue
- Generating first drafts of structured artifacts (PRDs, status reports, test plans)
- Cross-referencing decisions against earlier decisions for contradictions

What stays human even when agents do the legwork:

- Deciding when a spec is done (not just complete, but right)
- Choosing which stakeholder feedback to incorporate vs. defer
- Setting the quality bar for this release, this audience, this moment
- Navigating organizational dynamics that don't appear in any document

---

## Level 6: Autonomous Pipelines

Set it up, walk away, come back to completed work. Self-correcting loops execute tasks against explicit acceptance criteria, move to the next task in a fresh context window, and repeat until done. Failure triggers structured retry, not a dead stop.

**You're here if:** You define acceptance criteria upfront and AI handles planning, execution, verification, and error recovery with minimal human intervention.

**Human role:** Defining success criteria and reviewing results. You're out of the execution loop entirely. Your job is to define what "done" looks like precisely enough that agents can self-verify, then review the output when they're finished. The judgment call here is knowing when your acceptance criteria are specific enough to trust autonomous execution—and when they're not, which means you should be at Level 5 instead.

**What helps at this level:** Ralph Loop for autonomous retry, GSD for planning the pipeline, clear acceptance criteria for every task, fresh context windows to avoid degradation over long runs.

---

## The Progression

| Level | Human Role | AI Role | What Stays Human |
|---|---|---|---|
| 1. Ad Hoc | Does the work | Reactive helper | Everything |
| 2. Personal Workflows | Directs with personal patterns | Personalized executor | All decisions, all structure |
| 3. Connected Tooling | Orchestrates across systems | Integrated actor | Workflow design, output validation |
| 4. Structured Planning | Sets the frame, approves plans | Spec-driven planner and executor | Tradeoff calls, scope decisions, "done" judgment |
| 5. Coordinated Execution | Supervises, intervenes on judgment calls | Coordinated team (code and non-code) | Prioritization, stakeholder judgment, quality bar |
| 6. Autonomous Pipelines | Defines criteria, reviews results | Self-correcting system | Success criteria design, knowing when not to automate |

Most engineering teams find their sustainable sweet spot at Levels 3–4. Levels 5–6 become essential for large projects or teams that need to scale AI-assisted output beyond what one engineer can supervise.

**Don't skip levels.** Each one builds capability that the next level depends on. Teams that jump to multi-agent execution without framework discipline (Level 4) waste tokens on uncoordinated work. Teams that try autonomous pipelines without connected tooling (Level 3) can't close the feedback loop.

**The human role doesn't shrink—it changes.** At every level, agents handle more of the completeness work (making sure nothing falls through the cracks) while humans handle the judgment work (deciding which cracks matter). The danger is confusing maturity with autonomy. Higher maturity means trusting agents with more of the work, not trusting them with the judgment about what work matters.

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
