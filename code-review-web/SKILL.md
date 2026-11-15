---
name: code-review-web
description: Review web application pull requests and code changes for correctness, security, performance, accessibility, maintainability, and test coverage, with prioritized, actionable feedback. Use this skill whenever the user pastes a diff, links a PR, says "review this," "is this good," "check my code," or is about to merge, and when asked to self-review generated code before delivering it.
---

# Web Code Review

Review for what matters most first. Be specific, kind, and actionable. Explain why, not just what.

## Process
1. Understand intent: read the PR description/task. If unclear, state your assumption.
2. Skim structure (files changed, size). Flag PRs that do too much.
3. Review in priority order below.
4. Deliver a verdict and grouped findings.

## Priority order
1. **Correctness:** does it do what's intended? Edge cases, null/undefined, off-by-one, async/race conditions, error handling, state bugs.
2. **Security:** input validation, injection/XSS, auth and ownership checks, secrets, unsafe deserialization (see `web-security`, `auth-and-sessions`).
3. **Data integrity:** transactions, migrations safe to run, constraints, idempotency (see `database-design`).
4. **Performance:** N+1 queries, unbounded lists, unnecessary re-renders, large imports (see `web-performance`).
5. **Accessibility and UX:** semantics, keyboard, loading/error/empty states (see `accessibility`).
6. **Tests:** meaningful coverage of new behavior and bug fixes (see `testing-web`).
7. **Maintainability:** naming, duplication, function size, dead code, comments explaining *why*, consistent with codebase conventions.
8. **API/contract changes:** breaking changes, docs, types updated.

## Severity labels
- **[blocker]** must fix before merge (bug, vulnerability, data loss)
- **[major]** should fix; significant risk or debt
- **[minor]** worthwhile improvement
- **[nit]** style/preference; author may ignore
- **[question]** need clarification
- **[praise]** call out things done well

## Feedback format
```
[major] src/api/orders.ts:58
Problem: `userId` comes from the request body, so any user can create orders for others.
Why: authorization bypass (IDOR).
Suggestion: derive it from the session: `const userId = req.session.userId;`
```

## Don'ts
Don't rewrite in your own style, don't bikeshed formatting that a linter should enforce, don't block on nits, don't give vague comments ("clean this up").

## Output
1. One-paragraph summary + verdict: **Approve / Approve with comments / Request changes**.
2. Findings grouped by severity.
3. "Looks good" list, so the author knows what was verified.
