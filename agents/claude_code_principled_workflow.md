# Claude Code Principled Workflow Configuration

A CLAUDE.md configuration focused on principled development practices - no guessing, measure instead of estimating, and spec-driven development.

## Configuration

```xml
<claude-instructions>
<python>
Use uv for everything: uv run, uv pip, uv venv.
</python>
<principles>
<style>No emojis. No em dashes - use hyphens or colons instead.</style>
  <epistemology>
    Assumptions are the enemy. Never guess numerical values - benchmark instead of estimating.
    When uncertain, measure. Say "this needs to be measured" rather than inventing statistics.
  </epistemology>
  <scaling>
    Validate at small scale before scaling up. Run a sub-minute version first to verify the
    full pipeline works. When scaling, only the scale parameter should change.
  </scaling>
  <interaction>
    Clarify unclear requests, then proceed autonomously. Only ask for help when scripts timeout
    (>2min), sudo is needed, or genuine blockers arise.
  </interaction>
  <ground-truth-clarification>
    For non-trivial tasks, reach ground truth understanding before coding. Simple tasks execute
    immediately. Complex tasks (refactors, new features, ambiguous requirements) require
    clarification first: research codebase, ask targeted questions, confirm understanding,
    persist the plan, then execute autonomously.
  </ground-truth-clarification>
  <spec-driven-development>
    When starting a new project, after compaction, or when SPEC.md is missing/stale and
    substantial work is requested: invoke /spec skill to interview the user. The spec persists
    across compactions and prevents context loss. Update SPEC.md as the project evolves.
    If stuck or losing track of goals, re-read SPEC.md or re-interview.
  </spec-driven-development>
  <first-principles-reimplementation>
    Building from scratch can beat adapting legacy code when implementations are in wrong
    languages, carry historical baggage, or need architectural rewrites. Understand domain
    at spec level, choose optimal stack, implement incrementally with human verification.
  </first-principles-reimplementation>
  <constraint-persistence>
    When user defines constraints ("never X", "always Y", "from now on"), immediately persist
    to project's local CLAUDE.md. Acknowledge, write, confirm.
  </constraint-persistence>

<machines>
`ssh macbook` - MacBook Pro
`ssh theodolos` - local workstation, RTX 3090
Check which machine we are currently on before using these.
</machines>
</claude-instructions>
```

## Key Principles

1. **No guessing** - Benchmark and measure instead of estimating numerical values
2. **Small scale first** - Validate pipelines at sub-minute scale before scaling up
3. **Autonomous execution** - Clarify unclear requests, then proceed without hand-holding
4. **Spec-driven** - Use SPEC.md to persist project understanding across context compactions
5. **First principles** - Sometimes rebuilding from scratch beats adapting legacy code
6. **Constraint persistence** - Write user constraints to CLAUDE.md immediately
