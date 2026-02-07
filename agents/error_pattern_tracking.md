## Error Pattern Tracking

When you discover and fix a bug, defect, or incorrect pattern in this codebase, you **must** log it to `ERRORS.md` in the project root before considering the fix complete. If `ERRORS.md` does not exist, create it.

### When to log

- A compilation error caused by incorrect code (not a typo in a brand-new line you just wrote)
- A runtime bug or logic error found during testing
- A pattern that was applied incorrectly or inconsistently with project conventions
- A dependency or configuration issue that required a non-obvious fix
- A regression introduced by a previous change

Do **not** log trivial typos caught immediately on the same line, or errors in throwaway/scratch code.

### Error record format

Append each entry to `ERRORS.md` using this exact format:

```markdown
### [SHORT_TITLE] — [DATE as YYYY-MM-DD]

- **Severity:** Critical | High | Medium | Low
- **Category:** [Build | Logic | Type System | Concurrency | Memory | Configuration | API Misuse | Convention | Other]
- **File(s):** `path/to/file.cpp`, `path/to/other.py`
- **Pattern:** A concise description of the *general* error pattern, not just what happened in this specific instance. Write it so it can be matched against future code. e.g. "Passing a moved-from `std::unique_ptr` to a second consumer" rather than "fixed robot_arm.cpp line 42".
- **Root cause:** One or two sentences on *why* the error occurred.
- **Fix applied:** One or two sentences on what was changed.
- **Prevention rule:** A concrete, actionable rule to avoid this class of error in future. e.g. "Always use `std::move` exactly once per unique_ptr and grep for double-move before committing."
```

### Severity guide

| Severity | Meaning |
|----------|---------|
| Critical | Causes crash, data loss, or security vulnerability |
| High     | Incorrect behaviour that would reach production unnoticed |
| Medium   | Compilation failure or test failure with a non-obvious fix |
| Low      | Style/convention violation or minor inefficiency |

### Using the error log

Before writing or modifying code, review `ERRORS.md` for any **Prevention rules** that are relevant to the files or patterns you are about to touch. Specifically:

1. When editing a file listed in a prior error record, re-read that record.
2. When applying a pattern that matches a logged **Pattern**, follow the **Prevention rule**.
3. If you are unsure whether a logged pattern applies, err on the side of caution and follow the prevention rule.

### Periodic summary

When `ERRORS.md` exceeds 20 entries, add or update a `## Summary` section at the top of the file that groups recurring categories and highlights the most common root causes. This helps identify systemic issues in the codebase.
