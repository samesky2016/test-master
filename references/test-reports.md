# Test Reports

## Test Report Template

```markdown
# Test Report: {Feature Name}

**Date**: YYYY-MM-DD
**Tester**: {Name}
**Version**: {App Version}

## Summary

| Metric | Value |
|--------|-------|
| Total Tests | X |
| Passed | X |
| Failed | X |
| Skipped | X |
| Coverage | X% |

## Test Scope

- [x] Unit tests
- [x] Integration tests
- [x] E2E tests
- [ ] Performance tests
- [ ] Security tests

## Findings

### [CRITICAL] {Issue Title}
- **Location**: src/api/users.ts:45
- **Steps to Reproduce**:
  1. Send POST to /api/users without auth
  2. Request succeeds with 201
- **Expected**: 401 Unauthorized
- **Actual**: 201 Created
- **Impact**: Unauthorized user creation
- **Fix**: Add auth middleware

### [HIGH] {Issue Title}
- **Location**: src/services/orders.ts:123
- **Description**: N+1 query in order list
- **Impact**: 3s response time with 100 orders
- **Fix**: Add eager loading for order items

### [MEDIUM] {Issue Title}
- **Details**: ...

### [LOW] {Issue Title}
- **Details**: ...

## Coverage Analysis

| Module | Lines | Branches | Functions |
|--------|-------|----------|-----------|
| api/ | 85% | 78% | 90% |
| services/ | 92% | 85% | 95% |
| utils/ | 100% | 100% | 100% |

### Coverage Gaps
- `src/api/admin.ts` - 0% (no tests)
- `src/services/payment.ts:45-60` - Error handling untested

## Recommendations

1. **Immediate**: Add auth middleware to admin routes
2. **High Priority**: Optimize order queries
3. **Medium Priority**: Add tests for payment error handling
4. **Low Priority**: Increase branch coverage in api/

## Performance Results

| Endpoint | p50 | p95 | p99 |
|----------|-----|-----|-----|
| GET /users | 45ms | 120ms | 250ms |
| POST /orders | 150ms | 400ms | 800ms |

## Sign-off

- [ ] All critical issues addressed
- [ ] Coverage meets threshold (80%)
- [ ] Performance meets SLA
```

## Severity Definitions

| Severity | Criteria |
|----------|----------|
| **CRITICAL** | Security vulnerability, data loss, system crash |
| **HIGH** | Major functionality broken, severe performance |
| **MEDIUM** | Feature partially working, workaround exists |
| **LOW** | Minor issue, cosmetic, edge case |

## Quick Reference

| Section | Content |
|---------|---------|
| Summary | High-level metrics |
| Findings | Issues by severity |
| Coverage | Code coverage analysis |
| Recommendations | Prioritized actions |
| Sign-off | Approval criteria |

## Automatic Report Generation

### Jest/Playwright Report Generation

```json
// package.json
{
  "scripts": {
    "test:report": "jest --coverage --coverageReporters=html --coverageReporters=json-summary",
    "test:e2e:report": "playwright test --reporter=html",
    "report:merge": "node scripts/merge-reports.js"
  }
}
```

```javascript
// scripts/merge-reports.js
const fs = require('fs');
const path = require('path');

function generateTestReport() {
  const unitCoverage = JSON.parse(fs.readFileSync('coverage/coverage-summary.json'));
  const e2eResults = JSON.parse(fs.readFileSync('test-results/results.json'));
  
  const report = {
    timestamp: new Date().toISOString(),
    summary: {
      unitTests: {
        total: e2eResults.suites?.reduce((acc, s) => acc + s.specs.length, 0) || 0,
        passed: e2eResults.suites?.reduce((acc, s) => 
          acc + s.specs.filter(spec => spec.ok).length, 0) || 0,
        coverage: unitCoverage.total.lines.pct
      },
      e2eTests: {
        total: e2eResults.stats?.tests || 0,
        passed: e2eResults.stats?.passed || 0,
        failed: e2eResults.stats?.failed || 0
      }
    },
    coverage: {
      lines: unitCoverage.total.lines.pct,
      statements: unitCoverage.total.statements.pct,
      branches: unitCoverage.total.branches.pct,
      functions: unitCoverage.total.functions.pct
    }
  };
  
  fs.writeFileSync('test-reports/report.json', JSON.stringify(report, null, 2));
  return report;
}

generateTestReport();
```

