---
name: docs-update
description: "Úsame después de lanzar para actualizar la documentación del proyecto. Cuando el creador diga 'actualiza los docs', 'el README está desactualizado', o después de un /ship exitoso. Leo qué cambió en el PR y actualizo README, CHANGELOG, y cualquier doc que haya quedado obsoleto."
---

# /docs-update — Actualizar documentación

## Rol

Soy el technical writer que cierra el loop. Cada feature que se shipea deja docs desactualizados: el README que no menciona la nueva funcionalidad, el CHANGELOG sin entrada, los ejemplos de API que ya no son correctos. Lo encuentro y lo arreglo.

## Pasos

### 1. Leer qué cambió

```bash
BRANCH=$(git rev-parse --abbrev-ref HEAD 2>/dev/null)
git diff main...HEAD --name-only 2>/dev/null
git diff main...HEAD -- "*.md" 2>/dev/null
cat .claude/PROJECT.md 2>/dev/null
```

### 2. Encontrar docs que actualizar

```bash
# Listar todos los archivos de documentación
find . -name "*.md" -not -path "*/node_modules/*" \
       -not -path "*/.git/*" \
       -not -path "*/docs/designs/*" \
       -not -path "*/docs/plans/*" \
       2>/dev/null
```

### 3. Revisar cada doc relevante

Para cada archivo `.md` encontrado, verificar:

- ¿Menciona funcionalidades que cambiaron?
- ¿Tiene instrucciones de instalación/uso que cambiaron?
- ¿Los ejemplos de código siguen siendo válidos?
- ¿La estructura de carpetas descrita sigue siendo correcta?

### 4. Actualizar docs

**README.md** — siempre revisar:
- Features listados
- Instrucciones de setup
- Ejemplos de uso
- Estructura del proyecto

**CHANGELOG.md** — agregar entrada si existe:

```markdown
## [Unreleased] / {fecha}

### Agregado
- [Feature nuevo]

### Cambiado
- [Comportamiento que cambió]

### Arreglado
- [Bug que se arregló]
```

Si no existe CHANGELOG.md, crearlo con formato básico.

**Otros docs** — actualizar solo si hay cambios reales. No tocar si no cambió nada relevante.

### 5. Commit

```bash
git add -A "*.md"
git commit -m "docs: actualizar documentación post-ship {rama}"
```

### 6. Reportar

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 pa-stack /docs-update
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Docs revisados: X
Docs actualizados: Y
  - [lista de archivos modificados]
Docs sin cambios: Z
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Principios

- **Solo actualizar lo que cambió.** No reescribir docs por reescribirlos.
- **Ejemplos de código deben funcionar.** Si hay ejemplos, verificar que sean válidos con el código actual.
- **CHANGELOG es para humanos.** Escribir en lenguaje simple, no en nombres de commits.
