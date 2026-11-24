# Glosario del flujo

Vocabulario del flujo ODD portable. El glosario de dominio del producto es del repo consumidor y no se
crea acá.

| Término | Definición |
| --- | --- |
| **ODD** | El proceso dueño: ficha `odd/tasks/<unidad>.md` con `## Specs` (`S#`), `## Tasks` y `## Log`; gates con salida observada, reporte de cierre de siete partes y audit con hash. |
| **Unidad** | El entregable que cambiaría si una respuesta se invirtiera. Un pedido con tres problemas son tres unidades. |
| **Grill** | La ronda de preguntas del ciclo: solo lo que el repo no puede contestar y lo que tiene más de una rama viva. La ejecuta `grill-me` (global, no se copia). |
| **Frontier** | El conjunto de decisiones abiertas de una unidad que hoy están listas para responderse. Se pregunta completo por ronda, no de a una. |
| **Rama viva** | Una decisión con al menos dos respuestas defendibles. Sin rama viva no hay pregunta: hay decisión del agente con su condición de reversión. |
| **`S#`** | Decisión numerada de una unidad, con procedencia y condición de reversión. Vive en la ficha. |
| **Settled** | Un `S#` ya escrito. No se re-grilla ni se re-discute sin una rama nueva que lo contradiga. |
| **Seam** | La interfaz pública por la que se ejerce el comportamiento en un test. Sin seam no hay Strict TDD posible. |
| **RED / GREEN** | Fallo observado antes de implementar / verde observado después. Sin salida observada no hay evidencia. |
| **RDD** | La revisión de decisiones de diseño del ciclo. Existe una sola por unidad; la reseña Spec vs Standards es verificación y no la reemplaza ni la repite. |
| **Adaptador** | Un `SKILL.md` de `adapters/odd-*` que traduce una técnica upstream al encaje ODD y prohíbe sus partes mutantes. Entrada manual, no default. |
| **Default por fase** | La regla de `ODD.md`: dentro de una fase ODD con workflow correspondiente, se hace `read` del `SKILL.md` upstream y se sigue su secuencia, sin slash y sin justificar valor. |
| **Upstream** | Las skills de Matt Pocock copiadas en `reference/skills/**`, intactas y en inglés, con `reference/PROVENANCE.md` como procedencia. |
| **Tool `Skill`** | El tool de otro cliente que algunos punteros upstream invocan. **No existe en Pi** y no se simula. |
| **Trust (proyecto)** | Aprobación del dueño para que Pi lea los recursos de proyecto (`.pi/`, `.agents/skills/`). Solo la otorga el dueño, por proyecto; este flujo no la cambia ni la autoaprueba. |
| **Cuerpo mutante** | Un `SKILL.md` upstream cuyos pasos escriben hooks, dependencias, trackers o archivos del repo. Se lee, no se ejecuta. |
| **Gate** | Comando exacto con salida observada que decide si un paso terminó. Vive en `docs/gates.md`. |
| **No disponible** | Un gate que no se puede correr, con su motivo real. No es verde y no se omite en silencio. |
| **Deuda declarada** | Lo que quedó sin hacer, con el motivo real. "Pendiente" no es un motivo. |
| **Bloque de activación** | El texto pequeño que el dueño agrega al final de su `AGENTS.md` global para apuntar a `ODD.md`. No sobreescribe nada. |
