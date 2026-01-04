# Claude Code Power User Setup

Based on Boris Cherny's X Thread (Creator of Claude Code at Anthropic)

## Philosophy

There's no one right way to use Claude Code. This guide shares one approach that emphasizes parallel sessions, verification mechanisms, and team knowledge sharing.

## Session Management

### Parallel Terminal Sessions

Run 5 Claude instances in parallel tabs (numbered 1-5) with system notifications for input alerts. This allows concurrent work on multiple aspects of a project or across different projects.

Reference: [Terminal Config Docs](https://docs.anthropic.com/en/docs/claude-code)

### Web and Mobile Integration

- Operate 5-10 additional sessions on claude.ai/code
- Hand off terminal sessions to web using `&`
- Start sessions from the Claude iOS app for later check-ins
- Useful for monitoring long-running tasks while mobile

## Model Selection

Use Opus 4.5 with thinking enabled. Despite being larger than Sonnet, its superior performance in coding and tool use makes it faster overall for complex tasks.

## Documentation and Knowledge Sharing

### Shared Team Docs

Maintain a single shared docs file for your repo, checked into Git:

```
.claude/
├── settings.json      # Shared permissions
├── commands/          # Custom slash commands
└── docs.md           # Team documentation
```

Update this file frequently to correct Claude's errors and encode project-specific knowledge.

### Code Review Integration

Tag `@.claude` in PRs to update docs using the Claude Code GitHub action. This compounds engineering knowledge over time.

## Workflow Patterns

### Plan Mode for PRs

1. Start in Plan mode (`Shift+Tab` twice)
2. Refine plans iteratively with Claude
3. Switch to auto-accept edits mode
4. Execute one-shot implementation

### Custom Slash Commands

Store repetitive tasks in `.claude/commands/`. Example:

```bash
# .claude/commands/commit-push-pr.md
Commit all changes with a descriptive message, push to origin, and create a PR with a summary of the changes.
```

Use inline bash for efficiency in command definitions.

## Subagents

Regularly use subagents for specialized tasks:

- **code-simplifier**: Post-editing simplification
- **verify-app**: End-to-end testing

## Hooks

### Code Formatting Hook

Implement a PostToolUse hook to ensure code formatting, preventing CI errors:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "command": "prettier --write $FILE"
      }
    ]
  }
}
```

### Stop Hooks for Long Tasks

Use agent Stop hooks for extended tasks to trigger verification or notification.

## Permissions Management

Avoid `--dangerously-skip-permissions`. Instead:

1. Pre-allow safe commands via `/permissions`
2. Share allowlist in `.claude/settings.json`
3. Apply permission modes in sandboxes for unattended operation

Example settings.json:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run build)",
      "Bash(git:*)"
    ]
  }
}
```

## Tool Integration

Claude can autonomously use external tools via MCP servers:

- Slack (for team communication)
- BigQuery (for data queries)
- Sentry (for error monitoring)

Share MCP configs in `.mcp.json` for team consistency.

## Long-Running Tasks

For extended tasks:

1. Prompt verification with background agents
2. Use agent Stop hooks
3. Apply permission modes in sandboxes to avoid interruptions
4. Consider plugins like ralph-wiggum for monitoring

## Verification Mechanisms

**This is crucial for quality.** Providing Claude with verification mechanisms can 2-3x result quality.

Verification approaches by domain:

| Domain | Verification Method |
|--------|---------------------|
| CLI tools | Bash commands, test suites |
| Web apps | Chrome extension for UI testing |
| Mobile | Simulators |
| APIs | Integration tests, curl commands |
| Data | Query validation, checksums |

Always give Claude a way to verify its own work. Tailor verification to your specific domain.

## Quick Reference

| Technique | Purpose |
|-----------|---------|
| 5 parallel terminal tabs | Concurrent task execution |
| Opus 4.5 + thinking | Best coding performance |
| Shift+Tab twice | Enter Plan mode |
| `.claude/commands/` | Custom slash commands |
| PostToolUse hooks | Auto-formatting |
| `.claude/settings.json` | Shared permissions |
| `.mcp.json` | Tool integrations |
| Subagents | Specialized tasks |
| Verification mechanisms | 2-3x quality improvement |
