# Comprehensive Agentic Coding Setup

Based on a thread by Jamon Holmgren ([@jamonholmgren](https://x.com/jamonholmgren))

## Philosophy

Most people miss giant chunks of what makes agentic coding work well, and it's hurting their results. This is a full agentic setup dump — the infrastructure and habits that make the difference between dry, hand-held prompting and an agent that can genuinely work autonomously and reliably.

If you have all of this in place, your agentic coding experience is going to be very different from manually guiding a model toward the right thing every time.

## The Setup

### 0. AGENTS.md as a router

An `AGENTS.md` file that doesn't try to contain everything, but instead routes the agent to the right skills, docs, and tools for the task at hand.

### 1. A standard workflow doc/skill

A workflow doc customized to your needs (grab Matt Pocock's skills if you don't already have something similar). Tag it in most sessions, e.g. `@/AGENT_WORKFLOW.md`, so it gets pulled into context.

### 2. Self-healing docs for every system

Agents are instructed to keep documentation updated as they work. Tag the docs you know you need, or let the agent discover them through `AGENTS.md`. Put a detailed summary in the first 7 lines of every doc so they're easily greppable, and document this convention in `AGENTS.md`.

### 3. Agents always run the app

The agent should always actually run the app itself, test its own work, and fix issues as it goes — especially when running autonomously or asynchronously.

### 4. End-to-end tests, kept current

Write end-to-end tests and instructions for writing more. Keep them up to date. Maintain docs on how to write tests, what to avoid, and a list of all tests with what they test (yet another markdown doc). Write and run targeted tests during implementation, and improve/commit them alongside the work.

### 5. Custom linters at pre-commit hooks

Linters that look for problems specific to your codebase, with `--fix` fixing them automatically. If auto-fix isn't feasible, shell out to a cheaper LLM (e.g. a smaller/faster model) to fix the problems — not just flag them, but actually produce cleaned code.

### 6. Cross-agent review at each major point

Review at research, plan, implementation, and wrap-up stages — using a different model than the one that did the work (Codex, Claude, Cursor, whatever), never the same model reviewing its own output. Maintain specific docs for agent review: what to look for, how to approach it. Use personas that review from different angles (maintainability, code quality, security, performance, "AI smells," domain expertise like "financial services expert"). Each persona also "owns" a set of system docs and keeps them up to date.

### 7. Agent traces / worksheets

Track what the agent is doing each session in a worksheet. If the agent fails partway through, another agent should be able to pick up that worksheet and finish the job. Commit the worksheet with the work so it's all connected and easy to reference later — you will reference these later. Apply git tags corresponding to specific worksheet names so they're easy to find.

### 8. Automatic agent feedback

At the end of a session, the agent adds feedback to a doc that's committed with the work. Periodically ingest this into an interactive session to improve your workflows over time.

### 9. A tools/bin folder

Python or bash scripts the agent builds to make its own job easier — for example, an `agent_review` bash script that lets the agent kick off agent reviews via CLI without needing to know each agent's particular invocation. Maintain docs on how to write scripts effectively, and instruct the agent to keep building these out.

### 10. Periodic agent sweeps

Sweeps through recent commits looking for problems and gotchas visible only at a higher level, across multiple commits.

### 11. A coding conventions doc

Specific coding conventions for the codebase, heavily used by review agents (though as much of this as possible should be enforced by linters instead).

### 12. An agent loop / night shift skill

A skill laying out how the agent should approach autonomous work from an orchestration standpoint.

### 13. A task queue accessible to the agent

Could be as simple as a `TODOS.md`, or something like Linear with a CLI to fetch tasks via API.

### 14. A false-confidence test audit skill

Run periodically to find tests that aren't actually testing what you think they're testing, and fix them.

### 15. Visual regression tests

Take screenshots, compare via tooling and agent visual review, and commit them with the work (Git LFS is useful here), or at minimum push them into the PR.

### 16. Automatic performance benchmark tests

Tests that notice when performance degrades.

### 17. Performance profiling tools for agents

Tools agents can use for targeted benchmarking, trying new techniques, comparing outputs, and comparing profiles.

### 18. End-of-shift full validation

Run all tests, performance benchmarks, agent reviews, and sweeps before wrapping up — so when you return, everything is as pristine as it can be.
