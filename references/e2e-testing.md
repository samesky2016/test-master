# E2E Testing Reference

## Goals
- Validate critical user journeys in a production-like environment.

## Structure
- `tests/e2e/auth/` login/registration.
- `tests/e2e/features/` feature flows.
- `tests/e2e/api/` cross-layer checks.

## Playwright Guidance
- Prefer locators over brittle selectors.
- Replace fixed sleeps with explicit waits.
- Capture screenshots/traces on failure.
- Use retries in CI only.

## Flaky Test Handling
- Tag flaky cases and open tracking issue.
- Run repeated execution (`--repeat-each`) to reproduce.
