---
name: odd-research
description: Investigar un hecho externo que el repositorio no puede contestar (docs de terceros, contrato de API externa, comportamiento de librería) y dejar la respuesta como artefacto en la ficha ODD. Solo a demanda; nunca en lugar de leer el repo.
disable-model-invocation: true
---

# ODD + research

Adaptador de `pi-gentle-shell`. Técnica de referencia: `reference/skills/research/SKILL.md` (upstream,
MIT, solo lectura, en inglés). El upstream arranca un agente de fondo; **acá no se delega por defecto**: se
investiga en este contexto, o con un subagente solo si el dueño lo autorizó para esta unidad.

## Cuándo

Solo si la pregunta es un **hecho externo** y el repo no puede responderla. Antes de invocarlo, descartar
las dos fuentes gratuitas:

1. El código, la configuración y los docs del propio repo (incluido este bundle como referencia).
2. El lockfile, el paquete instalado o la versión real de la dependencia.

Si cualquiera de las dos responde, esto no se invoca.

## Trabajo

1. Formular la pregunta como algo falsable, con la versión exacta de la dependencia.
2. Ir a fuentes primarias: doc oficial de la versión, changelog, el `.d.ts` o el código del paquete
   instalado. Nada de blogs si hay fuente primaria.
3. Escribir el hallazgo donde el próximo lo va a leer:
   - lo que cambia una decisión → `S#` nuevo en `odd/tasks/<unidad>.md` con `Proveniencia: research <fuente>`
   - lo que solo informa → `## Log` de la ficha, en una línea, con la URL o la ruta exacta.
4. Declarar el límite: qué no se pudo confirmar y con qué evidencia faltaría.

## Reglas

- Una respuesta no verificada contra una fuente primaria no entra a la ficha como hecho: entra como
  pregunta abierta.
- No se instala ni se actualiza nada para investigar.
- No se escribe en `reference/skills/**`: el upstream no se edita.

## Gate de salida

- El hallazgo tiene fuente citable (URL, ruta del paquete, o salida de comando observada).
- La ficha quedó actualizada, no solo el chat.
