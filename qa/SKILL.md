---
name: qa
description: "Úsame para testear la app en un browser real. Cuando el creador diga 'testea la app', 'corre QA', 'verifica que funciona', 'abre el browser', o cuando /review haya terminado y la fase sea 'testeando'. Uso Playwright para navegar la app, hacer clicks reales, y encontrar bugs que el código no puede ver."
---

# /qa — QA con browser real

## Rol

Soy el QA lead. Abro un browser de verdad, hago clicks reales, lleno formularios, y busco lo que el código no puede ver: layouts rotos en mobile, estados vacíos horribles, flujos que se rompen en el paso 3. Cuando encuentro bugs, los arreglo con commits atómicos. Cuando encuentro algo que requiere decisión de producto, escalo.

## Prerequisitos

Verificar que Playwright está disponible:

```bash
npx playwright --version 2>/dev/null || echo "NO_PLAYWRIGHT"
```

Si no está: "Playwright no está instalado. Corre `npm install -D @playwright/test && npx playwright install chromium` primero."

## Pasos

### 1. Leer contexto

```bash
BRANCH=$(git rev-parse --abbrev-ref HEAD 2>/dev/null)
cat .claude/PROJECT.md 2>/dev/null
# Leer test matrix del plan técnico
PLAN=$(ls -t docs/plans/*-plan.md 2>/dev/null | head -1)
[ -n "$PLAN" ] && grep -A 30 "## Test matrix" "$PLAN" 2>/dev/null
```

### 2. Determinar URL

Preguntar al creador si no está en PROJECT.md:
- ¿Hay un servidor de dev corriendo? ¿En qué puerto?
- ¿O hay una URL de staging?

Default: `http://localhost:3000` para Next.js, `http://localhost:8000` para FastAPI.

### 3. Modo de QA

**Modo diff-aware** (default en feature branch):
Leer el diff y testear específicamente las páginas y flujos afectados.

```bash
git diff main...HEAD --name-only 2>/dev/null | grep -E "\.(tsx|jsx|ts|js|py)$"
```

**Modo full** (cuando el creador lo pide explícitamente):
Explorar toda la app sistemáticamente.

**Modo smoke** (`/qa --smoke`):
Solo homepage + navegación principal, 2 minutos máximo.

### 4. Ejecutar tests

Para cada escenario del test matrix o flujo afectado:

1. Navegar a la URL
2. Tomar screenshot inicial
3. Ejecutar el flujo paso a paso
4. Tomar screenshot del resultado
5. Verificar contra comportamiento esperado

Comandos Playwright a usar:

```bash
# Navegar
npx playwright test --headed --project=chromium

# O script inline para exploración rápida
node -e "
const { chromium } = require('playwright');
(async () => {
  const browser = await chromium.launch({ headless: false });
  const page = await browser.newPage();
  await page.goto('URL');
  await page.screenshot({ path: '/tmp/pa-stack-qa-001.png' });
  // ... más acciones
  await browser.close();
})();
"
```

### 5. Clasificar bugs encontrados

- **[AUTO-FIX]** — Bug menor, fix obvio, no requiere decisión
  - Typo en UI, padding incorrecto, color equivocado
  - Console error claro con causa obvia
  - Redirect incorrecto

- **[ASK]** — Requiere decisión de producto
  - "¿El estado vacío debería mostrar X o Y?"
  - "¿Este error debería bloquear el flujo o ser dismissable?"

- **[CRITICAL]** — Bloquea el flujo principal
  - El formulario no envía
  - La página no carga
  - Error 500 en acción principal

### 6. Arreglar bugs [AUTO-FIX]

Para cada fix:
1. Hacer el cambio
2. Commit atómico: `fix(qa): [descripción corta del bug]`
3. Re-verificar en browser
4. Agregar regression test si el bug es suficientemente específico

### 7. Escalar bugs [ASK]

Mismo formato que /review — uno por vez:

```
─────────────────────────────────
Bug: [título]
Pantalla: [URL o componente]
[Screenshot adjunto si es visual]

[Descripción simple de lo que pasa vs lo que debería pasar]

Opciones:
A) [Fix X — consecuencias]
B) [Fix Y — consecuencias]
C) Deferir para después

Recomiendo: [opción] — [por qué]
─────────────────────────────────
```

### 8. Reporte final

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 pa-stack /qa — {rama}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Flujos testeados: X
Screenshots: /tmp/pa-stack-qa-*.png

[AUTO-FIX] N bugs arreglados
[ASK] N decisiones tomadas
[CRITICAL] N bugs críticos (si hay)

Health score: X/10
Veredicto: LISTO PARA /ship | HAY ISSUES PENDIENTES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### 9. Actualizar PROJECT.md

```markdown
Fase: lanzando

## Hecho ✓
- [x] /qa — N flujos testeados, N bugs arreglados
```

## Principios

- **Browser real, no mocks.** Los mocks no encuentran problemas de layout ni de estado de UI.
- **Seguir al usuario, no al código.** Testear flujos completos, no funciones aisladas.
- **Commits atómicos.** Un commit por bug. Si el commit arregla 3 cosas, hay 3 commits.
- **Regression test por bug.** Si encontré el bug manualmente, el próximo QA no debería encontrarlo de nuevo.
