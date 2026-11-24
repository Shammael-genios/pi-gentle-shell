---
name: odd-prototype
description: Construir un prototipo desechable para contestar una pregunta de diseño que el código no puede contestar (modelo de estados, forma de flujo). Código siempre en temp, nunca en el checkout. A demanda.
disable-model-invocation: true
---

# ODD + prototype

Adaptador de `pi-gentle-shell`. Técnica de referencia: `reference/skills/prototype/SKILL.md` (upstream,
MIT, solo lectura, en inglés). El upstream separa prototipo de UI (`UI.md`) y de lógica (`LOGIC.md`).

## Cuándo

La pregunta es de **diseño** y el repo no la contesta leyendo: "¿este modelo de estados se siente bien?",
"¿esta forma de flujo aguanta el caso borde X?". Si la pregunta se contesta leyendo el código existente,
esto no se invoca.

Un prototipo contesta **una** pregunta. Si aparecen dos, son dos prototipos o ninguno.

## Dónde vive el código

- Temp del sistema (`/tmp`, `%TEMP%`), nunca en el checkout, nunca en el código de producto ni en los
  tests.
- Sin dependencias nuevas del repo: se usa lo que ya está instalado, o nada.
- Al terminar: la pregunta, la respuesta y el camino al archivo desechable quedan en el `## Log` de
  `odd/tasks/<unidad>.md`. El prototipo no se commitea.

## Trabajo

1. Escribir la pregunta en una línea, con el criterio que la contesta.
2. Prototipo mínimo: una sola hipótesis, cero integración con el sistema real.
3. Ejercitarlo y anotar la respuesta **y** lo que el prototipo no prueba.
4. Si la respuesta cambia el contrato: `S#` en la ficha con `Proveniencia: prototype <ruta temp>` y su
   `Reversión:`.

## Reglas

- Cero código de producto en el checkout: el prototipo es desechable y no es un borrador de feature.
- Un prototipo no es un plan de implementación ni un scaffold. Lo que sobrevive se re-escribe en ODD.
- No se convierte en test: si algo merece test, se escribe el test en su unidad, con su seam.

## Gate de salida

- La pregunta quedó contestada o declarada como no contestada, con el motivo.
- `git status --short` no muestra archivos nuevos por el prototipo.
