# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 5. Code Language

**Code is written in English, whatever language we talk in.**

- All variables in code must be named in English.
- All comments in code must be in English.

## 6. Long-running Tasks

**Report progress every 10 minutes.**

When the user gives any long-running task estimated to take 15 minutes or more (a crawl, a download, a build, a
migration…), start monitoring together with it: a recurring check every 10 minutes that tells the user the
progress – phase, how much is done, how much remains and the estimated end. The estimate of the remaining time
should rather be pessimistic than optimistic (round up, allow for slower phases and retries). When the task ends, check its result
(errors, warnings, totals in the log), summarise it and stop the recurring check.

How: right after launching the task, schedule a recurring job (CronCreate, an off-minute such as
`3-59/10 * * * *`) whose prompt runs a small status script (e.g. parsing the task's log) and reports in the user's
language; delete the job when the task is finished. Mention that the schedule lives only in the current Claude
session.

## 7. Code Review

**One finding at a time.**

When we do a code review, don't present all findings at once. Present one finding, discuss it with the user, and
either resolve it (fix it) or reject it – only then move on to the next one. Order them most severe first and say
how many remain (e.g. "2 of 5").

## 8. Version Control

**Never commit or push on your own initiative.**

In any version control system (Git, Mercurial, SVN, …), always ask the user for confirmation before every action
that records changes or sends them elsewhere – a commit, a push, a check-in, a submit – and wait for a clear yes. An
approval applies only to that one action – it does not carry over to later ones.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.