### JUnit 5 Report Generation (Maven/Gradle)

```xml
<!-- pom.xml -->
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-surefire-plugin</artifactId>
  <version>3.1.2</version>
  <configuration>
    <reportFormat>xml</reportFormat>
    <reportsDirectory>${project.build.directory}/test-reports</reportsDirectory>
  </configuration>
</plugin>
<plugin>
  <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
  <version>0.8.10</version>
  <executions>
    <execution>
      <goals>
        <goal>prepare-agent</goal>
        <goal>report</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

```groovy
// build.gradle
plugins {
  id 'java'
  id 'jacoco'
}

test {
  useJUnitPlatform()
  reports {
    html.required = true
    junitXml.required = true
    html.outputLocation = file("$buildDir/test-reports/html")
    junitXml.outputLocation = file("$buildDir/test-reports/xml")
  }
  finalizedBy jacocoTestReport
}

jacocoTestReport {
  reports {
    xml.required = true
    html.required = true
    html.outputLocation = file("$buildDir/test-reports/coverage")
  }
}
```

### pytest Report Generation

```bash
# Generate HTML report with coverage
pytest --html=test-reports/report.html --self-contained-html \
       --cov=src --cov-report=html --cov-report=xml \
       --junitxml=test-reports/junit.xml
```

```python
# pytest.ini
[pytest]
addopts = 
    --html=test-reports/report.html
    --self-contained-html
    --cov=src
    --cov-report=html:test-reports/coverage
    --cov-report=xml:test-reports/coverage.xml
    --junitxml=test-reports/junit.xml
testpaths = tests
```

## CI/CD Integration

### GitHub Actions Complete Pipeline

```yaml
# .github/workflows/test-pipeline.yml
name: Test Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run unit tests with coverage
        run: npm run test:report
      
      - name: Upload coverage report
        uses: actions/upload-artifact@v4
        with:
          name: unit-coverage
          path: coverage/
      
      - name: Upload to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          fail_ci_if_error: true

  e2e-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Install Playwright browsers
        run: npx playwright install --with-deps
      
      - name: Run E2E tests
        run: npx playwright test --shard=${{ matrix.shard }}/${{ strategy.job-total }}
        env:
          CI: true
      
      - name: Upload Playwright report
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: playwright-report-${{ matrix.shard }}
          path: playwright-report/
          retention-days: 30

  performance-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run k6 load test
        uses: grafana/k6-action@v0.3.0
        with:
          filename: tests/performance/load-test.js
        env:
          K6_CLOUD_TOKEN: ${{ secrets.K6_CLOUD_TOKEN }}
      
      - name: Upload performance report
        uses: actions/upload-artifact@v4
        with:
          name: performance-report
          path: summary.json

  generate-report:
    needs: [unit-tests, e2e-tests, performance-tests]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Download all artifacts
        uses: actions/download-artifact@v4
        with:
          path: test-reports
      
      - name: Generate consolidated report
        run: node scripts/merge-reports.js
      
      - name: Upload final report
        uses: actions/upload-artifact@v4
        with:
          name: final-test-report
          path: test-reports/
      
      - name: Comment PR with results
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const report = JSON.parse(fs.readFileSync('test-reports/report.json'));
            const body = `## 📊 Test Results
            
            | Type | Passed | Failed | Coverage |
            |------|--------|--------|----------|
            | Unit | ${report.summary.unitTests.passed} | - | ${report.coverage.lines}% |
            | E2E | ${report.summary.e2eTests.passed} | ${report.summary.e2eTests.failed} | - |
            
            [View Full Report](./actions/runs/${{ github.run_id }})`;
            
            github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: body
            });

  quality-gate:
    needs: [unit-tests, e2e-tests]
    runs-on: ubuntu-latest
    steps:
      - name: Check quality gates
        run: |
          # Fail if coverage below threshold
          COVERAGE=$(cat coverage/coverage-summary.json | jq '.total.lines.pct')
          if (( $(echo "$COVERAGE < 80" | bc -l) )); then
            echo "❌ Coverage $COVERAGE% is below 80% threshold"
            exit 1
          fi
          echo "✅ Coverage $COVERAGE% meets threshold"
```

### GitLab CI Pipeline

```yaml
# .gitlab-ci.yml
stages:
  - test
  - report
  - quality-gate

variables:
  NODE_VERSION: "20"

