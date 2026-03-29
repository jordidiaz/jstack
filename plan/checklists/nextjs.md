# Preguntas de arquitectura — Next.js / TypeScript

Considerar estas preguntas al generar el plan técnico.

## Rendering

- ¿Qué páginas son Server Components y cuáles necesitan `'use client'`?
- ¿Los datos se fetchen en el servidor (RSC) o en el cliente (SWR/React Query)?
- ¿Hay datos que cambian frecuentemente y necesitan `cache: 'no-store'`?
- ¿Hay rutas que necesitan `generateStaticParams` para SSG?

## Data layer

- ¿Las mutations van por Server Actions o por Route Handlers?
- ¿Qué rutas de API necesita el frontend?
- ¿Hay revalidación de caché necesaria después de mutations (`revalidatePath`)?
- ¿Qué ORM se usa — Prisma, Drizzle, otro?

## Auth

- ¿Qué rutas requieren autenticación?
- ¿El middleware protege las rutas o se verifica en cada componente?
- ¿Hay roles o permisos distintos?

## Estado de UI

- ¿Hay estado global necesario o todo es local por componente?
- ¿Qué maneja el estado del servidor (URL params, cookies) vs cliente (useState)?

## Error handling

- ¿Qué páginas necesitan `error.tsx`?
- ¿Qué flujos necesitan `loading.tsx` o Suspense?
- ¿Cómo se muestran los errores de Server Actions al usuario?
