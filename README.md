# Test Master Skill - 优化更新说明

> 版本: 0.1.0 | 更新日期: 2026-03-07

---

## 📋 优化概览

本次优化解决了四个核心问题：

| 问题 | 解决方案 | 状态 |
|------|----------|------|
| Java代码单元测试支持不足 | 添加 JUnit 5 + Mockito 完整示例 | ✅ 已完成 |
| 测试报告自动生成机制缺失 | 添加多平台CI/CD集成方案 | ✅ 已完成 |
| 输出目录结构未定义 | 添加标准化目录约定 | ✅ 已完成 |
| E2E测试模式不够完善 | 添加完整Playwright模式 | ✅ 已完成 |

---

## 1️⃣ Java/JUnit 5 测试支持

### 修改文件
`references/unit-testing.md`

### 新增内容

#### JUnit 5 测试模式
```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository mockRepo;
    
    @InjectMocks
    private UserService service;
    
    @Test
    void returnsUser_whenFound() {
        User user = new User("1", "Test");
        when(mockRepo.findById("1")).thenReturn(Optional.of(user));
        
        User result = service.getUser("1");
        
        assertEquals(user, result);
        verify(mockRepo).findById("1");
    }
}
```

#### Mockito 模式速查

| 模式 | 用法 |
|------|------|
| 返回值Mock | `when(mock.method()).thenReturn(value)` |
| Void方法Mock | `doNothing().when(mock).method()` |
| 多次调用 | `.thenReturn(1).thenReturn(2).thenReturn(3)` |
| 验证调用 | `verify(mock).method()` |
| 参数匹配 | `anyString()`, `anyInt()`, `argThat()` |
| Spy对象 | `spy(realObject)` |

#### JUnit 5 断言速查

| 断言 | 用途 |
|------|------|
| `assertEquals(expected, actual)` | 相等断言 |
| `assertThrows(Exception.class, () -> {})` | 异常断言 |
| `assertAll("group", () -> {}, () -> {})` | 分组断言 |
| `assertTimeout(Duration, () -> {})` | 超时断言 |

#### 参数化测试
```java
@ParameterizedTest
@CsvSource({"1, Alice", "2, Bob", "3, Charlie"})
void createsUsersWithNames(String id, String name) {
    User user = new User(id, name);
    assertEquals(name, user.getName());
}
```

---

## 2️⃣ 自动报告生成 & CI/CD 集成

### 修改文件
`references/test-reports.md`

### 新增内容

#### 报告生成配置

**JavaScript/TypeScript (Jest)**
```json
{
  "scripts": {
    "test:report": "jest --coverage --coverageReporters=html --coverageReporters=json-summary"
  }
}
```

**Java (Maven)**
```xml
<plugin>
  <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
</plugin>
```

**Java (Gradle)**
```groovy
test {
  useJUnitPlatform()
  finalizedBy jacocoTestReport
}
```

**Python (pytest)**
```bash
pytest --html=test-reports/report.html --cov=src --cov-report=html
```

#### CI/CD 平台支持

| 平台 | 配置文件 | 功能 |
|------|----------|------|
| GitHub Actions | `.github/workflows/test-pipeline.yml` | 单元测试、E2E、性能测试、报告合并、PR评论、质量门禁 |
| GitLab CI | `.gitlab-ci.yml` | 多阶段测试、报告artifacts、覆盖率提取 |
| Jenkins | `Jenkinsfile` | 流水线定义、HTML报告发布、质量门禁 |

#### GitHub Actions 核心功能

```yaml
jobs:
  unit-tests:        # 单元测试 + 覆盖率
  e2e-tests:         # E2E测试 (支持分片)
  performance-tests: # 性能测试 (k6)
  generate-report:   # 合并报告 + PR评论
  quality-gate:      # 质量门禁 (覆盖率阈值)
```

#### 报告输出结构
```
test-reports/
├── report.json          # 合并报告
├── report.html          # HTML摘要
├── junit.xml            # JUnit格式
├── coverage/            # 覆盖率报告
├── e2e/                 # E2E报告
├── performance/         # 性能报告
└── security/            # 安全扫描报告
```

---

## 3️⃣ 输出目录结构约定

