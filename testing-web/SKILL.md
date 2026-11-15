---
name: testing-web
description: Plan and write tests for web applications: unit, component, integration, API, and end-to-end tests with Vitest/Jest, Testing Library, Playwright, and Supertest. Use this skill whenever the user asks to write tests, add coverage, set up a test framework, fix flaky tests, or asks "how should I test this," and apply it when finishing any feature that has non-trivial logic.
---

# Web Testing

Test behavior users and callers depend on, not implementation details.

## Strategy (testing trophy)
- **Static:** TypeScript + ESLint catch a large class of bugs for free.
- **Unit:** pure logic, utilities, validators, reducers. Fast and many.
- **Integration (highest value):** component + hooks + mocked network, or API route + real test DB.
- **E2E:** a few critical user journeys (signup, login, checkout). Slow, so keep it small.

## Tooling defaults
| Layer | Tool |
|---|---|
| Unit/integration (Vite/modern) | Vitest (Jest if the project already uses it) |
| Components | Testing Library (`@testing-library/react`, `/vue`, etc.) |
| Network mocking | MSW |
| API | Supertest or framework test client, against a disposable DB |
| E2E | Playwright |
| Accessibility | `@axe-core/playwright`, `jest-axe` |

## Writing good tests
- Name by behavior: `it("shows an error when the email is invalid")`.
- Arrange → Act → Assert; one reason to fail per test.
- Query the DOM like a user: `getByRole`, `getByLabelText`; avoid `data-testid` unless needed, and never test CSS classes.
- Use `userEvent` over `fireEvent`; `await` async UI with `findBy*` / `waitFor`.
- Mock at the boundary (network, time, randomness), not internal modules.
- Factories/fixtures for test data; no shared mutable state between tests.

## What to cover
Happy path, validation failures, empty/loading/error states, permission boundaries (authorized vs not), edge values (0, empty, max length, unicode), and regression tests for every fixed bug.

## Playwright essentials
```ts
test("user can sign in", async ({ page }) => {
  await page.goto("/login");
  await page.getByLabel("Email").fill("user@example.com");
  await page.getByLabel("Password").fill("correct-horse");
  await page.getByRole("button", { name: "Sign in" }).click();
  await expect(page).toHaveURL("/dashboard");
});
```
- Use auto-waiting assertions, not `waitForTimeout`.
- Reuse auth via `storageState`; run in CI with traces on failure.

## Flaky tests
Causes: timing, shared state, real network, random data, order dependence. Fix by awaiting conditions, isolating data, seeding time/random, and running tests in random order.

## CI
Run lint + typecheck + unit/integration on every PR; E2E on PR or before deploy. Report coverage as a signal, not a target (aim for meaningful coverage of critical paths).

## Output
Deliver test files next to the code they test, runnable with the project's existing runner, plus any setup/config needed. Briefly list what each test group covers and what is intentionally not covered.
