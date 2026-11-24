# Reseña Spec vs Standards, y PR reversible

Referencia upstream, solo lectura y en inglés: `reference/skills/code-review/SKILL.md`. Se toma el método
(dos ejes independientes sobre el mismo diff), no su salida ni su tracker.

## Reseña en dos ejes, una sola pasada

**Alcance fijo**: el diff de la unidad contra su punto de partida declarado en la ficha. No se reseña el
repo entero ni cambios ajenos.

| Eje | Pregunta | Fuente de verdad |
| --- | --- | --- |
| **Spec** | ¿el diff hace exactamente lo que dice `odd/tasks/<unidad>.md` y cada `S#`? ¿sobra algo que ninguna `S#` cubre? ¿falta algo que una `S#` exige? | la ficha |
| **Standards** | ¿sigue las convenciones del repo consumidor (estructura de módulos, formato de respuesta, lint/format, migraciones)? | la convención observable en el repo y en su `AGENTS.md` |

### Eje Spec: la cadena, en orden

1. **Decisión** — la respuesta del dueño tal como quedó registrada, o la fuente que resuelve una decisión
   técnica.
2. **`S#`** — la decisión trasladada sin cambiar su semántica, con su fuente y su reversión.
3. **Comportamiento** — el diff leído contra el `S#`, no contra su propia descripción.
4. **Prueba/evidencia** — `expected` derivado del `S#`, nunca de la salida observada del diff.

Reglas de la cadena:

- **Contradicción ≠ gate rojo.** Un gate verde cuyo `expected` salió de la implementación no prueba la
  decisión: la contradicción se detecta **aunque esté verde** (`docs/testing-decisions.md`, tercera forma
  de falso verde).
- **La contradicción vuelve al carril como bloqueante** cuando rompe una regla acordada (`S#` settled). La
  reseña no la arregla ni la absorbe en el mismo diff.
- **Reinterpretación:** si el comportamiento solo se sostiene con una lectura nueva de la regla, no se
  decide en silencio y no se re-grilla: se pide `S#` nuevo (o se anota en deuda) **solo si la expectativa
  cambia de verdad**. Si no cambia, es contradicción y aplica la regla anterior.
- **Tipo de decisión:** humana (expectativa, alcance, autoridad) se pregunta al dueño; técnica (resoluble
  contra el repo o una fuente citada) se resuelve y se registra con `Proveniencia: agente`. La cadena no
  agrega artefacto, etapa ni matriz: en una unidad simple se recorre en la misma pasada.

### Evidencia que no se acepta

- Una relectura de estado que no serializa dos secuencias distintas: son operaciones separadas y se piden
  por separado.
- Un test que asume exclusividad mutua entre dos estados sin declararlo en el fixture.
- Un `expected` copiado de la salida observada: prueba que el código hace lo que hace, no lo que la
  decisión manda.
- Un comando citado que no existe en el repo (dependencias ausentes, script inexistente) no es gate: se
  declara no disponible con su motivo.

### Reglas de la reseña

- Una sola pasada. La reseña es **verificación**, no un segundo RDD: no se reabre el diseño, no se
  re-grilla un `S#` settled, no se propone una arquitectura alternativa.
- Hallazgo sin `S#` que lo cubra: no se arregla dentro de la reseña. Se propone `S#` nuevo (o se anota en
  deuda declarada con motivo).
- Hallazgo de Standards que ya existía antes del diff: no se arregla acá; se anota con destino explícito.
- Salida en el `## Log` de la ficha: una línea por hallazgo con `S#`, severidad y dónde quedó el arreglo.

## PR: antes / después / reversión

Referencia upstream, solo lectura y en inglés: `reference/skills/pr/SKILL.md`. El cuerpo va en
`odd/pr-bodies/<unidad>/NN.md`, numerado por unidad.

```markdown
## Antes
<estado observable previo: comando y salida, o comportamiento concreto. Sin adjetivos.>

## Después
<estado observable nuevo: mismo comando, salida nueva.>

## Reversión
<comando exacto: `git revert <sha>` o el borrado concreto de archivos>
<y cómo se comprueba que volvió: qué comando y qué salida esperada>

## Verificación
<los gates corridos, con el resultado real: N/N, y los no disponibles con su motivo>
```

Reglas del PR:

- Toda fila "Después" tiene que poder deshacerse con la sección "Reversión" sin leer el diff.
- Si la reversión no es trivial (datos, migración, secretos), se dice explícitamente y se propone la
  alternativa (migración correctiva, feature flag).
- Sin push ni apertura de PR si el dueño no lo autorizó: el archivo es el artefacto.
