# Unit Testing Reference

## Goals
- Validate behavior of a single unit in isolation.
- Cover happy path, edge cases, and failure path.

## Core Checklist
- Arrange-Act-Assert structure.
- Mock external I/O (DB, network, file system, clock).
- Use descriptive test names with expected behavior.
- Assert outcomes, not implementation details.

## Framework Notes
- JS/TS: Jest/Vitest (`describe/it`, `beforeEach`, spies/mocks).
- Python: pytest (`fixtures`, `parametrize`, `raises`).
- Java: JUnit 5 + Mockito (`@Test`, `@ParameterizedTest`, `verify`).

## JUnit 5 + Mockito snippet
```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
  @Mock UserRepository repo;
  @InjectMocks UserService service;

  @Test
  void returnsUser_whenFound() {
    when(repo.findById("1")).thenReturn(Optional.of(new User("1", "Alice")));
    User result = service.getUser("1");
    assertEquals("Alice", result.getName());
    verify(repo).findById("1");
  }
}
```
