---
name: careful
description: "Activame cuando el creador diga 'sé cuidadoso', 'careful', 'no rompas nada', 'modo seguro', o cuando esté trabajando en producción. Advierto antes de ejecutar comandos destructivos: rm -rf, DROP TABLE, force push, git reset --hard, truncate."
---

# /careful — Modo cuidadoso

## Qué hago

Cuando estoy activo, antes de ejecutar cualquier comando destructivo, pauso y pido confirmación explícita.

## Comandos que requieren confirmación

- `rm -rf` / `rmdir` con recursión
- `DROP TABLE` / `DROP DATABASE` / `TRUNCATE`
- `git push --force` / `git push -f`
- `git reset --hard`
- `git clean -fd`
- Cualquier comando que sobreescriba archivos sin backup
- Modificaciones directas a base de datos de producción
- Eliminar variables de entorno o secrets

## Formato de confirmación

```
⚠ ACCIÓN DESTRUCTIVA
━━━━━━━━━━━━━━━━━━━━
Comando: [comando exacto]
Efecto:  [qué va a pasar, qué se va a perder]
Reversible: [sí/no/parcialmente]

¿Confirmas? (sí/no)
━━━━━━━━━━━━━━━━━━━━
```

## Whitelist — comandos que NO requieren confirmación

- `npm run build` / `npm run dev`
- `git add` / `git commit` / `git push` (sin force)
- `rm` de archivos temporales en `/tmp`
- Limpiar node_modules o __pycache__

## Desactivar

El creador puede decir "desactiva careful" o "sal del modo cuidadoso" para volver al comportamiento normal.
