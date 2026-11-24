---
name: odd-triage
description: Clasificar y verificar entradas de trabajo nuevas sobre las fichas de odd/tasks/, sin tracker externo ni labels de GitHub. Opt-in, cuando el dueño trae trabajo sin dueño o un pedido ambiguo que hay que convertir en unidad.
disable-model-invocation: true
---

# ODD + triage (opt-in)

Adaptador de `pi-gentle-shell`. Referencia upstream, solo lectura y en inglés:
`reference/skills/triage/SKILL.md`. El upstream mueve issues por un estado de roles con labels de
GitHub/GitLab; acá el tracker son las fichas `odd/tasks/` del repo consumidor y no se crean labels.

## Cuándo

- Llegó trabajo sin dueño (un pedido, un hallazgo, un bug) y hay que decidir si es unidad, si necesita
  información, o si no va.
- Nunca para reordenar fichas existentes por gusto: `odd/tasks/` se ordena por dependencia real.

## Trabajo

1. **Categorizar**: bug / deuda / feature / pregunta / nada. Si es "nada", se dice y se cierra la entrada
   sin crear archivo.
2. **Verificar reproducibilidad** antes de escribir una ficha: bug ⇒ camino reproducible o síntoma
   exacto; feature ⇒ decisión que falta. Sin eso, la ficha no se abre: se devuelve la pregunta.
3. **Unidad o no**: si el pedido tiene más de una decisión independiente, se parte; si es más grande que
   una sesión, va a `odd-wayfinder`.
4. **Escribir la ficha** `odd/tasks/<slug>.md` con `## Specs` (`S#` mínimo: pedido del dueño, alcance,
   fuera de alcance), `## Tasks` y `## Log` vacío. Un pedido sin `S#` no es ficha.
5. **Verificar antes de trabajar**: si el pedido ya está resuelto en el repo, se demuestra con evidencia
   y se cierra sin ficha.

## Reglas

- Cero `gh`/`glab`, cero labels, cero trackers externos.
- Una ficha abierta sin `S#` es deuda de proceso: se completa o se borra.
- El triage no grilla: si hay más de una rama viva, se rutea a `grill-me` después de abrir la ficha.
- No se toca ningún tracker preexistente ajeno a `odd/tasks/`.

## Gate de salida

- Toda entrada queda con destino explícito: ficha creada, unidad existente, o cerrada con motivo.
- Ninguna ficha nueva queda sin `## Specs`.
