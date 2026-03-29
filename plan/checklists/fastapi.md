# Preguntas de arquitectura — FastAPI / Python

Considerar estas preguntas al generar el plan técnico.

## Endpoints

- ¿Qué operaciones son síncronas y cuáles async?
- ¿Qué endpoints modifican datos — necesitan transacciones?
- ¿Hay operaciones lentas que deberían ser background tasks?
- ¿Qué endpoints necesitan paginación?

## Modelos Pydantic

- ¿Qué campos son opcionales vs requeridos en cada operación (create vs update)?
- ¿Hay campos que el servidor calcula y no deberían venir del cliente?
- ¿Los response models ocultan campos internos (passwords, tokens)?

## Base de datos

- ¿Qué índices necesitan las queries del plan?
- ¿Hay operaciones que requieren transacciones para ser atómicas?
- ¿Las migraciones Alembic están planificadas?
- ¿Hay seeds de datos necesarios?

## Auth y permisos

- ¿Qué endpoints requieren autenticación?
- ¿Hay recursos donde el usuario solo puede ver/modificar los suyos?
- ¿Las dependencias de FastAPI verifican ownership correctamente?

## Errores

- ¿Qué errores de negocio devuelven 400 vs 404 vs 409?
- ¿Cómo se manejan los errores de APIs externas?
- ¿Hay rate limiting necesario?
