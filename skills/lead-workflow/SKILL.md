---
name: lead-workflow
description: Structured way to make changes to code or Figma designs. Classifies the task, uses the cheapest model that does it well (Sonnet leads, Haiku for volume, Opus for risk and hard reasoning), and ends with a review.
---

# Lead workflow

You lead: classify, plan, do or delegate, check, review, finish. You are responsible for the result.

Explicit instructions from the user override this skill (for example "skip the review"). Questions and explanations don't need this workflow; just answer them.

## Setup

- Read one domain guide: [references/code.md](references/code.md) for code, [references/design.md](references/design.md) for Figma. It defines orient steps, examples for each level and risk, checks, and review criteria.
- When spawning subagents, set the model per call. If the tool can't set models, ignore model names. If it can't spawn subagents, do every step yourself and review as in "Self-review".

## Flow

### 1. Orient

Follow the domain guide's orient step. Adapt to what exists; never introduce a competing pattern.

### 2. Classify

Rate the task on three questions, using the domain guide's examples:

| | Low | Medium | High |
|---|---|---|---|
| **Judgment:** how many decisions does it need? | None: mechanical or fully specified | Some, within existing patterns | Significant: new patterns, real trade-offs, architecture, unclear root cause |
| **Scope:** how many distinct changes? | One change, even if repeated across many files (a rename) | Several changes in one area | Changes across areas, or work that splits into parallel parts |
| **Risk:** how bad if wrong, and would the checks catch it? | Cosmetic, or fully caught by checks | Wrong behavior in one feature that checks might miss | A high-risk area from the guide, an irreversible action, or a shared contract |

| Level | When |
|---|---|
| Trivial | All three Low |
| Medium | Anything else that isn't Large. High risk alone makes a task Medium: risk changes the review, not the size of the process |
| Large | High judgment or High scope |

Skip the task if it's already done, out of scope, or not worth doing, and say why. If requirements are ambiguous, ask before planning. If a task turns out bigger than its level, stop and reclassify.

### 3. Path

| Level | Steps |
|---|---|
| Trivial | One-line plan (don't wait) → do it yourself → checks → self-review. No subagents |
| Medium | Research only if needed → plan, wait for approval → do it yourself or with one `sonnet` subagent → checks → independent review |
| Large | Research → `opus` drafts the plan; present it and wait for approval → up to 3 parallel subagents → checks → independent review |

### 4. Research

Spawn read-only `haiku` subagents, in parallel for independent questions. Each reports under 200 words: findings, exact locations (file and line, or page and node ID), patterns to follow, open questions. Use `sonnet` when the research needs real reasoning.

### 5. Plan

List the tasks. Each has a goal, what it owns (files, or frames and components), acceptance criteria, and a model. Parallel tasks must own separate things; tasks touching the same thing run one after another.

### 6. Do

Do it yourself when you already have the context and briefing would cost more than doing. Otherwise delegate with the brief template. Name each subagent and send follow-up fixes to the same one.

### 7. Check

Run the domain guide's checks, yourself or through a `haiku` subagent that reports failures only.

### 8. Review

Look at the actual result (diff, or screenshot and node structure), never just a summary.

| Situation | Reviewer |
|---|---|
| Trivial | Self-review |
| Medium or Large, a subagent did the work | You |
| Medium or Large, you did the work | A `sonnet` subagent |
| High risk, or Large | An `opus` subagent instead of `sonnet` (in addition to you if a subagent did the work) |

If the review finds problems, send a focused fix to the same subagent or fix it yourself. A problem that survives a fix counts as a failed attempt (see "When stuck").

### 9. Finish

- Checks pass, or remaining failures are explained.
- Tell the user briefly: what changed, where, what the review found, and why Opus was used if it was.
- Commit, publish or merge only when the user asks.

## Rules

### Models

| Model | Use for |
|---|---|
| `haiku` | Research, running checks, mechanical work at volume inside Medium or Large tasks |
| `sonnet` | The lead, implementation, the default reviewer |
| `opus` | Large plans; reviews of High-risk or Large work; one attempt when stuck on reasoning; settling a disagreement between you and a Sonnet reviewer |

Not reasons for Opus: size, number of files, duration, writing tests, or the task feeling important. Whenever you use Opus, say which rule triggered it.

### When stuck

Count attempts per problem. After 2 failed attempts, diagnose before trying again:

| Diagnosis | Signs | Action |
|---|---|---|
| Missing information | Unclear or contradictory requirements, can't reproduce, needs a decision only the user can make, missing access | Ask the user a specific question. A bigger model would only guess better |
| Reasoning difficulty | The information is there, but attempts fail for different reasons or circle the same theory | One `opus` attempt, given what was tried and why it failed |

If that fails too, stop and ask the user, with a short summary and your best guess at what's missing. Never more than 3 attempts on one problem.

### Brief template

Subagents don't see this conversation, so every brief stands on its own:

```
Goal: <one sentence>
Context: <what to look at, patterns to follow, research findings>
Owns: <what this agent may change; touch nothing else>
Constraints: <from the domain guide>
Done when: <acceptance criteria>
Verify: <the domain guide's checks, run before reporting>
Report (under 150 words): what changed, check results, anything uncertain
```

### Reviewer setup

A reviewer gets the goal, the acceptance criteria, the domain guide's review criteria, and the result, not the story of how it was made. Every finding must point to a location. "No issues" is a valid answer; invented problems waste a round. Report under 100 words, real problems only, nothing the checks already cover.

### Self-review

Switch roles: re-read the result from scratch as if someone else made it, go through the domain guide's review criteria one by one, and note each finding before fixing it. Tell the user it was a self-review.

## Cost guardrails

- Ask every subagent for short reports; long reports fill your context.
- Send follow-up work to an existing named subagent rather than spawning a new one.
- If you're re-explaining a lot of context to a subagent, do the work yourself.
- Calibration mode only when the user asks for it: follow [references/calibration.md](references/calibration.md).
