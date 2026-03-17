---
name: adversarial-review
description: Adversarial code review that forces genuine analysis. Use before creating MRs or after implementing significant changes. The reviewer MUST find issues — no rubber-stamping allowed.
---

# Adversarial Review

Force genuine analysis of code changes by requiring problems to be found. Inspired by the BMAD Method's adversarial review technique.

## When to Use

- Before creating an MR for any non-trivial change
- After implementing a feature, before marking it done
- When the user says "review this", "check my code", or "is this ready?"
- After a teaching-mode walkthrough, to validate the user's implementation

## Core Rule

**You must find issues. "Looks good" is not an acceptable response.**

If you genuinely cannot find issues after thorough analysis, you must explicitly explain what you checked and why nothing was found. But this should be rare — there are almost always things to catch.

## What to Look For

Examine the changes through these lenses, in order of priority:

### 1. What's Missing (Most Important)
- Edge cases not handled (null, empty, loading, error states)
- Accessibility gaps (missing labels, keyboard nav, screen reader support)
- Missing tests for new behavior
- Error handling that's absent or incomplete
- i18n — are there hardcoded strings that should be translation keys?

### 2. What Could Break
- State management issues (stale closures, race conditions, derived state in effects)
- Type safety gaps (any casts, missing null checks, incorrect generics)
- Import paths that might not resolve
- CSS/styling that might break in dark mode or narrow viewports
- Browser compatibility issues

### 3. What Doesn't Match the Codebase
- Patterns that diverge from established conventions (check nearby files)
- Naming that's inconsistent with surrounding code
- Import style that doesn't match the file (barrel vs granular)
- Component patterns that don't match the project's approach

### 4. What Could Be Better
- Unnecessary complexity (could be simpler)
- Performance concerns (unnecessary re-renders, missing memoization where it matters)
- Readability issues (unclear variable names, long functions, missing context)
- DRY violations (duplicated logic that exists elsewhere in the codebase)

## Output Format

Present findings as a numbered list with severity ratings:

```
## Review Findings

1. **HIGH** — `file.tsx:42` — No error handling on the API call. If fetchData fails, the component silently shows nothing instead of an error state.

2. **MEDIUM** — `file.tsx:15` — Using `useEffect` to derive state from props. This causes an extra render. Compute it during render instead: `const fullName = firstName + ' ' + lastName`.

3. **LOW** — `file.tsx:8` — Import order: Kumo imports should come after React imports per project convention.

## Summary
- X findings: Y high, Z medium, W low
- Recommend: SHIP / SHIP WITH FIXES / NEEDS WORK
```

### Severity Definitions

| Level | Meaning | Action |
|-------|---------|--------|
| **HIGH** | Bug, security issue, missing functionality, accessibility violation | Must fix before shipping |
| **MEDIUM** | Code quality, missed pattern, minor UX issue | Should fix, or document why not |
| **LOW** | Style, naming, minor improvement | Fix if easy, skip if time-pressured |

## Review Recommendation

End every review with one of:

- **SHIP** — No high findings, mediums are acceptable trade-offs
- **SHIP WITH FIXES** — Fix the highs, mediums are optional
- **NEEDS WORK** — Multiple highs or fundamental issues

## Before the Review

1. **Check for a plan file** at `~/.config/opencode/plans/PLAN-{TICKET}-*.md`. If one exists, read it — it has the scope, design decisions, and readiness checklist. Verify the implementation matches the plan's intent.
2. **Prefer a fresh session** — if you just finished implementing the code, the review will be biased. Suggest starting a new session for the review so you evaluate the artifact, not the intent you remember from writing it.

## How to Conduct the Review

1. **Read the diff first** — understand what changed and why
2. **Check surrounding code** — read files near the changes to understand conventions
3. **Trace data flow** — follow props, state, and events through the change
4. **Think like a user** — what happens when they interact with this?
5. **Think like an attacker** — what inputs could break this?
6. **Check the plan** — if a plan file exists, verify the change matches intent and scope. Flag anything that was in scope but wasn't implemented.

## Anti-Patterns

- Do NOT rubber-stamp with "looks good to me"
- Do NOT only find LOW-severity nitpicks (dig deeper)
- Do NOT hallucinate issues that don't exist (each finding must reference specific code)
- Do NOT repeat the same finding multiple times for different locations (group them)
- Do NOT flag pre-existing issues in code you didn't change (note them separately if critical)

## After the Review

If findings exist:
1. Present the findings to the user
2. For teaching mode: explain the "why" behind each finding so the user learns
3. After fixes are made, do a **second pass** (diminishing returns after two passes)

When the review passes (SHIP or SHIP WITH FIXES after fixes are done):
4. **Move to shipping** — suggest committing and creating the MR. In stratus, load the `commit` skill then the `pr` skill.
5. Ask if anything should be added to `~/.config/opencode/LEARNING.md`