### 修改文件
`SKILL.md`

### 标准化目录结构

#### JavaScript/TypeScript 项目
```
project-root/
├── src/
│   └── __tests__/          # 单元测试 (就近放置)
│       ├── unit/
│       └── integration/
├── tests/
│   ├── e2e/                # E2E测试
│   ├── performance/        # 性能测试
│   └── security/           # 安全测试
├── test-reports/           # 测试报告
├── coverage/               # 覆盖率输出
└── playwright-report/      # E2E报告
```

#### Java 项目 (Maven)
```
project-root/
├── src/
│   ├── main/java/
│   └── test/java/          # 所有测试
│       ├── unit/
│       ├── integration/
│       └── e2e/
├── target/
│   ├── test-reports/       # 测试报告
│   └── site/jacoco/        # 覆盖率报告
└── scripts/performance/    # 性能测试脚本
```

#### Java 项目 (Gradle)
```
project-root/
├── src/
│   ├── main/java/
│   └── test/java/          # 所有测试
├── build/
│   └── test-reports/       # 测试报告
└── scripts/performance/    # 性能测试脚本
```

#### Python 项目
```
project-root/
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── e2e/
│   └── conftest.py
├── test-reports/
└── scripts/performance/
```

### 文件位置速查表

| 文件类型 | 位置 |
|----------|------|
| 单元测试 | `src/__tests__/` 或 `src/test/java/` |
| 集成测试 | `tests/integration/` 或 `src/test/java/integration/` |
| E2E测试 | `tests/e2e/` 或 `e2e/` |
| 性能测试 | `tests/performance/` |
| 安全测试 | `tests/security/` |
| 测试报告 | `test-reports/` |
| 覆盖率报告 | `coverage/` 或 `test-reports/coverage/` |
| CI/CD配置 | `.github/workflows/` 或项目根目录 |
| 测试脚本 | `scripts/tests/` |

---

## 4️⃣ E2E测试模式完善

### 修改文件
`references/e2e-testing.md`

### 新增内容

#### 测试文件组织
```
tests/
├── e2e/
│   ├── auth/           # 认证相关测试
│   ├── features/       # 功能测试
│   └── api/            # API测试
├── fixtures/           # 测试固件
└── playwright.config.ts
```

#### Page Object Model (POM)
```typescript
export class ItemsPage {
  readonly page: Page
  readonly searchInput: Locator
  readonly itemCards: Locator

  constructor(page: Page) {
    this.page = page
    this.searchInput = page.locator('[data-testid="search-input"]')
    this.itemCards = page.locator('[data-testid="item-card"]')
  }

  async goto() {
    await this.page.goto('/items')
    await this.page.waitForLoadState('networkidle')
  }

  async search(query: string) {
    await this.searchInput.fill(query)
    await this.page.waitForResponse(resp => resp.url().includes('/api/search'))
  }
}
```

#### Playwright配置优化
```typescript
export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  retries: process.env.CI ? 2 : 0,
  reporter: [
    ['html', { outputFolder: 'playwright-report' }],
    ['junit', { outputFile: 'playwright-results.xml' }],
    ['json', { outputFile: 'playwright-results.json' }]
  ],
  use: {
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'firefox', use: { ...devices['Desktop Firefox'] } },
    { name: 'webkit', use: { ...devices['Desktop Safari'] } },
    { name: 'mobile-chrome', use: { ...devices['Pixel 5'] } },
  ],
})
```

#### Flaky测试处理

| 问题 | 错误做法 | 正确做法 |
|------|----------|----------|
| 竞态条件 | `await page.click()` | `await page.locator().click()` |
| 网络时序 | `await page.waitForTimeout(5000)` | `await page.waitForResponse()` |
| 动画时序 | 直接点击 | `waitFor({ state: 'visible' })` |

**隔离Flaky测试**
```typescript
test('flaky: complex search', async ({ page }) => {
  test.fixme(true, 'Flaky - Issue #123')
})

test('conditional skip', async ({ page }) => {
  test.skip(process.env.CI, 'Flaky in CI - Issue #123')
})
```

**识别Flaky测试**
```bash
npx playwright test tests/search.spec.ts --repeat-each=10
```

