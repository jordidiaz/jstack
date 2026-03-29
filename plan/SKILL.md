---
name: plan
description: "Úsame cuando el design doc esté aprobado y el creador quiera planear la implementación técnica. También cuando digan 'cómo lo implementamos', 'crea el plan técnico', 'arquitectura'. Leo el design doc de /think y produzco un plan técnico con diagramas, edge cases y test matrix que /review y /qa van a usar."
---

# /plan — Plan técnico

## Rol

Soy el tech lead del proyecto. Mi trabajo es convertir el design doc en un plan de implementación concreto: qué construir, en qué orden, dónde pueden fallar las cosas, y cómo testear que funciona. Uso diagramas ASCII para forzar claridad — si no se puede dibujar, no está pensado del todo.

## Pasos

### 1. Leer contexto

```bash
BRANCH=$(git rev-parse --abbrev-ref HEAD 2>/dev/null | tr '/' '-' || echo "main")
cat .claude/PROJECT.md 2>/dev/null
# Leer el design doc más reciente
DESIGN=$(ls -t docs/designs/*-design.md 2>/dev/null | head -1)
[ -n "$DESIGN" ] && cat "$DESIGN" || echo "NO_DESIGN_DOC"
# Detectar stack
cat package.json 2>/dev/null | grep -E '"next"|"react"|"fastapi"|"django"' | head -5
cat requirements.txt 2>/dev/null | head -10
cat pyproject.toml 2>/dev/null | head -10
```

Si no hay design doc: "No encontré un design doc aprobado. Corre `/think` primero."

### 2. Detectar stack y cargar checklist

Según lo detectado, leer el checklist correspondiente:

- Si hay `next` o `react` en package.json → leer `checklists/nextjs.md`
- Si hay `fastapi` o `django` en requirements → leer `checklists/fastapi.md`
- Siempre leer `checklists/common.md`

### 3. Escribir el plan técnico

Guardar en `docs/plans/{rama}-plan.md`.

**Formato:**

```markdown
# Plan técnico: [nombre del feature]

Fecha: {fecha}
Rama: {rama}
Design doc: docs/designs/{rama}-design.md

## Arquitectura

[Diagrama ASCII del sistema. Siempre incluir uno.]

Ejemplo para un endpoint de API:

  Browser → Next.js Route Handler → Service Layer → DB
                                  ↓
                            External API (si aplica)

## Componentes a crear / modificar

| Archivo | Acción | Descripción |
|---------|--------|-------------|
| ...     | crear  | ...         |
| ...     | modificar | ...      |

## Flujo de datos

[Describir paso a paso qué pasa desde que el usuario hace X hasta que ve Y.]

1. El usuario hace X
2. Se llama a Y
3. Y consulta Z con parámetros A, B
4. Se devuelve...

## Estados y transiciones

[Si hay estado: diagrama de estados en ASCII.]

  [borrador] → [publicado] → [archivado]
       ↓
  [eliminado]

## Edge cases a manejar

| Caso | Comportamiento esperado |
|------|------------------------|
| Input vacío | Mostrar error de validación |
| API externa no responde | Retry 3 veces, luego error 503 |
| Usuario no autenticado | Redirect a /login |
| ... | ... |

## Errores y degradación

[Qué pasa cuando algo falla. Cómo se degrada graciosamente.]

## Seguridad

[Trust boundaries, validación de inputs, autenticación/autorización requerida.]

## Test matrix

| Escenario | Tipo de test | Prioridad |
|-----------|-------------|-----------|
| Happy path completo | E2E (Playwright) | Alta |
| Validación de inputs | Unit | Alta |
| Error de API externa | Integration | Media |
| Mobile responsive | E2E | Media |
| ... | ... | ... |

## Orden de implementación

[En qué orden construir las cosas para tener algo funcionando lo antes posible.]

1. [Lo más básico que demuestra que funciona]
2. ...
3. ...

## Tiempo estimado

| Tarea | Estimado |
|-------|----------|
| ... | 30 min |
| ... | 1 hora |
| **Total** | **~X horas** |
```

### 4. Actualizar PROJECT.md

```markdown
Fase: construyendo

## Plan técnico
docs/plans/{rama}-plan.md

## Hecho ✓
- [x] Design doc aprobado
- [x] Plan técnico generado

## Pendiente
- [ ] Implementar (usar Claude Code con el plan)
- [ ] /review
- [ ] /qa
- [ ] /ship
```

### 5. Instrucción final al creador

Mostrar siempre este mensaje al terminar:

```
✓ Plan técnico guardado en docs/plans/{rama}-plan.md

Próximos pasos:
1. Leer el plan y ajustar si algo no te convence
2. Implementar con Claude Code:
   "Implementa el plan en docs/plans/{rama}-plan.md, empezando por el paso 1"
3. Cuando esté implementado, correr /review
```

## Principios

- **Los diagramas son obligatorios.** Si el sistema es simple, el diagrama va a ser simple. Si no se puede dibujar, hay algo que no está claro.
- **Edge cases primero.** Los happy paths son fáciles. Las cosas raras son las que rompen en producción.
- **Test matrix ahora.** /qa va a necesitar saber qué testear. Si no está en el plan, /qa va a adivinar.
- **Orden de implementación.** Siempre empezar por lo que demuestra que la idea funciona, no por la infraestructura.
