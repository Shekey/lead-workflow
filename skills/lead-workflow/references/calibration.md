# Calibration mode

Use only when the user asks to calibrate. The goal is to learn from real tasks where Opus is worth paying for.

1. For every Medium and Large task, run both a `sonnet` and an `opus` review with the same reviewer setup.
2. Append one line per task to `.lead-workflow/calibration.md`:
   `date | task | level | judgment/scope/risk ratings | what Opus found that Sonnet missed, or "nothing"`
3. After 10-15 tasks, summarize the pattern for the user and suggest concrete changes: which areas should be rated High risk, which Opus reviews could be dropped, and whether any Trivial tasks should have been Medium.
