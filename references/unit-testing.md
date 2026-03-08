# Unit Testing

## Jest/Vitest Pattern

```typescript
describe('UserService', () => {
  let service: UserService;
  let mockRepo: jest.Mocked<UserRepository>;

  beforeEach(() => {
    mockRepo = { findById: jest.fn(), save: jest.fn() } as any;
    service = new UserService(mockRepo);
  });

  afterEach(() => jest.clearAllMocks());

  describe('getUser', () => {
    it('returns user when found', async () => {
      const user = { id: '1', name: 'Test' };
      mockRepo.findById.mockResolvedValue(user);

      const result = await service.getUser('1');

      expect(result).toEqual(user);
      expect(mockRepo.findById).toHaveBeenCalledWith('1');
    });

    it('throws NotFoundError when user not found', async () => {
      mockRepo.findById.mockResolvedValue(null);

      await expect(service.getUser('1')).rejects.toThrow(NotFoundError);
    });
  });
});
```

## pytest Pattern

```python
import pytest
from unittest.mock import Mock, AsyncMock

class TestUserService:
    @pytest.fixture
    def mock_repo(self):
        return Mock()

    @pytest.fixture
    def service(self, mock_repo):
        return UserService(mock_repo)

    async def test_get_user_returns_user(self, service, mock_repo):
        mock_repo.find_by_id = AsyncMock(return_value={"id": "1", "name": "Test"})

        result = await service.get_user("1")

        assert result == {"id": "1", "name": "Test"}
        mock_repo.find_by_id.assert_called_once_with("1")

    async def test_get_user_raises_not_found(self, service, mock_repo):
        mock_repo.find_by_id = AsyncMock(return_value=None)

        with pytest.raises(NotFoundError):
            await service.get_user("1")
```

## JUnit 5 Pattern (Java)

```java
import org.junit.jupiter.api.*;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository mockRepo;

    @InjectMocks
    private UserService service;

    @Nested
    @DisplayName("getUser")
    class GetUserTests {

        @Test
        @DisplayName("returns user when found")
        void returnsUser_whenFound() {
            User user = new User("1", "Test");
            when(mockRepo.findById("1")).thenReturn(Optional.of(user));

            User result = service.getUser("1");

            assertEquals(user, result);
            verify(mockRepo).findById("1");
        }

        @Test
        @DisplayName("throws NotFoundError when user not found")
        void throwsNotFound_whenNotFound() {
            when(mockRepo.findById("1")).thenReturn(Optional.empty());

            assertThrows(NotFoundException.class, () -> service.getUser("1"));
        }
    }

    @BeforeEach
    void setUp() {
        // Additional setup if needed
    }

    @AfterEach
    void tearDown() {
        // Cleanup if needed
    }
}
```

### Mockito Patterns

```java
// Mock return values
when(mockRepo.findById("1")).thenReturn(Optional.of(user));
when(mockRepo.findById("1")).thenReturn(Optional.empty());

// Mock void methods
doNothing().when(mockRepo).delete(any());
doThrow(new RuntimeException()).when(mockRepo).delete(any());

// Mock multiple calls
when(mockRepo.count())
    .thenReturn(1L)
    .thenReturn(2L)
    .thenReturn(3L);

// Verify interactions
verify(mockRepo).findById("1");
verify(mockRepo, times(2)).save(any());
verify(mockRepo, never()).delete(any());
verify(mockRepo, atLeastOnce()).findAll();

// Argument matchers
when(mockRepo.findByName(anyString())).thenReturn(user);
when(mockRepo.findByAge(anyInt())).thenReturn(users);
when(mockRepo.save(argThat(u -> u.getName().length() > 3))).thenReturn(user);

// Spy on real objects
UserService realService = new UserService(realRepo);
UserService spyService = spy(realService);
when(spyService.getUser("1")).thenReturn(mockUser);
```

### JUnit 5 Assertions

