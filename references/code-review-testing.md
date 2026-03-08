# Code Walkthrough & Review Testing Reference

## Objectives
- Detect defects early through structured walkthroughs.
- Validate testability, maintainability, and security posture in code.
- Ensure implementation matches requirements and test design.

## Walkthrough Process
1. Define review scope by module and requirement IDs.
2. Run static checks/lint/format before review.
3. Review by layers: API/controller -> service -> repository -> integration points.
4. Record findings with severity, evidence, and remediation suggestion.
5. Confirm fixes and regression risks.

## General Review Checklist
- Clear naming, small functions, single responsibility.
- Proper error handling and meaningful logs (without sensitive data leakage).
- No dead code, debug artifacts, or hidden side effects.
- Boundary checks and null/empty handling are explicit.
- Unit/integration tests cover changed logic and failure paths.

## Vue Frontend Focus
- Input validation and output encoding for XSS safety.
- Route guards and permission checks for protected pages.
- State management side effects are predictable.
- API error states, retries, and loading skeletons are handled.
- Component tests cover key interactions and edge cases.

## Java Backend Focus
- Validation at API boundary (`@Valid`, custom validators).
- Transaction boundaries and idempotency are correct.
- Authorization checks enforced at service/resource level.
- External calls have timeout/retry/circuit-breaker strategy.
- Sensitive fields are masked in logs and responses.

## AI Legal Assistant Risk Focus
- Prompt construction avoids injection from user-controlled content.
- Model outputs pass policy filters for legal-risk language.
- Hallucination-sensitive workflows include confidence gating/escalation.
- Evidence/citation links are validated before response rendering.

## Deliverables
1. Review checklist result.
2. Defect list with severity and code references.
3. Follow-up verification notes after fix.
