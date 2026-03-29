# Checklist FastAPI / Python

Patrones específicos que revisar en proyectos FastAPI y Python.

## Async y Concurrencia

- [ ] ¿Hay operaciones bloqueantes (requests sync, sleep, IO) dentro de funciones `async`?
- [ ] ¿Las queries a la DB usan sesiones async correctamente (no sync SQLAlchemy en async context)?
- [ ] ¿Hay race conditions posibles en operaciones que modifican el mismo recurso?
- [ ] ¿Los background tasks manejan errores sin silenciarlos?

## Pydantic y Validación

- [ ] ¿Los modelos Pydantic validan todos los inputs de endpoints?
- [ ] ¿Hay campos opcionales que deberían ser requeridos o viceversa?
- [ ] ¿Los tipos `Optional` usan `= None` como default?
- [ ] ¿Las respuestas usan `response_model` para no exponer campos internos?

## Dependencias e Inyección

- [ ] ¿Las dependencias (`Depends`) manejan cleanup con `yield` correctamente?
- [ ] ¿Las sesiones de DB siempre se cierran incluso si hay excepción?
- [ ] ¿La autenticación se verifica como dependencia en todos los endpoints que la requieren?

## Seguridad

- [ ] ¿Los endpoints que modifican datos verifican que el usuario es dueño del recurso?
- [ ] ¿Los inputs de texto pasan por validación antes de usarse en queries raw?
- [ ] ¿Las passwords nunca se logean ni se retornan en respuestas?
- [ ] ¿Los tokens tienen expiración y se validan correctamente?

## SQLAlchemy / Base de datos

- [ ] ¿Hay N+1 queries? (query en loop — usar `joinedload` o `selectinload`)
- [ ] ¿Las columnas usadas en filtros tienen índices?
- [ ] ¿Las transacciones están bien delimitadas con commit/rollback?
- [ ] ¿Los modelos tienen `__tablename__` explícito?

## Manejo de errores

- [ ] ¿Los endpoints tienen exception handlers para errores esperados?
- [ ] ¿Los errores HTTP usan los códigos correctos (400 vs 422 vs 500)?
- [ ] ¿Los errores de validación de Pydantic se mapean a respuestas legibles?
- [ ] ¿Los errores no exponen stack traces en producción?

## Performance

- [ ] ¿Hay queries que podrían paginarse en vez de devolver todo?
- [ ] ¿Las respuestas grandes usan `StreamingResponse` donde corresponde?
- [ ] ¿Hay datos que podrían cachearse con Redis u otra capa?
