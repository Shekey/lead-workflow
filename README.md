# lead-workflow

A skill that gives an AI agent one consistent way to do work: triage the task, use the cheapest model that does the job well, and always finish with an independent review.

It works for code and for design in Figma. It follows the open [Agent Skills](https://agentskills.io) format, so it runs in Claude Code, the Claude apps, and other tools that support `SKILL.md`.

## How it works

1. **Orient.** Read the project (or the Figma file and its design system) and adapt to what's there.
2. **Triage.** Sort the task into Skip, Trivial, Standard or Large.
3. **Follow the path for that class.** Trivial work skips research and planning. Large work gets research, an Opus plan, and parallel subagents.
4. **Check and review.** Automated checks first, then a review by someone who didn't do the work. Risky changes always get an Opus reviewer.

| Model | Role |
|---|---|
| Sonnet | Leads the session, implements, reviews routine work |
| Haiku | Searching, reading, running checks, mechanical edits |
| Opus | Only when escalation rules fire: risky areas, irreversible actions, real trade-offs, problems Sonnet couldn't solve |

When the same problem fails twice, the lead diagnoses why before trying again. Missing information goes to the user as a question; a reasoning problem gets one Opus attempt. No problem gets more than 3 attempts.

To tune the escalation rules on your own work, ask for calibration mode: both models review each task and the differences are logged.

Where the tool can't spawn subagents or pick models, the skill runs every step itself and does the review as a structured self-review.

## Structure

```
skills/lead-workflow/
├── SKILL.md              the method, for any kind of work
└── references/
    ├── code.md           checks, review criteria and risky areas for code
    └── design.md         the same for Figma work
```

The agent loads only the guide that matches the task.

## Install

Claude Code, for one project:

```bash
mkdir -p .claude/skills
cp -r skills/lead-workflow .claude/skills/
```

Claude Code, for all your projects: copy `skills/lead-workflow` into `~/.claude/skills/`.

Claude apps: zip the `skills/lead-workflow` folder and upload it under your skills settings.

Other tools: copy the folder to wherever the tool loads skills from.

## Use

Start the session on Sonnet, then either invoke it directly (`/lead-workflow` in Claude Code) or add one line to your project instructions (CLAUDE.md or AGENTS.md):

```
For any coding or design task, follow the lead-workflow skill.
```

For design work, connect a Figma connector first and give the agent a link to the file or frame.

## Customize

Add your own rules to your project instructions rather than editing the skill: commit message style, extra checks, team conventions. The skill follows the project's conventions.

To support another kind of work, add a guide in `references/` with the same sections (Orient, Trivial examples, Constraints for briefs, Checks, Review criteria, Risky, Finish) and list it in the Setup step of `SKILL.md`.

## License

MIT
