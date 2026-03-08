# Testing Anti-Patterns

## Common Issues
- Testing implementation details instead of behavior.
- Over-mocking that hides integration defects.
- Order-dependent tests and shared mutable state.
- Silent assertions (no strong expected outcomes).
- Ignored flaky tests without ownership.

## Fixes
- Assert externally observable behavior.
- Reduce unnecessary mocks in higher-level tests.
- Ensure full isolation and deterministic setup/teardown.
