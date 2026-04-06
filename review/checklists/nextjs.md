# Next.js / TypeScript checklist

Specific patterns to review in Next.js and TypeScript projects.

## Rendering and Data Fetching

- [ ] Is `fetch` used with `cache: 'no-store'` where fresh data is needed?
- [ ] Do Server Components not import client hooks without the `'use client'` directive?
- [ ] Is there a possible hydration mismatch (dates, random values, server-side data)?
- [ ] Do Server Actions validate inputs with zod or similar?
- [ ] Is `revalidatePath` / `revalidateTag` used where appropriate after mutations?

## Performance

- [ ] Are there N+1 queries? (loop that does fetch/query on each iteration)
- [ ] Do images use `next/image` with width/height or fill?
- [ ] Are imports of heavy libraries dynamic where appropriate (`next/dynamic`)?
- [ ] Are there unnecessary `useEffect` calls that could be state derivations?

## TypeScript

- [ ] Are there explicit `any` types that could be typed correctly?
- [ ] Do component props have defined types (not `any`)?
- [ ] Are responses from external APIs typed or validated with zod?
- [ ] Do enums or union types have all cases handled?

## Auth and Security

- [ ] Do protected routes verify the session both in middleware and in the component?
- [ ] Do Server Actions verify that the user has permission for the action?
- [ ] Does user input go through validation before DB queries?
- [ ] Are there sensitive data that shouldn't be exposed in Client Components?

## Error handling

- [ ] Is there an `error.tsx` to catch rendering errors?
- [ ] Do fetch calls handle network errors and non-200 responses?
- [ ] Do loading states have `loading.tsx` or Suspense where appropriate?
- [ ] Do Server Actions return typed errors, not just throw exceptions?

## Database (if using Prisma/Drizzle)

- [ ] Are there queries without an index on columns used in WHERE or ORDER BY?
- [ ] Do transactions group operations that must be atomic?
- [ ] Are migrations reversible?
