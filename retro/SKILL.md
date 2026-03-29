---
name: retro
description: "Úsame al final de la semana o del sprint para ver qué se logró. Cuando el creador diga 'retro', 'qué hice esta semana', 'resumen del sprint', o 'qué tan bien estamos'. Analizo el git log con métricas reales y genero un reporte honesto sin fluff."
---

# /retro — Retrospectiva del sprint

## Rol

Soy el eng manager que mira los números sin romantizarlos. Commits, LOC, ratio de tests, PRs mergeados, bugs encontrados en QA vs producción. Sin vibes, sin "fue una buena semana" sin datos.

## Pasos

### 1. Definir período

Default: última semana (7 días). Si el creador especifica otro período, usarlo.

```bash
SINCE="7 days ago"
# o la fecha que el creador indique
```

### 2. Recolectar métricas de git

```bash
# Commits en el período
git log --oneline --since="$SINCE" 2>/dev/null
COMMIT_COUNT=$(git log --oneline --since="$SINCE" 2>/dev/null | wc -l)

# LOC agregadas/eliminadas
git log --since="$SINCE" --numstat --format="" 2>/dev/null | \
  awk '{added+=$1; removed+=$2} END {print "+"added" -"removed}'

# Archivos más tocados
git log --since="$SINCE" --name-only --format="" 2>/dev/null | \
  sort | uniq -c | sort -rn | head -10

# PRs mergeados (si hay mensajes de merge)
git log --oneline --since="$SINCE" 2>/dev/null | grep -i "merge\|pr\|pull request" | wc -l

# Horas de actividad (por timestamps de commits)
git log --since="$SINCE" --format="%ad" --date=format:"%H" 2>/dev/null | \
  sort | uniq -c | sort -rn | head -5

# Tests
find . -name "*.test.*" -o -name "*.spec.*" -o -name "test_*.py" 2>/dev/null | wc -l
git log --since="$SINCE" --oneline 2>/dev/null | grep -i "test\|spec\|fix" | wc -l
```

### 3. Leer historial de PROJECT.md para contexto

```bash
cat .claude/PROJECT.md 2>/dev/null
cat DECISIONS.md 2>/dev/null | tail -20
```

### 4. Generar reporte

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 pa-stack /retro — semana {fecha}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

MÉTRICAS
  Commits:     {N}
  LOC:         +{agregadas} / -{eliminadas} (neto: {neto})
  Tests:       {total} archivos de test ({ratio}% del código)
  PRs:         {N} mergeados

ACTIVIDAD
  Horas pico:  {hora}-{hora} (según timestamps de commits)
  Día más activo: {día}
  Archivos más tocados:
    {archivo} ({N} veces)
    ...

SPRINT
  Objetivo:    [del PROJECT.md]
  Estado:      [completado | en curso | bloqueado]
  Features:    [lista de lo hecho]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LO QUE FUNCIONÓ BIEN
[2-3 observaciones honestas basadas en los datos]

LO QUE MEJORAR
[2-3 observaciones honestas — si el ratio de tests bajó, decirlo]

PRÓXIMA SEMANA
[Sugerencia basada en el estado actual del PROJECT.md]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### 5. Guardar snapshot

```bash
mkdir -p .claude/retros
DATE=$(date +%Y-%m-%d)
# Guardar el reporte en .claude/retros/{fecha}.md
```

### 6. Archivar el sprint en PROJECT.md

Si el sprint está completado (fase: listo):

```markdown
## Sprints archivados

### Sprint {fecha} — {objetivo}
Estado: completado
Commits: N | LOC neto: +N | Tests: N
```

Y limpiar la sección "Pendiente" para el próximo sprint.

## Principios

- **Números reales, no estimados.** Si no hay dato, decir "no hay dato", no inventar.
- **Honestidad sobre el ratio de tests.** Si bajó, es señal de alerta, no hay que suavizarlo.
- **Tendencias importan más que el número absoluto.** Un commit de 500 LOC puede ser mejor o peor que 50 commits de 10 LOC.
- **Sin fluff.** "Fue una semana productiva" sin datos no significa nada.
