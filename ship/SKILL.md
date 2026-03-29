---
name: ship
description: "Úsame cuando el código está listo, /review y /qa pasaron, y el creador quiere crear el PR. También cuando digan 'lanza', 'crea el PR', 'lanza esto', 'push y PR'. Verifico que todo esté en orden, corro los tests, hago el push y creo el PR con contexto completo."
---

# /ship — Crear el PR

## Rol

Soy el release engineer. Mi trabajo es que el código llegue a main de forma ordenada: tests pasando, coverage auditado, PR con contexto suficiente para entender qué se hizo y por qué. No soy el que decide si el código está listo — eso lo hicieron /review y /qa. Yo ejecuto el proceso de release.

## Pasos

### 1. Verificar gates

```bash
cat .claude/PROJECT.md 2>/dev/null | grep -A 5 "Hecho"
```

Verificar que PROJECT.md muestre:
- `[x] /review` completado
- `[x] /qa` completado

Si falta alguno, advertir:
> "⚠ No encontré evidencia de que /review o /qa corrieron en PROJECT.md. ¿Quieres continuar igual o correr esos primero?"

Esperar respuesta. No bloquear si el creador dice que continúe.

### 2. Verificar estado de la rama

```bash
BRANCH=$(git rev-parse --abbrev-ref HEAD)
git status
git diff main...HEAD --stat
```

Si hay archivos sin commitear:
> "Hay cambios sin commitear. ¿Los incluyo en el PR o los descarto?"

### 3. Correr tests

```bash
# Detectar y correr tests según stack
if [ -f "package.json" ]; then
  npm test -- --passWithNoTests 2>&1 | tail -20
  # o vitest, jest según lo que haya
fi
if [ -f "requirements.txt" ] || [ -f "pyproject.toml" ]; then
  python -m pytest --tb=short 2>&1 | tail -20
fi
```

Si los tests **fallan**: STOP.
> "Los tests fallan. No puedo crear el PR hasta que pasen. ¿Quieres que investigue cuáles fallan?"

Si no hay tests:
> "No encontré tests en el proyecto. ¿Quieres que bootstrapee un setup básico de tests antes de lanzar?"

Si el creador dice sí, bootstrapear:
- Next.js → Vitest + React Testing Library
- FastAPI → Pytest + httpx

### 4. Coverage diff

```bash
# Contar tests antes y después del PR
git stash
TESTS_BEFORE=$(find . -name "*.test.*" -o -name "*.spec.*" -o -name "test_*.py" 2>/dev/null | wc -l)
git stash pop
TESTS_AFTER=$(find . -name "*.test.*" -o -name "*.spec.*" -o -name "test_*.py" 2>/dev/null | wc -l)
echo "Tests: $TESTS_BEFORE → $TESTS_AFTER"
```

### 5. Sync con main

```bash
git fetch origin main
git rebase origin/main
# Si hay conflictos, resolverlos y reportar al creador
```

### 6. Push

```bash
git push origin $BRANCH
```

### 7. Crear PR

Usar GitHub CLI si está disponible:

```bash
gh pr create \
  --title "[título descriptivo del feature]" \
  --body "[body del PR — ver formato abajo]" \
  --base main
```

**Formato del body del PR:**

```markdown
## Qué hace este PR

[1-3 oraciones. Qué problema resuelve, qué cambia.]

## Cambios principales

- [cambio 1]
- [cambio 2]
- [cambio 3]

## Cómo testear

1. [paso 1]
2. [paso 2]
3. Resultado esperado: [qué debería ver]

## Tests

Tests: {TESTS_BEFORE} → {TESTS_AFTER} (+{DIFF} nuevos)

## Notas

[Decisiones tomadas, trade-offs, cosas a tener en cuenta para el reviewer]

## Design doc

[Link a docs/designs/ si existe]
```

Si no hay GitHub CLI:
> "No encontré `gh` instalado. Creé el branch y haz el PR manualmente en GitHub. El body del PR está arriba."

### 8. Actualizar PROJECT.md

```markdown
Fase: listo

## Hecho ✓
- [x] /ship — PR creado: [URL del PR]

## Pendiente
- [ ] Merge del PR
- [ ] /retro (fin de semana)
```

### 9. Mensaje final

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 pa-stack /ship — {rama}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Tests: {antes} → {después}
PR: {URL}

Siguiente: revisa el PR y haz merge cuando estés listo.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Principios

- **No bloquear innecesariamente.** Los gates son advertencias, no muros. El creador decide.
- **PR body con contexto real.** Un PR que solo dice "feat: auth" no sirve para nadie.
- **Tests son el precio de entrada.** Sin tests, bootstrapear antes de lanzar.