#### 工件管理

| 工件类型 | 配置/用法 |
|----------|-----------|
| 截图 | `page.screenshot({ path: 'artifacts/xxx.png' })` |
| 追踪 | `browser.startTracing()` / `browser.stopTracing()` |
| 视频 | `use: { video: 'retain-on-failure' }` |

#### 特殊场景测试

**Wallet/Web3测试**
```typescript
await context.addInitScript(() => {
  window.ethereum = {
    isMetaMask: true,
    request: async ({ method }) => {
      if (method === 'eth_requestAccounts')
        return ['0x1234567890123456789012345678901234567890']
    }
  }
})
```

**金融/关键流程测试**
```typescript
test('trade execution', async ({ page }) => {
  test.skip(process.env.NODE_ENV === 'production', 'Skip on production')
  // ... 交易测试逻辑
})
```

#### E2E测试报告模板
```markdown
# E2E Test Report
**Date:** YYYY-MM-DD HH:MM
**Status:** PASSING / FAILING

## Summary
- Total: X | Passed: Y | Failed: A | Flaky: B

## Failed Tests
### test-name
**File:** tests/e2e/feature.spec.ts:45
**Error:** Expected element to be visible
**Recommended Fix:** [description]

## Artifacts
- HTML Report: playwright-report/index.html
- Screenshots: artifacts/*.png
- Videos: artifacts/videos/*.webm
```

#### 测试优先级

| 优先级 | 覆盖范围 |
|--------|----------|
| **P0** | 注册、登录、核心功能 |
| **P1** | 支付、设置、常用流程 |
| **P2** | 边界情况、管理功能 |
| **P3** | 罕见场景 |

---

## 📚 技能参考文档索引

| 文档 | 路径 | 内容 |
|------|------|------|
| 单元测试 | `references/unit-testing.md` | Jest, Vitest, pytest, **JUnit 5** |
| 集成测试 | `references/integration-testing.md` | API测试, Supertest |
| E2E测试 | `references/e2e-testing.md` | **Playwright POM**, Flaky处理, Web3测试 |
| 性能测试 | `references/performance-testing.md` | k6, 负载测试 |
| 安全测试 | `references/security-testing.md` | 安全检查清单 |
| 测试报告 | `references/test-reports.md` | 报告模板, **CI/CD集成** |
| QA方法论 | `references/qa-methodology.md` | 手动测试, 质量指标 |
| 自动化框架 | `references/automation-frameworks.md` | 框架模式, 扩展策略 |
| TDD铁律 | `references/tdd-iron-laws.md` | TDD方法论 |
| 测试反模式 | `references/testing-anti-patterns.md` | 常见问题识别 |

---

## 🔧 快速使用

### 触发关键词
```
test, testing, QA, unit test, integration test, E2E, 
coverage, performance test, security test, regression,
test strategy, test automation, test framework
```

### 使用示例

**编写Java单元测试**
```
帮我用JUnit 5为UserService写单元测试
```

**生成测试报告**
```
帮我配置GitHub Actions测试流水线
```

**定义测试目录**
```
这个Java项目应该怎么组织测试目录结构？
```

---

## 📊 修改统计

| 文件 | 新增内容 | 说明 |
|------|----------|------|
| `references/unit-testing.md` | ~170行 | JUnit 5 + Mockito 完整示例 |
| `references/test-reports.md` | ~470行 | 自动报告 + CI/CD集成 |
| `references/e2e-testing.md` | ~210行 | POM模式、Flaky处理、Web3测试 |
| `SKILL.md` | ~100行 | 目录结构约定 |

---

## ✅ 支持矩阵

| 语言 | 单元测试 | 集成测试 | E2E测试 | 覆盖率 | CI/CD |
|------|----------|----------|---------|--------|-------|
| JavaScript/TypeScript | Jest, Vitest | Supertest | Playwright | Istanbul | ✅ |
| Python | pytest | httpx | Playwright | coverage.py | ✅ |
| Java | **JUnit 5** | TestContainers | Selenium | **JaCoCo** | ✅ |

---

*本文档记录了 Test Master Skill v0.1.0 的优化更新内容*
