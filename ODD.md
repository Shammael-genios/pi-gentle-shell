# ODD.md — flujo ODD portable con los workflows de Matt Pocock

Entrada operativa de `pi-gentle-shell`. Se lee entera una vez y se consulta por fase. No es un tracker, no
abre unidades y no reemplaza las reglas globales del dueño (ver "Precondiciones").

Rutas relativas a la raíz de este bundle. Los `SKILL.md` de `reference/skills/**` están **intactos y en
inglés**: se leen tal como están, no se traducen ni se editan.

## Precondiciones globales (no copiadas acá)

Este bundle **no** copia ni reimplementa lo que ya es global:

| Regla global existente | Qué se usa del bundle |
| --- | --- |
| `grill-me` (grill ODD, skill global) | nada: el bundle solo dice en qué fase entra y qué workflow se pliega a esa ronda |
| `security-audit` (skill global) | nada: el cierre auditado sigue siendo del ciclo ODD, con `scope_paths` acotado |
| Cierre de ODD: reporte de siete partes + audit con hash registrado | nada: el bundle aporta artefactos del flujo, no un segundo cierre |

Si alguna de esas reglas no existe en el entorno, se reporta `no disponible: regla global ausente` y el
ciclo queda incompleto. No se inventa una versión local, no se crea un tool `Skill` falso y no se simula
la skill ausente.

## Dueño único del ciclo

ODD es el proceso dueño: ficha `odd/tasks/<unidad>.md` con `## Specs` (`S#`), `## Tasks` y `## Log`; PR en
`odd/pr-bodies/<unidad>/NN.md`. Ningún workflow de `reference/skills/**` abre su propia fase, su propio
ciclo de preguntas ni su propio tracker: se ejecuta **dentro** de la fase ODD que le corresponde.

Si el repo donde se trabaja no usa ODD con esa ruta de tracker, el flujo no aplica: se dice y se trabaja
como ese repo mande.

## Default por fase: el workflow se usa, no se ofrece

En una fase ODD con workflow correspondiente, se hace `read` de la ruta exacta de la tabla y se sigue su
secuencia y sus gates dentro del paso vigente. Sin slash, sin justificar que aporta valor, sin lista de
gatillos como condición previa. No se recorre el catálogo: se usa el workflow de la fase.

| Fase ODD | Workflow por defecto (ruta que se lee) | Artefacto |
| --- | --- | --- |
| decision / grill | `grill-me` global; `reference/skills/domain-modeling/SKILL.md` si el vocabulario de la unidad está en disputa | `S#` con reversión; glosario o ADR si el término lo pide |
| hecho externo que el repo no contesta | `reference/skills/research/SKILL.md` | hallazgo con fuente, en `S#` o en `## Log` |
| pregunta de diseño | `reference/skills/prototype/SKILL.md`, código en temp | respuesta + ruta del desechable |
| trabajo más grande que una sesión | `reference/skills/wayfinder/SKILL.md` | mapa de decisiones en `odd/tasks/` |
| diagnóstico | `reference/skills/diagnosing-bugs/SKILL.md` | loop rojo: comando exacto y salida observada |
| implementación | `reference/skills/tdd/SKILL.md`; `reference/skills/codebase-design/SKILL.md` si la interfaz o el seam están en duda | RED y GREEN observados; decisión de diseño si el seam cambia |
| reseña | `reference/skills/code-review/SKILL.md`, dos ejes en una pasada | hallazgos en `## Log` con `S#` |
| PR | `reference/skills/pr/SKILL.md`, solo el cuerpo | `odd/pr-bodies/<unidad>/NN.md` |
| documento que consume un agente | `reference/skills/writing-for-agents/SKILL.md` | el documento |
| cambio de sesión o de agente | `reference/skills/handoff/SKILL.md` | handoff de una pantalla |
| retro | `reference/skills/retro/SKILL.md`, solo a pedido del dueño | hasta tres cambios de entorno |

En Pi no existe un tool `Skill`. La invocación interna es `read` de la ruta de la tabla; un puntero
upstream que diga "Call the Skill tool" se traduce a esa lectura. No se simula el tool.

## Cuándo se omite (cinco motivos, y la razón se dice)

1. **La fase no aplica** a esta unidad (ODD corto: sin grill, sin prototipo, sin diagnóstico).
2. **El resultado ya existe y está vigente**: repro reproducible, spec, reseña o ciclo TDD ya hechos en la
   unidad.
3. **Añade trabajo ya hecho o una segunda pasada**: segunda entrevista, segundo spec, segunda revisión del
   mismo diff.
4. **Falta precondición, herramienta o autoridad**: trust no otorgado, dependencias sin instalar, tracker
   externo, subagentes no disponibles, permiso de commit/push/publicación.
5. **Incompatibilidad concreta con ODD o con Pi**: otro orquestador, tool `Skill`, hooks de otro cliente.

