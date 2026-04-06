# .NET / C# checklist

Specific patterns to review in .NET and C# projects.

## Async and Concurrency

- [ ] Are there blocking operations (`.Result`, `.Wait()`, `Thread.Sleep`) inside `async` methods?
- [ ] Are DbContext instances scoped correctly (not used across multiple requests)?
- [ ] Are there possible race conditions in operations that modify the same resource?
- [ ] Do background tasks handle exceptions without silencing them?
- [ ] Is `ConfigureAwait(false)` used appropriately in library code?

## Validation and DTOs

- [ ] Do all endpoint inputs go through FluentValidation or DataAnnotations?
- [ ] Are there optional fields that should be required or vice versa?
- [ ] Do response DTOs explicitly exclude internal fields (password hashes, internal IDs)?
- [ ] Are request models validated before reaching the service layer?

## Dependency Injection

- [ ] Are services registered with the correct lifetime (Singleton/Scoped/Transient)?
- [ ] Is there a captive dependency issue (Singleton depending on Scoped)?
- [ ] Are IDisposable services properly disposed (via DI container, not manually)?
- [ ] Are typed HttpClients registered with HttpClientFactory?

## Security

- [ ] Do endpoints that modify data verify the user owns the resource?
- [ ] Does user text input go through validation before being used in raw queries?
- [ ] Are passwords never logged or returned in responses?
- [ ] Do JWT tokens have expiration and are they validated correctly?
- [ ] Are CORS policies configured appropriately (not wildcard in production)?

## Entity Framework Core / Database

- [ ] Are there N+1 queries? (loop querying DB — use `Include()` or `Select()` projection)
- [ ] Do columns used in filters have indexes?
- [ ] Are transactions properly scoped with `BeginTransactionAsync`?
- [ ] Are migrations reversible (have a Down() method)?
- [ ] Are raw SQL queries (`FromSqlRaw`) parameterized to prevent injection?

## Error handling

- [ ] Do endpoints have proper exception handling for expected errors?
- [ ] Do HTTP errors use the correct status codes (400 vs 422 vs 404 vs 409 vs 500)?
- [ ] Is there a global exception handler middleware that prevents stack traces in production?
- [ ] Are `ProblemDetails` (RFC 7807) used for error responses?

## Performance

- [ ] Are there queries that could be paginated instead of returning everything?
- [ ] Are large payloads streamed where appropriate?
- [ ] Are there data that could be cached with IMemoryCache or IDistributedCache?
- [ ] Are `AsNoTracking()` queries used for read-only operations?
