---
name: odd-wayfinder
description: Mapear trabajo más grande que una sesión como un mapa de decisiones en odd/tasks/, y resolverlo de a una. Solo a demanda, cuando el destino existe pero el camino no se ve. Nunca usa un tracker externo.
disable-model-invocation: true
---

# ODD + wayfinder

Adaptador de `pi-gentle-shell`. Técnica de referencia: `reference/skills/wayfinder/SKILL.md` (upstream,
MIT, solo lectura, en inglés). El upstream abre "decision tickets" en un issue tracker; acá el tracker son
las fichas `odd/tasks/` del repo consumidor.

## Cuándo

Dos condiciones, ambas: el trabajo **no cabe en una sesión** (más de una unidad ODD encadenada) y el
camino no está visible todavía, aunque el destino sí. Si el camino ya está claro, esto es una lista de
unidades ODD y no hace falta mapa.

## Trabajo

1. Escribir el destino en una línea: qué es verdad cuando termina.
2. Enumerar las **decisiones** que hoy no se pueden tomar (no las tareas: las decisiones), cada una con
   lo que desbloquea.
3. Una ficha por decisión en `odd/tasks/<slug>.md`, con `## Specs` mínimo, `## Tasks` y `## Log`; y un
   índice en la ficha madre (`## Mapa`) listando el orden y qué depende de qué.
4. Resolver **una** decisión por vez: grill → `S#` → unidad. El mapa se actualiza al cerrar cada una.

## Reglas

- Cero tickets en GitHub/GitLab/Linear/Jira; cero `gh issue create`; nada de directorios temporales dentro
  del repo.
- El mapa no es un plan de ejecución: no contiene tareas de implementación, solo decisiones.
- No se resuelve una decisión "de paso" mientras se implementa otra.
- Si el mapa crece más de lo que el dueño pidió, se corta y se documenta el recorte.

## Gate de salida

- Cada decisión del mapa tiene ficha con `S#` o está marcada como abierta con su pregunta exacta.
- El índice del mapa coincide con las fichas que existen en disco.
