# The 7 Levels of Claude Code: From Beginner to Fully Autonomous

**Original framework by [Simon Scrapes](https://x.com/simonscrapes)** | Adapted with engineering-focused examples

I've spent significant time studying Claude Code—breaking down creator Boris Cherny's work, testing real workflows from top builders, and analyzing what actually works at scale. Here's what most people miss: even if you think you're using Claude Code well today, you'll probably get stuck at level 2 or 3.

This guide walks you through all seven levels—from beginner workflows to fully autonomous systems—so you can skip the trial and error. It adapts Simon Scrapes' original framework with practical examples tailored for engineering teams.

---

## **Level 1: Plan Before You Build**

The foundational habit that separates beginners from results-getters is using **Plan Mode** (Shift+Tab). Instead of typing and hoping, you work in read-only mode where Claude analyzes your codebase, proposes a plan, and asks clarifying questions before executing anything.

The second critical element is the **Claude.md file**—essentially an onboarding document for Claude. It tells Claude how you work: your architecture decisions, coding standards, mistakes to avoid, and non-negotiables. Keep it under 30 instructions. A human freelancer taking over should find it useful.

---

## **Level 2: Teach Claude Your Rules**

Once Claude understands your project through Claude.md, refine it based on actual output. When Claude generates code that doesn't match your preferences, add clarifying rules to Claude.md and reload with `/clear`. You're now getting personalized output, not generic AI.

---

## **Level 3: Build Repeatable Workflows**

This is where you stop explaining the same workflows over and over.

**Slash Commands** are saved prompts you trigger manually. Create `.claude/commands/pr-summary.md` with your full prompt template, and now `/pr-summary my-branch` executes it every time without retyping.

**Skills** are upgraded slash commands—background knowledge Claude loads automatically when relevant. Store them in `.claude/skills/code-review/` with a SKILL.md file and supporting documentation. Claude applies them without you typing anything.

**Hooks** are automatic, non-thinking triggers that run after Claude acts (no LLM tokens). Store validation logic in `.claude/settings.json` to automatically lint code, check for banned words, or validate formatting after every write.

**Memory aid:** Skills = how Claude thinks | Hooks = what happens after Claude acts | Commands = manually triggered prompts

---

## **Level 4: Connect to Your Tools**

**MCP (Model Context Protocol) servers** bridge Claude Code with external apps like GitHub, Airtable, Notion, Slack. Instead of copying/pasting data, Claude connects directly. Create `.mcp.json` with your MCP configuration, add API credentials, restart Claude, and you're connected. Now Claude can read from your actual systems and write results back automatically.

---

## **Level 5: Let Claude Plan, You Supervise**

Instead of you thinking and Claude executing, use the **GSD Framework** to have Claude plan in high detail, then you supervise. GSD breaks large projects into phases with a plan-execute-verify loop. Keep context in separate files (project roadmap, state, phase plans) instead of one long conversation. This solves **context rot**—when your context window fills up and Claude loses detail.

---

## **Level 6: Run Teams of Agents**

Instead of one Claude doing everything, split work among **sub-agents** (specialized personas). Each agent gets fresh, isolated context, so they work faster and cheaper. Run them sequentially (one finishes, next starts) for specialization, or run them in parallel (multiple terminal tabs) for speed.

Create agents in `.claude/agents/` with a `.md` file describing their role and tools. You can run up to 5+ Claude instances in parallel. Use `--dangerously-skip-permissions` to stop asking for approval on every action.

---

## **Level 7: Build Fully Autonomous Systems**

Set it up, walk away, come back to completed work. Everything lives in a **route loop**—three files: a bash script telling Claude when to stop, a PRD.json file defining tasks with acceptance criteria, and the route loop plugin.

How it works: Claude executes a task, checks if it meets your acceptance criteria, moves to the next task in a fresh context window (avoiding context rot), and repeats until all tasks are done.

**Ralph Loop vs. GSD Framework:**
- **Ralph** = Executor (best for well-scoped tasks: "Generate 6 PR summaries")
- **GSD** = Planner + Executor (best for large projects: "Build Q4 strategy")

---

## **The Progression**

1. **Levels 1–2:** You think, Claude executes (with your rules)
2. **Level 3:** Repeatable actions (commands, skills, hooks)
3. **Level 4:** Connected to your real apps (MCPs)
4. **Level 5:** You supervise, Claude plans + executes (GSD)
5. **Level 6:** Multiple Claude agents working in parallel
6. **Level 7:** Fully autonomous pipelines running without you

Most teams find their sweet spot at Levels 3–4. As projects grow, Levels 5–7 become essential.

Start with Level 1 (planning and Claude.md). Once that becomes natural, move to Level 3 (your first slash command). From there, the progression is clear—each level builds on the previous one.