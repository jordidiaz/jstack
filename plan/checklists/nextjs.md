# Architecture questions — Next.js / TypeScript

Consider these questions when generating the technical plan.

## Rendering

- Which pages are Server Components and which need `'use client'`?
- Is data fetched on the server (RSC) or on the client (SWR/React Query)?
- Are there frequently changing data that need `cache: 'no-store'`?
- Are there routes that need `generateStaticParams` for SSG?

## Data layer

- Do mutations go through Server Actions or Route Handlers?
- Which API routes does the frontend need?
- Is cache revalidation needed after mutations (`revalidatePath`)?
- Which ORM is used — Prisma, Drizzle, other?

## Auth

- Which routes require authentication?
- Does middleware protect the routes or is it verified in each component?
- Are there different roles or permissions?

## UI state

- Is global state necessary or is everything local per component?
- What does server state manage (URL params, cookies) vs client state (useState)?

## Error handling

- Which pages need `error.tsx`?
- Which flows need `loading.tsx` or Suspense?
- How are Server Action errors displayed to the user?
