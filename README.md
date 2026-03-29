# pa-stack

Framework de skills para Claude Code. Convierte a Claude Code en un equipo virtual para proyectos de fin de semana: piensa el problema, planea la implementación, revisa el código, testea en browser real y crea el PR.

La idea central: **el agente recuerda dónde quedaste**. Puedes cerrar Claude Code el domingo y retomar el sábado siguiente con `/status` sin tener que recordar nada.

## Skills

| Skill | Rol | Qué hace |
|-------|-----|----------|
| `/status` | Memoria | Lee el estado del proyecto y indica dónde quedaste |
| `/think` | YC Partner | Define el problema con 6 preguntas antes de codear |
| `/plan` | Tech Lead | Arquitectura, diagramas ASCII, edge cases, test matrix |
| `/review` | Staff Engineer | Encuentra bugs que pasan CI, auto-arregla lo obvio |
| `/qa` | QA Lead | Playwright en browser real, commits atómicos por bug |
| `/debug` | Debugger | Root cause analysis, nunca toca código sin entender |
| `/ship` | Release Eng | Tests, push, PR con contexto completo |
| `/docs-update` | Tech Writer | Actualiza README y CHANGELOG post-ship |
| `/retro` | Eng Manager | Métricas reales del sprint, sin fluff |
| `/careful` | Seguridad | Advierte antes de comandos destructivos |
| `/freeze` | Seguridad | Restringe edits a un directorio |
| `/unfreeze` | Seguridad | Desactiva freeze |

## El flujo del sprint

```
/status          ← inicio de sesión: ¿dónde estoy?
/think           ← Gate 1: definir y aprobar el problema
/plan            ← plan técnico con diagramas
[Claude Code]    ← implementar el plan
/review          ← Gate 2: revisar y aprobar antes de lanzar
/qa              ← testear en browser real
/ship            ← PR
/docs-update     ← actualizar docs
/retro           ← fin de semana: qué se logró
```

El creador aparece en **Gate 1** (aprobar el design doc) y **Gate 2** (aprobar los hallazgos de review/qa). El resto es autónomo.

## Instalación

### 1. Clonar en tu carpeta global de skills

```bash
git clone https://github.com/tu-usuario/pa-stack ~/.claude/skills/pa-stack
```

### 2. Agregar al proyecto

En el directorio de tu proyecto:

```bash
# Copiar el template de CLAUDE.md
cp ~/.claude/skills/pa-stack/CLAUDE.md.template CLAUDE.md

# Crear la carpeta de estado
mkdir -p .claude
cp ~/.claude/skills/pa-stack/PROJECT.md.template .claude/PROJECT.md

# Crear DECISIONS.md
echo "# Decisiones del proyecto\n" > DECISIONS.md

# Editar el PROJECT.md con el nombre del proyecto
# Editar CLAUDE.md con el stack y comandos del proyecto
```

### 3. Arrancar

```
/status
```

Si es un proyecto nuevo:
```
/think
```

## Estructura del proyecto con pa-stack

```
mi-proyecto/
├── .claude/
│   └── PROJECT.md          ← estado del proyecto (commiteado)
├── docs/
│   ├── designs/            ← design docs de /think
│   └── plans/              ← planes técnicos de /plan
├── CLAUDE.md               ← activa pa-stack en este repo
├── DECISIONS.md            ← log append-only de decisiones
└── [código del proyecto]
```

## Stack soportado

- **Next.js / TypeScript / React** — checklists específicos en /review y /plan
- **FastAPI / Python** — checklists específicos en /review y /plan
- **Playwright** — requerido para /qa

## Filosofía

pa-stack no es un copilot. No sugiere código mientras escribes. Es el proceso alrededor del código: pensar bien antes de arrancar, revisar antes de lanzar, y recordar dónde quedaste la próxima sesión.

La diferencia entre un proyecto de fin de semana que terminas y uno que abandonas suele ser si tienes claridad de qué falta y por dónde seguir. pa-stack mantiene esa claridad.

MIT License