```java
// Basic assertions
assertEquals(expected, actual);
assertNotEquals(expected, actual);
assertTrue(condition);
assertFalse(condition);
assertNull(value);
assertNotNull(value);

// Array/Collection assertions
assertArrayEquals(expectedArray, actualArray);
assertTrue(list.contains(element));
assertEquals(3, list.size());

// Exception assertions
assertThrows(NotFoundException.class, () -> service.getUser("invalid"));
Exception ex = assertThrows(IllegalArgumentException.class, 
    () -> service.process(null));
assertEquals("Input cannot be null", ex.getMessage());

// Timeout assertions
assertTimeout(Duration.ofSeconds(1), () -> service.slowOperation());
assertTimeoutPreemptively(Duration.ofSeconds(1), () -> service.slowOperation());

// Grouped assertions
assertAll("user",
    () -> assertEquals("1", user.getId()),
    () -> assertEquals("Test", user.getName()),
    () -> assertTrue(user.isActive())
);
```

### Parameterized Tests

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.*;

@ParameterizedTest
@ValueSource(strings = {"test@test.com", "user@example.org", "admin@domain.com"})
@DisplayName("validates valid email formats")
void validatesValidEmails(String email) {
    assertTrue(validator.isValidEmail(email));
}

@ParameterizedTest
@CsvSource({
    "1, Alice",
    "2, Bob",
    "3, Charlie"
})
@DisplayName("creates users with correct names")
void createsUsersWithNames(String id, String name) {
    User user = new User(id, name);
    assertEquals(name, user.getName());
}

@ParameterizedTest
@MethodSource("userProvider")
@DisplayName("processes various user types")
void processesVariousUserTypes(User user, boolean expectedActive) {
    assertEquals(expectedActive, user.isActive());
}

static Stream<Arguments> userProvider() {
    return Stream.of(
        Arguments.of(new User("1", "Active", true), true),
        Arguments.of(new User("2", "Inactive", false), false)
    );
}
```

## Mocking Patterns

```typescript
// Mock functions
const mockFn = jest.fn();
mockFn.mockReturnValue('value');
mockFn.mockResolvedValue('async value');
mockFn.mockRejectedValue(new Error('error'));

// Mock modules
jest.mock('./database', () => ({
  query: jest.fn(),
}));

// Spy on existing methods
jest.spyOn(console, 'log').mockImplementation(() => {});
```

## Test Organization

```typescript
describe('Feature', () => {
  describe('happy path', () => {
    it('does expected behavior', () => {});
  });

  describe('edge cases', () => {
    it('handles empty input', () => {});
    it('handles max values', () => {});
  });

  describe('error cases', () => {
    it('throws on invalid input', () => {});
  });
});
```

## Quick Reference

### JavaScript/TypeScript (Jest/Vitest)

| Pattern | Use Case |
|---------|----------|
| `describe()` | Group related tests |
| `it()` / `test()` | Single test case |
| `beforeEach()` | Setup before each test |
| `jest.fn()` | Create mock function |
| `mockResolvedValue()` | Mock async return |
| `expect().toThrow()` | Assert exception |

### Python (pytest)

| Pattern | Use Case |
|---------|----------|
| `@pytest.fixture` | Shared test setup |
| `class TestX:` | Group related tests |
| `def test_x():` | Single test case |
| `Mock()` | Create mock object |
| `pytest.raises()` | Assert exception |
| `@pytest.mark.asyncio` | Async test marker |

### Java (JUnit 5)

| Pattern | Use Case |
|---------|----------|
| `@Test` | Single test case |
| `@Nested` | Group related tests |
| `@BeforeEach` | Setup before each test |
| `@Mock` | Create mock object |
| `@InjectMocks` | Inject mocks into class |
| `assertThrows()` | Assert exception |
| `@ParameterizedTest` | Data-driven tests |
| `@DisplayName()` | Human-readable test name |
