# lead-workflow

A skill that gives an AI agent one consistent way to make changes: classify the task, use the cheapest model that does the job well, and finish with a review that fits the risk.

It works for code and for design in Figma. It follows the open [Agent Skills](https://agentskills.io) format, so it runs in Claude Code, the Claude apps, and other tools that support `SKILL.md`.

## How it works

1. **Orient.** Read the project (or the Figma file and its design system) and adapt to what's there.
2. **Classify.** Rate the task on judgment (how many decisions), scope (how many distinct changes) and risk (how bad if wrong, and would checks catch it). That gives a level: Trivial, Medium or Large. File count alone doesn't decide it: a rename across 40 files is still Trivial, and a one-line auth change is never Trivial.
3. **Follow the path for that level.**
   - Trivial: do it, run checks, self-review. No subagents, no extra cost.
   - Medium: plan, wait for approval, implement, independent Sonnet review.
   - Large: research, an Opus plan, up to 3 parallel subagents, Opus review.
4. **Review by risk.** High-risk work always gets an Opus reviewer, whatever its size.

| Model | Role |
|---|---|
| Sonnet | Leads the session, implements, default reviewer |
| Haiku | Research, running checks, mechanical work at volume |
| Opus | Large plans, reviews of high-risk or Large work, hard problems Sonnet couldn't solve |

When the same problem fails twice, the lead diagnoses why before trying again. Missing information goes to the user as a question; a reasoning problem gets one Opus attempt. No problem gets more than 3 attempts.

To tune the rules on your own work, ask for calibration mode: both models review each task and the differences are logged.

Where the tool can't spawn subagents or pick models, the skill runs every step itself and does the review as a structured self-review.

## Structure

```
skills/lead-workflow/
├── SKILL.md              the method, for any kind of work
└── references/
    ├── code.md           level and risk examples, checks, review criteria for code
    ├── design.md         the same for Figma work
    └── calibration.md    only read when you ask for calibration
```

The agent loads only the guide that matches the task.

## Install

### Claude Code plugin (recommended)

lead-workflow is listed in the `shekey` marketplace, which lives in [Shekey/claude-craft](https://github.com/Shekey/claude-craft):

```
/plugin install lead-workflow --marketplace Shekey/claude-craft
```

Or in two steps:

```bash
claude plugin marketplace add Shekey/claude-craft
claude plugin install lead-workflow@shekey
```

Update later with `claude plugin marketplace update shekey`.

The same marketplace lists [craft](https://github.com/Shekey/claude-craft). Both plugins plan, route and review tasks, so enable one of them at a time.

### As a plain skill (Claude apps, Cursor, Codex, Copilot and other tools)

Copy `skills/lead-workflow` to wherever your tool loads skills from, for example `~/.claude/skills/` or a project's `.claude/skills/`. For the Claude apps, zip the folder and upload it in your skills settings.

### Try it before installing

```bash
git clone https://github.com/Shekey/lead-workflow
claude --plugin-dir ./lead-workflow
```

## Use

Start the session on Sonnet (`claude --model sonnet`) and describe the change you want. The skill triggers from its description, so you don't need to name it. To force it, run `/lead-workflow:lead-workflow` (plugin) or `/lead-workflow` (plain skill).

To set the level yourself, start the request with it: `trivial: rename userId to customerId`.

For more reliable triggering, add one line to your project instructions (CLAUDE.md or AGENTS.md):

```
For changes to code or Figma designs, follow the lead-workflow skill.
```

For design work, connect a Figma connector first and give the agent a link to the file or frame.

## Customize

Add your own rules to your project instructions rather than editing the skill: commit message style, extra checks, team conventions. The skill follows the project's conventions.

To support another kind of work, add a guide in `references/` with the same sections (Orient, Level examples, Risk examples, Constraints for briefs, Checks, Review criteria, Finish) and list it in the Setup step of `SKILL.md`.

## License

MIT
