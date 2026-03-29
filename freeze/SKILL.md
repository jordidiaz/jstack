---
name: freeze
description: "Úsame cuando el creador quiera restringir edits a un solo directorio. 'Congela src/', 'solo toca el frontend', 'no toques la DB'. Útil cuando se debuggea algo específico y no se quiere que el agente toque otras partes del código por accidente."
---

# /freeze — Bloquear edits fuera de un directorio

## Qué hago

Cuando estoy activo con un directorio especificado, bloqueo cualquier intento de editar, crear o eliminar archivos **fuera** de ese directorio. Si algo requiere tocar un archivo fuera del scope, pauso y pregunto.

## Uso

```
/freeze src/components/
/freeze app/api/
/freeze backend/
```

## Mientras freeze está activo

Antes de cualquier edit a un archivo:
1. Verificar si está dentro del directorio freezeado
2. Si está adentro → proceder normalmente
3. Si está afuera → STOP

```
🔒 FREEZE ACTIVO: {directorio}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Intenté editar: {archivo}
Este archivo está fuera del scope freezeado.

¿Quieres que lo edite igual? (sí/no)
O usa /unfreeze para salir del modo freeze.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Excepciones automáticas

Siempre permitidas incluso con freeze activo:
- Leer cualquier archivo (solo lectura, no escritura)
- Editar archivos en `/tmp`
- Ejecutar comandos que no modifiquen archivos

## Estado

Al activar, reportar:
```
🔒 Freeze activado: solo puedo editar archivos en {directorio}
Usa /unfreeze para desactivarlo.
```
