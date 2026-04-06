# Architecture questions — .NET / C#

Consider these questions when generating the technical plan.

## API Design

- Which operations are synchronous and which are async?
- Which endpoints modify data — do they need database transactions?
- Are there slow operations that should be background tasks (IHostedService, Hangfire)?
- Which endpoints need pagination?
- Are endpoints versioned or is versioning not needed yet?

## Models and Validation

- What fields are optional vs required in each operation (create vs update)?
- Are there fields the server calculates that should not come from the client?
- Do response DTOs hide internal fields (passwords, tokens, internal IDs)?
- Which validator is used — FluentValidation, DataAnnotations, or both?

## Database (Entity Framework Core)

- Which indexes do the plan's queries need?
- Are there operations that require transactions to be atomic?
- Are EF Core migrations planned and reversible?
- Are there seed data needed?
- Are lazy loading or explicit loading strategies needed?

## Auth and permissions

- Which endpoints require authentication?
- Are there resources where the user can only see/modify their own?
- Are authorization policies or roles needed beyond simple authentication?
- How is the current user identity accessed in controllers/services (IHttpContextAccessor)?

## Dependency injection and architecture

- Which services are Singleton, Scoped, or Transient?
- Is a repository pattern used or direct DbContext in services?
- Are there external services (HTTP clients) that need typed clients with HttpClientFactory?
- Is MediatR / CQRS pattern used?

## Errors

- Which business errors return 400 vs 404 vs 409?
- Is there a global exception handler (middleware or IExceptionHandler)?
- How are external API errors handled?
- Is rate limiting needed (ASP.NET Core Rate Limiting middleware)?
