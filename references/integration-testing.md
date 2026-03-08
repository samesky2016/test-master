# Integration Testing Reference

## Goals
- Verify interactions between modules/services.
- Catch contract mismatches and data-flow issues.

## Scope Suggestions
- API + DB integration.
- Service-to-service calls (with test doubles where needed).
- Persistence and transaction boundaries.

## Checklist
- Use realistic test data factories.
- Isolate test environment (dedicated DB/schema).
- Reset state between tests.
- Validate response status, schema, and side effects.
