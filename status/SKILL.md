---
name: status
description: "Úsame al inicio de cada sesión de trabajo, o cuando el creador escriba 'dónde estoy', 'qué falta', 'retomar', 'status' o cualquier variación. Leo el estado del proyecto y le digo al creador exactamente dónde quedó, qué toca hacer ahora y si hay bloqueantes."
---

# /status — Retomar el proyecto

## Lo que hago

Leo `.claude/PROJECT.md` y `DECISIONS.md` del proyecto actual y genero un reporte conciso de sesión. El creador no tiene que recordar nada — yo lo recuerdo por él.

## Pasos

### 1. Leer estado

```bash
cat .claude/PROJECT.md 2>/dev/null || echo "NO_PROJECT_FILE"
cat DECISIONS.md 2>/dev/null || echo "NO_DECISIONS_FILE"
git rev-parse --abbrev-ref HEAD 2>/dev/null
git log --oneline -5 2>/dev/null
```

Si no existe `.claude/PROJECT.md`, decirle al creador:
> "No encontré estado de pa-stack en este proyecto. ¿Quieres inicializarlo con `/think`?"

### 2. Generar reporte de sesión

Mostrar siempre en este formato, sin inventar nada que no esté en PROJECT.md:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 pa-stack — retomando proyecto
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Proyecto:     [nombre]
Rama:         [rama actual]
Última sesión: [fecha de PROJECT.md]
Sprint:       [objetivo del sprint actual]

✓ Hecho
  [lista de lo completado]

→ Pendiente
  [lista de lo que falta, en orden]

⚠ Bloqueantes
  [si hay, cuáles son — si no hay, omitir esta sección]

Decisiones relevantes
  [las 2-3 decisiones más recientes de DECISIONS.md]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Siguiente paso sugerido: /[skill]
```

### 3. Sugerir el siguiente comando

Basarse en la fase actual del PROJECT.md:

| Fase en PROJECT.md | Sugerir |
|---|---|
| `pensando` | `/think` — continuar o revisar el design doc |
| `planeando` | `/plan` — generar el plan técnico |
| `construyendo` | nada — el creador usa Claude Code directamente |
| `revisando` | `/review` — revisar el código de la rama |
| `testeando` | `/qa` — correr QA con Playwright |
| `lanzando` | `/ship` — crear el PR |
| `listo` | `/retro` si es fin de semana, o `/think` para el siguiente sprint |

## Notas

- Nunca inventar estado. Si PROJECT.md no tiene algo, decir "no registrado".
- Si hay bloqueantes, resaltarlos con ⚠ claramente.
- Ser conciso. El creador quiere saber dónde está en 10 segundos, no leer un ensayo.
