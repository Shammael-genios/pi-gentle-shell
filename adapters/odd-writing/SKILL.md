---
name: odd-writing
description: Redactar o reescribir documentos que consume un agente (AGENTS.md, skills, docs alcanzables por puntero) con las técnicas de escritura del upstream. Opt-in, cuando el documento es el entregable.
disable-model-invocation: true
---

# ODD + escritura para agentes (opt-in)

Adaptador de `pi-gentle-shell`. Referencias upstream, solo lectura y en inglés:
`reference/skills/writing-for-agents/SKILL.md` (documentos que consume un agente), y
`reference/skills/writing-fragments/SKILL.md`, `reference/skills/writing-beats/SKILL.md`,
`reference/skills/writing-shape/SKILL.md` para prosa larga. Se leen como técnica; el upstream `wait-what`
(re-pitch) y `teach` **no** se usan en este flujo.

## Cuándo

- El entregable es un documento: `AGENTS.md`, un `SKILL.md`, o un doc alcanzable por puntero.
- Nunca para reescribir prosa existente del producto (README, docs de negocio) dentro de una unidad que
  no lo pidió.

## Reglas de forma (las que importan)

1. **Descripción primero**: en un skill, la `description` decide cuándo se carga. Dice qué hace y cuándo
   aplica, con el disparador exacto; no "ayuda con X".
2. **Puntero sobre copia**: si el hecho ya vive en otro archivo, se enlaza con ruta exacta. Duplicar crea
   deriva.
3. **Una afirmación por línea larga**; sin adjetivos donde va un número; sin "básicamente", "simplemente".
4. **Vocabulario del repo**: los términos del glosario, no sinónimos creativos.
5. **Cero futuro incierto**: "pendiente" no es un motivo; si algo no se hace, va el motivo real.

## Trabajo

1. Identificar el consumidor: agente en runtime (`AGENTS.md`, skill) o humano (ficha, docs). Cambia el
   registro, no la precisión.
2. Escribir; después recortar todo lo que no cambie una acción del lector.
3. Verificar que cada ruta citada existe y que cada comando citado es el real del repo; que el documento no
   contradice `AGENTS.md` del repo consumidor.

## Gate de salida

- Toda ruta citada existe; todo comando citado existe en el repo o se verificó a mano.
- Sin afirmaciones sin dueño: cada regla tiene su motivo o su consecuencia.
