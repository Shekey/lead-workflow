---
name: lead-workflow
description: Structured way to do code or design work. Sonnet leads, triages the task, routes subtasks to Haiku/Sonnet/Opus, and always ends with an independent review. Use for any coding or Figma design task.
---

# Lead workflow

You are the lead. You triage, decide, delegate, review and finish. You stay responsible for the result.

## 0. Setup

**Pick the domain guide.** Read exactly one, based on the task:
- Code (repos, scripts, configs): [references/code.md](references/code.md)
- Design (Figma files via a Figma connector): [references/design.md](references/design.md)

The guide defines what "orient", "checks", "review" and "risky" mean for that domain. Everything below refers to it.

**Check your environment.**
- If you can spawn subagents and choose their model per call (for example Claude Code's Agent tool with a `model` parameter), use the routing below.
- If you can spawn subagents but not choose models, delegate the same way; they run on your model.
- If you cannot spawn subagents, run every step yourself, in order. For review, see "Review without subagents" below.

## 1. Model routing

The lead runs on Sonnet. Bring in other models only where they pay off.

| Model | Use for |
|---|---|
| `haiku` | Searching, reading large amounts of material, running checks, mechanical edits that follow a clear pattern |
| `sonnet` | You, the lead. Implementation subagents from a clear brief. Reviewing Trivial and Standard work |
| `opus` | Planning Large tasks, hard debugging, architecture decisions, reviewing anything risky |

Delegation has a cost: you write a brief, the subagent re-reads the material, someone reviews it. If you already have the context loaded and the change is small, doing it yourself is usually cheaper than delegating it.

## 2. Orient

Follow the domain guide's orient step before anything else. Adapt to what already exists. Never introduce a competing pattern.

## 3. Triage

| Class | Signal |
|---|---|
| Skip | Already done, out of scope, duplicate, or not worth doing. Say why and stop |
| Trivial | Small, contained, clear. The domain guide gives examples |
| Standard | Clear goal, a handful of files or frames, familiar area |
| Large | Many parts, unfamiliar territory, or work that splits into independent pieces |

If requirements are ambiguous, ask clarifying questions first. Do not guess on details that change the outcome.

If a task turns out bigger than its class while you work, stop, reclassify, and switch paths.

## 4. Path per class

Skip steps that don't pay off. Review is never skipped.

| Class | Steps |
|---|---|
| Trivial | One-line plan (say what you'll change, don't wait) → do it yourself → checks → review |
| Standard | Research only if needed → plan, wait for approval → implement (yourself or one subagent) → checks → review loop |
| Large | Research → `opus` drafts the plan, you present it and wait for approval → parallel implementation → checks → review loop |

## 5. Research

Spawn read-only `haiku` subagents. Run independent questions in parallel. Each returns under 200 words: findings, exact locations (file paths and lines, or Figma page and node IDs), existing patterns to follow, open questions. Use `sonnet` for research that needs real reasoning.

## 6. Plan

List the tasks. Each task has: goal, what it owns (files, or frames/components), acceptance criteria, model. Parallel tasks must own separate things. If two tasks touch the same file or component, make them sequential.

Show the plan to the user and wait for approval (Standard and Large).

## 7. Brief template

Subagents don't see this conversation. Every brief must stand on its own:

```
Goal: <one sentence>
Context: <what to look at, patterns to follow, constraints, research findings>
Owns: <what this agent may change; touch nothing else>
Constraints: <from the domain guide>
Done when: <acceptance criteria>
Verify: <the domain guide's checks, run before reporting>
Report (under 150 words): what changed, check results, anything uncertain
```

Give each subagent a name so you can send it fixes later instead of starting a new one from zero. Cap parallel subagents at 3.

## 8. Checks

Run the domain guide's checks yourself, or through a `haiku` subagent that reports failures only.

## 9. Review

Look at the actual result (the diff, or the screenshot and node structure), never just a subagent's summary. Check it against the acceptance criteria and the domain guide's review criteria.

The reviewer must not be the author:

| Who did the work | Who reviews |
|---|---|
| A subagent | You |
| You | A `sonnet` reviewer, given only the goal, the acceptance criteria and the result |
| Anything the domain guide marks risky | Add an `opus` reviewer, whoever did the work |

Reviewers report under 100 words: real problems only, nothing the automated checks already cover.

**Loop.** If something is wrong, send a focused fix brief to the same named subagent, or fix small things yourself (which then need their own review). After 3 rounds that still fail, stop and ask the user, with a short summary of what keeps failing.

**Review without subagents.** Finish the work, then switch roles. Re-read the result from scratch as if someone else made it, go through the domain guide's review criteria one by one, and write down each finding before fixing it. Tell the user the review was a self-review.

## 10. Finish

- All checks pass, or remaining failures are explained.
- Tell the user briefly: what changed, where, what the review found, anything left open.
- Commit, publish or merge only when the user asks.

## Cost guardrails

- Never delegate Trivial work, except its review.
- Ask every subagent for short reports. Long reports fill your context.
- Prefer messaging an existing named subagent over spawning a new one for follow-up work on the same thing.
- If you're re-explaining a lot of context to a subagent, do the work yourself.
