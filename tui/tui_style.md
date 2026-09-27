# TUI style prompt

Paste this below a description of what the tool should manage.

---

Build a terminal UI in the same style as my `~/bin/llm-switch`:

**Stack**
- One self-contained bash script in `~/bin/<name>` (executable, no extension), `#!/usr/bin/env bash` and `set -uo pipefail`.
- UI is `whiptail` only (menu, yesno, msgbox, gauge, textbox). No Python, no extra dependencies.
- Capture whiptail choices with `choice=$(whiptail ... 3>&1 1>&2 2>&3) || return` so Esc/Cancel always backs out cleanly.

**Structure**
- Header comment: one line saying what it does, a short paragraph on the key behaviour, then usage lines for each mode.
- Config at the top: a default item constant, and a data array of `"id|short label"` entries with small `id_of`/`label_of` helpers. Adding an item means adding one line.
- Small single-purpose functions (`state_of`, `*_summary`, `status_text`, `*_menu`, `main_menu`), with a `case "${1:-}"` dispatcher at the bottom.
- A non-interactive `--status` mode that prints the same info as plain text (exit 0) and a `-h/--help` that prints the header comment.

**Main menu**
- A `--menu` loop that re-renders every time so live state is always current.
- Each row has an id tag and an aligned label (`printf '%-44s [%s]'`) with live state in brackets, e.g. `[active, boot]` or `[not installed]`.
- `--default-item` is my preferred option on first open, then whatever I last picked.
- The menu text shows a one-line live summary of the relevant resource (e.g. GPU memory).
- Utility rows at the end: bulk action (e.g. "Stop all"), "Status", "Quit".

**Item submenu**
- Selecting an item opens a submenu. Its title is the item id, its text is the item's full description plus current status, and the actions are verbs with a short explanation of any side effects. It always ends with "Back".

**Behaviour**
- Safe to run as a normal user. Call `sudo` only for the steps that need it, through an `ensure_sudo` helper that clears the screen and runs `sudo -v` before any whiptail box comes up.
- Handle conflicts for me (stop what clashes, free resources), but always ask with `--yesno` before killing anything I started by hand, and show what it is.
- Long-running actions show a `--gauge` with a meaningful progress signal (a real metric, not fake time), elapsed seconds and a live summary. Poll the real readiness signal (log line, health endpoint), detect failure and crash loops, and time out with a hint about what to check.
- Every outcome ends in a clear box: success shows the useful endpoints/paths and final state; failure shows the relevant log in a `--scrolltext --textbox`.
- If a prerequisite is missing (e.g. an unconfigured item), offer to set it up instead of just erroring.
- `clear` on exit.

**Code style**
- Sparse comments that explain *why*, not what. `local` for everything, and quote all variables.
- Test before handing over: run `bash -n`, run the `--status` mode, and render the main menu in a pty (`script -qfc`) to check the layout fits 120×30.
