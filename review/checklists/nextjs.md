# Checklist Next.js / TypeScript

Patrones específicos que revisar en proyectos Next.js y TypeScript.

## Rendering y Data Fetching

- [ ] ¿Se usa `fetch` con `cache: 'no-store'` donde se necesitan datos frescos?
- [ ] ¿Los Server Components no importan hooks de cliente sin directiva `'use client'`?
- [ ] ¿Hay hidration mismatch posible (fechas, valores random, datos del servidor)?
- [ ] ¿Las Server Actions tienen validación de inputs con zod o similar?
- [ ] ¿Se usa `revalidatePath` / `revalidateTag` donde corresponde después de mutations?

## Performance

- [ ] ¿Hay N+1 queries? (loop que hace fetch/query en cada iteración)
- [ ] ¿Las imágenes usan `next/image` con width/height o fill?
- [ ] ¿Los imports de librerías pesadas son dinámicos donde corresponde (`next/dynamic`)?
- [ ] ¿Hay `useEffect` innecesarios que podrían ser derivados de estado?

## TypeScript

- [ ] ¿Hay `any` explícitos que podrían tiparse correctamente?
- [ ] ¿Los props de componentes tienen tipos definidos (no `any`)?
- [ ] ¿Las respuestas de APIs externas están tipadas o validadas con zod?
- [ ] ¿Los enums o union types tienen todos los casos manejados?

## Auth y Seguridad

- [ ] ¿Las rutas protegidas verifican sesión tanto en middleware como en el componente?
- [ ] ¿Las Server Actions verifican que el usuario tiene permiso para la acción?
- [ ] ¿Los inputs del usuario pasan por validación antes de queries a la DB?
- [ ] ¿Hay datos sensibles que no deberían exponerse en Client Components?

## Manejo de errores

- [ ] ¿Hay `error.tsx` para capturar errores de renderizado?
- [ ] ¿Los fetch calls manejan errores de red y respuestas no-200?
- [ ] ¿Los loading states tienen `loading.tsx` o Suspense donde corresponde?
- [ ] ¿Las Server Actions devuelven errores tipados, no solo lanzan excepciones?

## Base de datos (si usa Prisma/Drizzle)

- [ ] ¿Hay queries sin índice sobre columnas que se usan en WHERE o ORDER BY?
- [ ] ¿Las transacciones agrupan operaciones que deben ser atómicas?
- [ ] ¿Las migraciones son reversibles?
