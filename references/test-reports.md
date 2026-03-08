# Test Reports Reference

## Minimum Report Contents
1. Scope and environment.
2. Test summary (total/passed/failed/skipped/flaky).
3. Coverage snapshot and known gaps.
4. Findings with severity and impact.
5. Actionable remediation recommendations.

## Output Paths
- `test-reports/report.json`
- `test-reports/report.html`
- `test-reports/coverage/`

## CI/CD Notes
- Upload artifacts on every run.
- Enforce quality gate (e.g., coverage threshold).
- Publish concise PR comment with key failures.
