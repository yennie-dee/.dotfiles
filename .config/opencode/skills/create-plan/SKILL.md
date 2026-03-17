---
name: create-plan
description: Create structured implementation plans for Jira tickets. Plans become context for teaching walkthroughs and implementation. Use when starting work on a new ticket.
---

# Create Plan

Create a structured implementation plan that serves as progressive context for all downstream work (teaching, implementation, review).

## When to Use

- Starting work on a new Jira ticket
- Work will span multiple MRs or sessions

**If scope is unclear or this is a new feature without a ticket yet**, load `spec-planner` first. Run spec-planner → get approval → then come back here to create the implementation plan.

```
spec-planner (what are we building?) → create-plan (how do we build it?)
```

## Plan File Location

Save to `~/.config/opencode/plans/PLAN-{TICKET}-{short-description}.md`

Example: `PLAN-UI-7868-authentication-kumo.md`

## Plan Structure

Every plan should follow this structure. Sections marked (optional) can be skipped for small tasks.

```markdown
# {TICKET}: {Title}

**Jira**: [{TICKET}](https://jira.cfdata.org/browse/{TICKET})
**Branch**: `{type}/{TICKET}-{slug}`
**Status**: Planning | In Progress | Review | Done
**Assignee**: Jenny Brecheisen

## Summary
One paragraph: what are we doing and why?

## Blockers (optional)
| Blocker | Status | Notes |
|---------|--------|-------|

## Scope
What's IN scope and what's explicitly OUT of scope.

## Current State
What exists today — the starting point. Include file paths.

## Target State
What it should look like when done. Include file paths for new/changed files.

## Design Decisions
| Decision | Choice | Rationale |
|----------|--------|-----------|
Why we're doing it this way, not another way. These are the "unobvious" things
that an agent needs to know but wouldn't infer from reading code.

## MR Staging Plan (optional — for multi-MR work)
Break into reviewable chunks with clear scope per MR.

### MR 1: {Title}
**Scope**: ...
**Files**: ...
**Review size**: ~N lines

## Component/Pattern Migration (optional — for Kumo migrations)
Show the mapping from old to new.

## Files to Change
| File | Change |
|------|--------|

## Readiness Checklist

Before starting implementation, verify:

- [ ] All files identified and paths confirmed in codebase
- [ ] Design decisions documented (no open questions)
- [ ] Dependencies/imports verified (components exist, correct API)
- [ ] Pattern confirmed by reading a reference implementation
- [ ] Edge cases identified (loading, error, empty, null states)
- [ ] Accessibility requirements noted (labels, keyboard nav, ARIA)
- [ ] Test strategy defined (what to test, what existing tests to update)
- [ ] i18n: translation keys identified (new keys needed? existing keys to reuse?)
- [ ] For multi-MR work: MR boundaries are clear and each MR is independently reviewable

## Progress
- [ ] Task 1
- [ ] Task 2

## Reference
Links to related files, docs, examples, Figma designs.
```

## Progressive Context

Plans create context that flows downstream:

```
Plan (this document)
  ↓ informs
Teaching walkthrough (if in teaching mode)
  ↓ informs
Implementation (the actual code changes)
  ↓ informs
Adversarial review (before creating MR)
  ↓ informs
MR description + LEARNING.md entry
```

Each phase should reference the plan. The plan captures decisions so they don't need to be re-debated in later phases.

## Design Decisions: The "Unobvious" Filter

When writing the Design Decisions section, ask: **"Would an agent figure this out from reading the code?"**

- If yes → don't document it (it's obvious)
- If no → document it (it's a decision that needs to be explicit)

Good design decisions to document:
- Why we chose approach A over approach B
- Constraints from other teams or systems
- Things we tried that didn't work
- Patterns we're deliberately following or avoiding
- Scope boundaries and why they exist

Bad things to document:
- "Use TypeScript" (obvious from the file extension)
- "Use functional components" (obvious from the codebase)
- "Follow existing patterns" (too vague to be useful)

## Updating Plans

Plans are living documents. Update them when:
- Scope changes (mark what changed and why)
- Blockers are resolved
- Design decisions change
- MRs are completed (check off progress items)

## After Planning

Tell the user what's next based on their workflow preference:

- **Teaching mode**: "Want me to walk you through the first MR step by step? (Load the `teaching-mode` skill)"
- **Just do it mode**: "Ready to start implementing. Want me to begin with MR 1?"
- **Review needed**: "There are open questions in the plan — let's resolve those first."

**Fresh context reminder**: Suggest starting a new session for implementation. The plan file bridges sessions — the next session can read it to get full context without needing this conversation's history.