Antes de omitir el workflow entero por (4) o (5) se adapta su **parte portable**: el método se sigue y se
omite solo el paso que exige lo que falta (`code-review` sin subagentes en paralelo, `pr` sin push,
`diagnosing-bugs` sin instrumentación en producción, `handoff` sin escribir en el repo).

La omisión se anota en el `## Log` de la ficha **solo** cuando el trabajo ya está trackeado. No se abre un
registro nuevo para dejar escrita una omisión.

## Lo que no se pregunta

- **Diagnóstico reproducible**: si ya hay un camino reproducible (un test que falla, un comando exacto, un
  log), se corre tal cual y se pega la salida real. No se pregunta cómo reproducir ni se pide permiso para
  correr el test que ya existe.
- **TDD y seams**: si el seam y el runner ya existen, Strict TDD va **ON** con RED y GREEN observados. Si
  no hay seam, se declara la excepción y el motivo; no se inventa un mock estructural para fingir el
  ciclo. Un comando que no existe en el repo no se cita como gate: se reporta `no disponible` con el
  motivo (`docs/testing-decisions.md`).
- **Tipo de decisión**: humana (expectativa, alcance, autoridad) se pregunta al dueño; técnica (resoluble
  contra el repo o una fuente citada) se resuelve y se registra con `Proveniencia: agente` y su condición
  de reversión.
- **Formato**: el del repo consumidor. Si no hay convención, se usa la de este bundle (`S#`, `## Log`,
  `odd/pr-bodies/`).

## Trazabilidad: decisión → `S#` → comportamiento → evidencia

La reseña del eje Spec recorre esta cadena en orden, y la unidad está cerrada solo si la cadena se
sostiene completa:

1. **Decisión** — la respuesta del dueño tal como quedó registrada, o la fuente que resuelve una decisión
   técnica.
2. **`S#`** — la decisión trasladada **sin cambiar su semántica**, con su fuente y su reversión.
3. **Comportamiento** — el diff leído contra el `S#`, no contra su propia descripción.
4. **Evidencia** — el `expected` derivado del `S#`, nunca de la salida observada del diff.

Reglas de la cadena:

- **Contradicción ≠ gate rojo.** Un gate verde cuyo `expected` salió de la implementación no prueba la
  decisión: la contradicción se detecta **aunque esté verde**.
- **La contradicción vuelve al carril como bloqueante** cuando rompe una regla acordada (un `S#` settled).
  La reseña no la arregla ni la absorbe en el mismo diff.
- **Reinterpretación:** si el comportamiento solo se sostiene con una lectura nueva de la regla, no se
  decide en silencio y no se re-grilla: se pide `S#` nuevo (o se anota en deuda declarada) **solo si la
  expectativa cambia de verdad**. Si no cambia, es contradicción y aplica la regla anterior.
- **Sin artefacto extra:** la cadena no agrega etapa, matriz ni segunda revisión; en una unidad simple se
  recorre en la misma pasada. El registro de las decisiones de este bundle está en `TRACEABILITY.md`.

## Adaptadores manuales (opt-in, no son el default)

`adapters/odd-*/SKILL.md` traducen una técnica upstream al encaje ODD y prohíben sus partes mutantes.
Tienen `disable-model-invocation: true`: nunca se autoseleccionan. Son entrada manual, y solo aparecen
como `/skill:odd-*` si el dueño instaló el manifest opcional (`package.json`); si no, se leen por ruta.

| Adaptador | Upstream que traduce | Se usa cuando |
| --- | --- | --- |
| `adapters/odd-domain-modeling` | `domain-modeling` | la ronda de grill vigente necesita vocabulario, glosario o ADR |
| `adapters/odd-research` | `research` | falta un hecho externo que el repo no puede contestar |
| `adapters/odd-prototype` | `prototype` | hay una pregunta de diseño que el código no contesta |
| `adapters/odd-wayfinder` | `wayfinder` | el trabajo no cabe en una sesión y el camino no se ve |
| `adapters/odd-architecture` | `codebase-design`, `improve-codebase-architecture` | fricción estructural fuera de la unidad, a pedido |
| `adapters/odd-triage` | `triage` | llega trabajo sin dueño y hay que decidir si es unidad |
| `adapters/odd-writing` | `writing-for-agents` (+ `writing-*`) | el entregable es un documento que consume un agente |

## Gates y artefactos

Cada paso deja artefacto y cada gate deja salida observada: `docs/gates.md`. Un gate no disponible se
reporta como no disponible con su motivo; un gate corrido y fallido se reporta fallando.

## Límites de este flujo

- No ejecuta cuerpos mutantes del upstream (`setup-*`, `scaffold-exercises`, `migrate-to-shoehorn`,
  `wizard`): se leen, no se corren (`docs/out-of-scope.md`).
- No crea un tool `Skill`, no instala dependencias, no usa red y no escribe configuración global: la
  activación es del dueño (`README.md`).
- No copia las reglas globales del dueño y no las duplica en otro archivo.
