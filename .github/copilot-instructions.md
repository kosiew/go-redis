# GitHub Copilot Instructions for go-redis

This project is **go-redis**, the official Redis client library for the Go programming language.
When contributing to this project, please follow these guidelines and conventions.

## Project Overview

- **Language**: Go (minimum version 1.18, currently supporting Go 1.23-1.24)
- **Purpose**: Official Redis client library providing a straightforward interface for interacting
  with Redis servers
- **Supported Redis Versions**: 7.2, 7.4, and 8.0 (including Redis Stack and Redis CE)
- **Testing Framework**: Ginkgo v2 with Gomega assertions

## Code Style and Conventions

### File Organization

- Command implementations go in `*_commands.go` files (e.g., `acl_commands.go`,
  `string_commands.go`)
- Tests are in corresponding `*_commands_test.go` files
- Use the `redis_test` package for test files
- Internal utilities go in the `internal/` directory

### Naming Conventions

- Use Redis command names directly in method names (e.g., `ACLLog`, `ACLSetUser`, `FTSearch`)
- Follow Go naming conventions: exported functions start with uppercase
- Use descriptive variable names, especially in tests

### Testing Guidelines

#### Test Structure

- Use Ginkgo's `Describe` and `It` blocks for organizing tests
- Include `BeforeEach` and `AfterEach` for setup and cleanup
- Use `Label` for categorizing tests (e.g., `Label("NonRedisEnterprise")`)

#### Test Patterns

```go
var _ = Describe("Feature Name", func() {
    var client *redis.Client
    var ctx context.Context

    BeforeEach(func() {
        ctx = context.Background()
        opt := redisOptions()
        client = redis.NewClient(opt)
    })

    AfterEach(func() {
        Expect(client.Close()).NotTo(HaveOccurred())
    })

    It("should perform specific action", func() {
        // Test implementation
    })
})
```

#### Assertions

- Use Gomega assertions: `Expect().To()`, `Expect().NotTo()`
- Check for errors: `Expect(err).NotTo(HaveOccurred())`
- Validate results: `Expect(result).To(Equal(expected))`
- Use `BeNumerically()` for numeric comparisons

#### Redis Version Compatibility

- Use `SkipBeforeRedisVersion(version, reason)` for version-specific tests
- Test with multiple Redis versions (7.2, 7.4, 8.0)
- Consider module availability differences between versions

### Error Handling

- Always check and handle errors appropriately
- Use `Expect(err).NotTo(HaveOccurred())` in tests
- Provide meaningful error messages in production code

### Context Usage

- Always pass `context.Context` as the first parameter to Redis operations
- Use `context.Background()` in tests unless testing context-specific behavior
- Respect context cancellation and timeouts

### Redis Modules Support

- Support for Redis Stack modules (Search, JSON, Bloom, etc.)
- Use appropriate version checks for module-specific features
- Test module permissions in ACL tests

### Performance Considerations

- Use connection pooling appropriately
- Consider pipeline operations for bulk commands
- Test performance-critical paths

### Documentation

- Include comprehensive examples in tests
- Document public APIs with Go doc comments
- Reference Redis documentation for command explanations

## Go Best Practices

### Code Organization and Structure

- Keep functions small and focused on a single responsibility
- Use meaningful package names that reflect their purpose
- Group related functionality together in the same file
- Separate concerns: commands, tests, utilities, and internal logic

### Variable and Function Naming

- Use camelCase for unexported functions and variables
- Use PascalCase for exported functions and types
- Choose descriptive names over short abbreviations
- Use consistent naming patterns throughout the codebase
- Prefer `ctx` for context.Context parameters
- Use `client` for Redis client instances in tests

### Error Handling

- Handle errors explicitly, don't ignore them
- Return errors as the last return value
- Use `fmt.Errorf` with `%w` verb for error wrapping when appropriate
- Provide context in error messages
- Use custom error types for specific error conditions

### Interface Design

- Keep interfaces small and focused (interface segregation)
- Define interfaces where they're used, not where they're implemented
- Use standard library interfaces when possible (io.Reader, fmt.Stringer, etc.)

### Memory Management

- Use sync.Pool for frequently allocated objects
- Prefer slices over arrays for dynamic data
- Be mindful of memory leaks in long-running operations
- Use context for cancellation and timeouts

### Concurrency

- Use channels for communication between goroutines
- Prefer sync.Mutex over channels for protecting shared state
- Use sync.Once for one-time initialization
- Always handle goroutine lifecycle properly
- Use context.Context for cancellation propagation

### Testing Best Practices

- Use table-driven tests for multiple test cases
- Keep test functions focused and independent
- Use descriptive test names that explain the scenario
- Follow AAA pattern: Arrange, Act, Assert
- Use testdata/ directory for test fixtures
- Mock external dependencies appropriately

### Performance Considerations

- Use benchmarks for performance-critical code
- Profile before optimizing
- Prefer simple solutions over premature optimization
- Use appropriate data structures for the use case
- Consider connection pooling for external resources

### Code Style

- Follow `gofmt` formatting
- Use `golint` and `go vet` for code quality
- Keep line length reasonable (typically under 100 characters)
- Use consistent indentation and spacing
- Group imports: standard library, third-party, local packages

### Documentation

- Write clear and concise comments
- Document public APIs with examples
- Use godoc conventions for documentation comments
- Keep comments up-to-date with code changes
- Explain the "why" not just the "what" in comments

### Dependency Management

- Use go.mod for dependency management
- Pin dependency versions for reproducible builds
- Regularly update dependencies for security patches
- Minimize external dependencies
- Use semantic versioning for releases

## Common Patterns

### Client Creation

```go
opt := redisOptions()
client := redis.NewClient(opt)
defer client.Close()
```

### Command Execution

```go
result, err := client.CommandName(ctx, args...).Result()
if err != nil {
    // handle error
}
```

### Testing Setup with Custom User

```go
// Create test user
err := client.ACLSetUser(ctx, "testuser", "nopass", "on", "allkeys", "+get").Err()
Expect(err).NotTo(HaveOccurred())

// Cleanup
defer func() {
    client.ACLDelUser(ctx, "testuser")
}()
```

## Redis-Specific Guidelines

### Command Implementation

- Follow Redis command syntax and semantics exactly
- Support all command options and variations
- Implement proper result parsing for complex return types

### ACL (Access Control List) Support

- Test user creation, modification, and deletion
- Verify permission enforcement
- Test module-specific permissions for Redis 8.0+
- Support both individual commands and command categories

### Module Integration

- Search (FT.\*), JSON, Bloom Filter, Cuckoo Filter
- Count-Min Sketch, Top-K, T-Digest, Time Series
- Proper error handling for unavailable modules

### Connection Management

- Support for standalone, cluster, and sentinel modes
- Proper connection pooling and lifecycle management
- Authentication and authorization support

## Development Workflow

1. **Setup**: Use `make docker.start` for development environment
2. **Testing**: Run `make test` or `make test.ci` for faster CI-style testing
3. **Code Quality**: Follow existing patterns and use proper error handling
4. **Documentation**: Update relevant documentation when adding features

## Dependencies

- Core: No external dependencies beyond Go standard library
- Testing: Ginkgo v2 and Gomega
- Utilities: cespare/xxhash and dgryski/go-rendezvous for consistent hashing

When writing code for this project, prioritize clarity, performance, and Redis compatibility while
following Go best practices and the established patterns in the codebase.
