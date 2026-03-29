# Checklist común

Patrones que aplican a cualquier stack.

## Lógica y Correctitud

- [ ] ¿Hay condiciones de borde no manejadas (arrays vacíos, nulls, strings vacíos)?
- [ ] ¿La lógica de paginación es correcta (off-by-one, última página)?
- [ ] ¿Las comparaciones de fechas/timestamps tienen en cuenta timezones?
- [ ] ¿Los cálculos matemáticos manejan división por cero y overflow?
- [ ] ¿Hay lógica duplicada que podría introducir inconsistencias?

## Trust Boundaries

- [ ] ¿Se confía en datos del cliente que deberían validarse en el servidor?
- [ ] ¿Los IDs de recursos vienen del cliente sin verificar ownership?
- [ ] ¿Hay parámetros de query que se pasan directamente a queries o comandos?

## Race Conditions

- [ ] ¿Hay operaciones check-then-act que podrían fallar bajo concurrencia?
- [ ] ¿Las operaciones que deben ser atómicas están en transacciones?
- [ ] ¿Los contadores o saldos se actualizan con operaciones atómicas?

## Completitud

- [ ] ¿Hay enums o tipos con nuevos valores que no están manejados en todos los switch/if?
- [ ] ¿Todos los casos del test matrix del plan técnico están cubiertos?
- [ ] ¿Hay TODOs o comentarios `// hack` que indican código incompleto?

## Cleanup y Recursos

- [ ] ¿Se cierran conexiones, file handles, o streams después de usarlos?
- [ ] ¿Los uploads fallidos dejan archivos huérfanos en storage?
- [ ] ¿Los jobs encolados tienen manejo de retry y dead letter queue?

## Tests

- [ ] ¿El happy path tiene test?
- [ ] ¿Los edge cases del plan técnico tienen test?
- [ ] ¿Los tests testean comportamiento, no implementación interna?
- [ ] ¿Hay tests que solo verifican mocks sin tocar código real?
