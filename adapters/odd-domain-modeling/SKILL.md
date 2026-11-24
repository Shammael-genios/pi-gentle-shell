---
name: odd-domain-modeling
description: Modelar el vocabulario de una unidad ODD DENTRO de la misma ronda de grill, escribiendo glosario y ADR. Usar solo cuando la unidad ya está nombrada y su vocabulario tiene más de una lectura viva. No abre una segunda entrevista.
disable-model-invocation: true
---

# ODD + domain modeling

Adaptador de `pi-gentle-shell`. La técnica está en `reference/skills/domain-modeling/SKILL.md` (upstream,
MIT, solo lectura, en inglés). Este archivo dice cómo encaja en ODD y qué NO se hace.

## Dueño y encaje

ODD es dueño. Esto no es una fase: es la mitad "vocabulario" de la **ronda de grill ya abierta** por
`grill-me` (regla global, no se copia). Una unidad, un conjunto de rondas, un solo dueño de la pregunta.

- Si la ronda de grill no está abierta, esto no se ejecuta: no se abre una entrevista para modelar.
- Si el `S#` que define el término ya está escrito, está **settled**: no se re-pregunta ni se "mejora" el
  término sin una rama nueva.

## Entrada

- La unidad nombrada y su frontier de decisiones abiertas.
- Los términos que el código ya usa, leídos del código real, no de la memoria del dueño.

## Trabajo

1. Para cada término en disputa: listar las dos o más lecturas vivas, el escenario de borde que las
   separa, y cuál cambia una decisión o un test. Un término que no cambia nada **no entra** al glosario.
2. Resolver con el dueño dentro de la ronda vigente: una pregunta, una recomendación, una condición de
   reversión. Nunca una segunda ronda para el mismo término.
3. Escribir el resultado:
   - término y decisión → el glosario del repo consumidor; si no hay, `docs/glossary.md` del repo y se
     dice en el `## Log`
   - decisión costosa o irreversible → `docs/adr/NNNN-<slug>.md` (siguiente número libre del repo)
   - si cambia el contrato de una unidad → `S#` en `odd/tasks/<unidad>.md`, con `Proveniencia:` y
     `Reversión:`

## Reglas

- No se inventa terminología nueva para renombrar código existente: renombrar es otra unidad.
- Un término por entrada; sin sinónimos "por si acaso".
- El glosario de este bundle (`docs/glossary.md`) describe el vocabulario del flujo. El glosario de
  dominio del producto es del repo consumidor y no se crea desde acá.

## Gate de salida

- Cada término agregado aparece en una decisión (`S#`) o en un ADR; si no, se borra la entrada.
- La ficha `odd/tasks/<unidad>.md` sigue siendo Markdown válido y no repite un `S#` existente.
