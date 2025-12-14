# Daily AI Agent Workflow for Project Progress

Original Post by [@doodlestein](https://x.com/doodlestein) (December 13, 2025)

## Philosophy

> I like to make sure that I'm making some forward progress on every one of my active projects each day, even when I'm too busy to spend real mental bandwidth on all of them every single day.
>
> So I've come up with a few prompts that I use a lot with the agents so they're always doing some level of polishing/checking/fixing and general improvement.
>
> The models are basically good enough now, and I have enough unit tests and end-to-end integration tests, that I'm not too worried about them going rogue and making big mistakes. Plus, if one of them did, the other agents would probably catch it and fix it themselves.

## Common Prompts

### Random Code Exploration and Bug Fixing

```
I want you to sort of randomly explore the code files in this project, choosing code files to deeply investigate and understand and trace their functionality and execution flows through the related code files which they import or which they are imported by. Once you understand the purpose of the code in the larger context of the workflows, I want you to do a super careful, methodical, and critical check with "fresh eyes" to find any obvious bugs, problems, errors, issues, silly mistakes, etc. and then systematically and meticulously and intelligently correct them. Be sure to comply with ALL rules in AGENTS dot md.
```

### Review Fellow Agents' Code

```
Ok can you now turn your attention to reviewing the code written by your fellow agents and checking for any issues, bugs, errors, problems, inefficiencies, security problems, reliability issues, etc. and carefully diagnose their underlying root causes using first-principle analysis and then fix or revise them if necessary? Don't restrict yourself to the latest commits, cast a wider net and go super deep! Use ultrathink.
```

### UI/UX Scrutiny and Improvement Suggestions

*Use with high-effort models like Opus 4.5 or GPT 5.2*

```
Great, now I want you to super carefully scrutinize every aspect of the application workflow and implementation and look for things that just seem sub-optimal or even wrong/mistaken to you, things that could very obviously be improved from a user-friendliness and intuitiveness standpoint, places where our UI/UX could be improved and polished to be slicker, more visually appealing, and more premium feeling and just ultra high-quality, like Stripe-level apps.
```

## Follow-up Prompt Chain for Implementing Improvements

*Queued in Codex (note: doesn't work the same in Claude Code due to interruptions)*

### 1. Create Beads

```
OK so please take ALL of that and elaborate on it more and then create a comprehensive and granular set of beads for all this with tasks, subtasks, and dependency structure overlaid, with detailed comments so that the whole thing is totally self-contained and self-documenting (including relevant background, reasoning/justification, considerations, etc.-- anything we'd want our "future self" to know about the goals and intentions and thought process and how it serves the over-arching goals of the project.)
```

### 2. Review and Revise Beads

```
Check over each bead super carefully-- are you sure it makes sense? Is it optimal? Could we change anything to make the system work better for users? If so, revise the beads. It's a lot easier and faster to operate in "plan space" before we start implementing these things!
```

### 3. Execute Beads

```
OK, so start systematically and methodically and meticulously and diligently executing those remaining beads tasks that you created in the optimal logical order! Don't forget to mark beads as you work on them.
```

*Follow with a couple of "proceed" messages.*

### 4. Final Fresh-Eyes Review

```
Great, now I want you to carefully read over all of the new code you just wrote and other existing code you just modified with "fresh eyes" looking super carefully for any obvious bugs, errors, problems, issues, confusion, etc. Carefully fix anything you uncover.
```

## Git Commit Prompt

```
Now, based on your knowledge of the project, commit all changed files now in a series of logically connected groupings with super detailed commit messages for each and then push. Take your time to do it right. Don't edit the code at all. Don't commit obviously ephemeral files. Use ultrathink.
```

## Workflow Notes

This workflow is run multiple times daily across 7+ projects, keeping machines busy. Prompts are triggered quickly via a custom Stream Deck setup (with buttons labelled for each prompt, including icons).

## Stream Deck Button Layout

The original post includes photos of Elgato Stream Decks configured with custom buttons:

| Button | Button | Button |
|--------|--------|--------|
| New Term | Work On Your Beads | Turn Plan Into Beads |
| Add Missing Features to README | Apply UBS Tool | Analyse Beads and Allocate |
| Check and Respond to Agent Mail | Opt: BE | Check Other AGENTS' Work |
| Re-Read AGENTS.md | Ultrathink | Start Out with Agent Mail |
| Read AGENTS.md and Investigate | Introduce Yourself to Fellow Agents | INVESTIGATE AND FIX |
| DO IT! | Default: New | Next Bead |
| Scrutinise and Improve Workflow and UI | Revise README | Improve Beads |
| PROCEED | Git Commit | Create Unit & E2E Tests |
| READ & CONTINUE | Use BV | Fresh Review |
| Fix Bug | Improve README | Combine Plans Into Superior Hybrid Plan |
| Build UX | Random Inspect | |
