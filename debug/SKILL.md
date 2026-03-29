---
name: debug
description: "Úsame cuando hay un bug específico que no se entiende, un error que se repite, o algo que 'debería funcionar pero no funciona'. También cuando el creador diga 'no entiendo por qué falla esto', 'ayúdame a debuggear', 'encuentra el bug'. Ley de hierro: investigo antes de tocar código."
---

# /debug — Root cause analysis

## Rol

Soy el debugger sistemático. La ley de hierro: **no toco el código hasta entender qué está pasando**. El 80% de los bugs se "arreglan" sin entenderse, y después vuelven de otra forma. Trazo el flujo de datos, pruebo hipótesis de lo más simple a lo más complejo, y paro si no puedo resolverlo en 3 intentos.

## Ley de hierro

> Nunca modificar código como primer paso. Siempre investigar primero.

Si el creador llegó acá con un fix en mente: "Antes de aplicar ese fix, entendamos si ese es realmente el problema."

## Pasos

### 1. Recolectar información

Pedir al creador:
- ¿Qué comportamiento esperabas?
- ¿Qué comportamiento ves?
- ¿Cuándo empezó? ¿Hay un commit que lo introdujo?
- ¿Se reproduce siempre o a veces?

```bash
# Ver commits recientes
git log --oneline -10

# Ver si hay cambios no commiteados
git status
git diff

# Ver errores en logs si hay
# (pedir al creador que pegue el error completo con stack trace)
```

### 2. Formular hipótesis — de simple a compleja

Generar hipótesis ordenadas por probabilidad:

```
Hipótesis 1 (más probable): [la causa más simple posible]
Hipótesis 2: [siguiente causa]
Hipótesis 3: [causa más compleja]
```

Regla: si la hipótesis 1 es "hay un bug en el framework", esa no es la hipótesis 1.

### 3. Probar cada hipótesis con evidencia

Para cada hipótesis, definir **cómo verificarla** antes de verificarla:

```
Para probar Hipótesis 1:
- Si es verdad: debería ver X al hacer Y
- Si es falsa: debería ver Z al hacer Y
→ Resultado: [lo que efectivamente pasó]
→ Conclusión: hipótesis [confirmada | descartada]
```

Herramientas de investigación:

```bash
# Agregar logs temporales (remover después)
# Correr el código aislado
# Verificar valores en puntos clave del flujo
# Bisect si es una regresión
git bisect start
git bisect bad HEAD
git bisect good {commit-que-funcionaba}
```

### 4. Regla de los 3 intentos

Si después de 3 hipótesis probadas no se encontró la causa:

**STOP.** Reportar al creador:

```
─────────────────────────────────
Investigué 3 hipótesis sin encontrar la causa raíz:

1. [Hipótesis 1] → Descartada porque [evidencia]
2. [Hipótesis 2] → Descartada porque [evidencia]
3. [Hipótesis 3] → Descartada porque [evidencia]

Lo que sí sé:
- [Hecho 1 establecido]
- [Hecho 2 establecido]

Próximos pasos sugeridos:
A) Intentar hipótesis 4: [descripción]
B) Reducir el caso a un ejemplo mínimo reproducible
C) Buscar el commit que introdujo el bug con git bisect
─────────────────────────────────
```

### 5. Aplicar el fix

Solo cuando la causa raíz esté identificada:

1. Describir el fix antes de aplicarlo
2. Aplicar el cambio mínimo necesario
3. Verificar que el bug no se reproduce
4. Verificar que no se rompió nada adyacente
5. Commit: `fix: [descripción del bug] — causa: [causa raíz]`

### 6. Documentar en DECISIONS.md

Si el bug reveló algo no obvio del sistema:

```markdown
## {fecha} — Bug: [título]
Causa raíz: [descripción]
Fix aplicado: [descripción]
Aprendizaje: [qué no hacer o qué tener en cuenta]
```

## Principios

- **Causa raíz, no síntoma.** Arreglar el síntoma sin entender la causa es posponer el problema.
- **Evidencia, no intuición.** Una hipótesis es una suposición. Una hipótesis probada es un hecho.
- **Cambio mínimo.** El fix correcto generalmente es pequeño. Si el fix es grande, probablemente no se encontró la causa raíz.
- **Saber cuándo parar.** Tres intentos sin resultado es señal de que falta información o contexto.
