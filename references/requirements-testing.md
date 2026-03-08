# Requirements Document Testing Reference

## Objectives
- Validate that requirements are testable, complete, and unambiguous.
- Identify gaps before implementation (shift-left quality).
- Ensure every requirement maps to measurable acceptance criteria.

## Review Checklist
- **Completeness**: Core flow, alternate flow, exception flow are all present.
- **Clarity**: No vague words like "fast", "friendly", "proper" without measurable criteria.
- **Consistency**: Terminology, roles, states, and rules are consistent across sections.
- **Feasibility**: Constraints, dependencies, and non-functional requirements are realistic.
- **Traceability**: Every requirement has a unique ID and can map to test cases.

## AI Legal Assistant Specific Checklist
- **Legal disclaimers**: Scope of legal advice is clearly bounded and visible.
- **Jurisdiction rules**: Region/country applicability is explicitly defined.
- **Prompt/response governance**: Sensitive/legal-risk outputs have review/guardrail rules.
- **Data privacy**: PII collection, retention, masking, and deletion requirements are explicit.
- **Auditability**: User actions, model responses, and decision logs are traceable.
- **Fallback strategy**: Model uncertainty/escalation to human lawyer is defined.

## Acceptance Criteria Quality Rules
- Use Given/When/Then style where possible.
- Include positive, negative, boundary, and permission scenarios.
- Define measurable SLAs/SLOs for response time and availability.
- Define security acceptance criteria (authN/authZ, data protection, abuse prevention).

## Deliverables
1. Requirements review findings list (severity + owner + due date).
2. Requirement-to-test-case traceability matrix.
3. Open questions / assumptions log.