unit-tests:
  stage: test
  image: node:${NODE_VERSION}
  script:
    - npm ci
    - npm run test:report
  artifacts:
    paths:
      - coverage/
      - test-reports/
    expire_in: 1 week
  coverage: '/Lines\s*:\s*(\d+.\d+)%/'

e2e-tests:
  stage: test
  image: mcr.microsoft.com/playwright:v1.40.0
  script:
    - npm ci
    - npx playwright test
  artifacts:
    paths:
      - playwright-report/
    expire_in: 1 week

performance-tests:
  stage: test
  image: grafana/k6:latest
  script:
    - k6 run tests/performance/load-test.js --out json=test-reports/performance.json
  artifacts:
    paths:
      - test-reports/performance.json
    expire_in: 1 week

generate-report:
  stage: report
  image: node:${NODE_VERSION}
  needs:
    - unit-tests
    - e2e-tests
    - performance-tests
  script:
    - node scripts/merge-reports.js
    - node scripts/generate-html-report.js
  artifacts:
    paths:
      - test-reports/
    expire_in: 30 days

quality-gate:
  stage: quality-gate
  image: node:${NODE_VERSION}
  needs:
    - unit-tests
  script:
    - |
      COVERAGE=$(cat coverage/coverage-summary.json | node -e "console.log(JSON.parse(require('fs').readFileSync(0)).total.lines.pct)")
      if [ $(echo "$COVERAGE < 80" | bc) -eq 1 ]; then
        echo "Coverage $COVERAGE% below threshold"
        exit 1
      fi
      echo "Quality gate passed"
```

### Jenkins Pipeline

```groovy
// Jenkinsfile
pipeline {
  agent any
  
  tools {
    nodejs 'NodeJS-20'
  }
  
  stages {
    stage('Install') {
      steps {
        sh 'npm ci'
      }
    }
    
    stage('Unit Tests') {
      steps {
        sh 'npm run test:report'
      }
      post {
        always {
          junit 'test-reports/junit.xml'
          publishHTML target: [
            allowMissing: false,
            alwaysLinkToLastBuild: true,
            keepAll: true,
            reportDir: 'coverage/lcov-report',
            reportFiles: 'index.html',
            reportName: 'Coverage Report'
          ]
        }
      }
    }
    
    stage('E2E Tests') {
      steps {
        sh 'npx playwright test'
      }
      post {
        always {
          publishHTML target: [
            allowMissing: false,
            alwaysLinkToLastBuild: true,
            keepAll: true,
            reportDir: 'playwright-report',
            reportFiles: 'index.html',
            reportName: 'E2E Report'
          ]
        }
      }
    }
    
    stage('Performance Tests') {
      steps {
        sh 'k6 run tests/performance/load-test.js --out json=test-reports/performance.json'
      }
    }
    
    stage('Quality Gate') {
      steps {
        script {
          def coverage = sh(
            script: "cat coverage/coverage-summary.json | node -e \"console.log(JSON.parse(require('fs').readFileSync(0)).total.lines.pct)\"",
            returnStdout: true
          ).trim().toDouble()
          
          if (coverage < 80) {
            error "Coverage ${coverage}% is below 80% threshold"
          }
          echo "✅ Quality gate passed with ${coverage}% coverage"
        }
      }
    }
  }
  
  post {
    always {
      archiveArtifacts artifacts: 'test-reports/**/*', allowEmptyArchive: true
      cleanWs()
    }
  }
}
```

## Report Output Directory Structure

```
project-root/
├── test-reports/
│   ├── report.json              # Consolidated JSON report
│   ├── report.html              # HTML summary report
│   ├── junit.xml                # JUnit format (CI compatible)
│   ├── coverage/
│   │   ├── index.html           # Coverage HTML report
│   │   ├── coverage.xml         # Coverage XML (SonarQube)
│   │   └── lcov.info            # LCOV format
│   ├── e2e/
│   │   ├── index.html           # Playwright HTML report
│   │   └── results.json         # E2E results JSON
│   ├── performance/
│   │   ├── load-test.json       # k6 JSON output
│   │   └── summary.html         # Performance summary
│   └── security/
│       └── scan-results.json    # Security scan results
├── coverage/                    # Unit test coverage (generated)
├── playwright-report/           # E2E report (generated)
└── k6-results/                  # Performance results (generated)
```
