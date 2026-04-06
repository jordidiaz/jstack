# Common checklist

Patterns that apply to any stack.

## Logic and Correctness

- [ ] Are there unhandled edge cases (empty arrays, nulls, empty strings)?
- [ ] Is the pagination logic correct (off-by-one, last page)?
- [ ] Do date/timestamp comparisons account for timezones?
- [ ] Do mathematical calculations handle division by zero and overflow?
- [ ] Is there duplicated logic that could introduce inconsistencies?

## Trust Boundaries

- [ ] Is client-supplied data being trusted when it should be validated on the server?
- [ ] Are resource IDs coming from the client without verifying ownership?
- [ ] Are query parameters being passed directly to queries or commands?

## Race Conditions

- [ ] Are there check-then-act operations that could fail under concurrency?
- [ ] Are operations that must be atomic wrapped in transactions?
- [ ] Are counters or balances updated with atomic operations?

## Completeness

- [ ] Are there enums or types with new values not handled in all switch/if statements?
- [ ] Are all cases from the technical plan's test matrix covered?
- [ ] Are there TODOs or `// hack` comments indicating incomplete code?

## Cleanup and Resources

- [ ] Are connections, file handles, or streams closed after use?
- [ ] Do failed uploads leave orphan files in storage?
- [ ] Do queued jobs have retry handling and a dead letter queue?

## Tests

- [ ] Does the happy path have a test?
- [ ] Do the technical plan's edge cases have tests?
- [ ] Do tests verify behavior, not internal implementation?
- [ ] Are there tests that only verify mocks without touching real code?
