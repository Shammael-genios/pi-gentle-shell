# TRACEABILITY — decisión → `S#` → regla → comportamiento → check

Este archivo cierra la cadena de trazabilidad del bundle: cada regla del flujo tiene una decisión que la
origina, un lugar exacto donde vive, un comportamiento esperado y un check que lo comprueba. Un cambio sin
esta cadena es deuda, no una mejora.

## Cómo se lee la cadena

1. **Decisión** — `B#` de la tabla de abajo (procedencia: dueño o agente) o un `S#` del ciclo ODD del repo
   consumidor.
2. **`S#` / `B#`** — la decisión trasladada sin cambiar su semántica, con su condición de reversión.
3. **Regla** — el archivo y la sección del bundle donde la decisión se hace operativa.
4. **Comportamiento esperado** — lo que un lector del bundle va a hacer distinto.
5. **Check** — el check ejecutable que lo comprueba, con su `expected` derivado de la decisión, no de la
   salida observada.

## Decisiones de este bundle

| `B#` | Decisión | Procedencia | Condición de reversión | Regla en el bundle |
| --- | --- | --- | --- | --- |
| B1 | El bundle se publica como repo `shammael/pi-gentle-shell`, con ese título | dueño | si el dueño elige otro nombre o destino | `README.md` (título), `package.json` (`name`) |
| B2 | La activación es un bloque pequeño al **final** del `AGENTS.md` global, con ruta absoluta y sin sobreescribir | dueño | si Pi deja de leer `<agent-dir>/AGENTS.md` como instrucciones globales | `exports/activacion-global.md`, `GUIA-ACTIVACION.md` §3 |
| B3 | El manifest Pi es **opcional** y nunca es el camino de activación: un paquete no agrega instrucciones globales | agente | si Pi agrega instrucciones globales desde paquetes | `package.json`, `README.md` §Manifest opcional |
| B4 | `reference/skills/**` vive fuera del manifest y fuera de toda ruta que Pi escanee | agente | si Pi empieza a escanear directorios arbitrarios del working directory | `MANIFEST.md`, `docs/out-of-scope.md` §Autodescubrimiento |
| B5 | Las reglas globales del dueño (`grill-me`, `security-audit`, cierre de siete partes + audit con hash) se citan, no se copian ni se duplican | dueño | si el dueño pide versionarlas dentro del bundle | `ODD.md` §Precondiciones, `docs/glossary.md` |
| B6 | El upstream se conserva intacto y **en inglés**; no se traduce ni se edita | dueño | si aparece una traducción oficial del upstream | `MANIFEST.md`, `reference/PROVENANCE.md`, `README.md` §Alcance |
| B7 | Viajan 17 skills: exactamente las citadas por el default por fase y por los adaptadores. `grill-me` y `grilling` no viajan | agente | si el flujo cita `grilling` o el dueño pide el catálogo completo | `MANIFEST.md` §Skills copiadas, `reference/PROVENANCE.md` |
| B8 | No se inventa licencia: se registra fuente, commit y hash, se verifica la copia byte a byte y se incluye el `LICENSE` MIT exacto del commit fijado en `reference/LICENSE-UPSTREAM` | agente | si el upstream cambia de commit o de licencia: se reemplaza el par commit + licencia y se actualiza la tabla | `reference/PROVENANCE.md` §Licencia, `MANIFEST.md` §Licencia, `README.md` §Alcance |
| B9 | Los ejemplos usan placeholders (`<RUTA_BUNDLE>`, `C:\src\pi-gentle-shell`): cero rutas personales, cero credenciales | dueño | si el dueño pide ejemplos con su ruta real (sería configuración de máquina: no) | `README.md`, `GUIA-ACTIVACION.md`, `exports/activacion-global.md` |
| B10 | El staging no inicializa git, no commitea y no publica: la publicación es un acto separado y autorizado | dueño | con autorización explícita de push del dueño | `README.md` §Estado, `GUIA-ACTIVACION.md` §1 |
| B11 | La documentación del flujo va en español; las copias upstream quedan en inglés | dueño | si el dueño pide el flujo en otro idioma | todo el bundle salvo `reference/skills/**` |

## Trazabilidad regla por regla

| Regla (dónde vive) | Decisión | Comportamiento esperado | Check |
| --- | --- | --- | --- |
| `ODD.md` §Default por fase | B7 | en cada fase ODD se lee la ruta `reference/skills/<n>/SKILL.md` y se sigue su secuencia, sin slash y sin justificar valor | C13 |
| `ODD.md` §Cuándo se omite | B5, B7 | se omite solo por los cinco motivos, se dice cuál, y por (4)/(5) se adapta la parte portable antes de omitir | C11 |
| `ODD.md` §Trazabilidad | B5 | el `expected` sale del `S#`; una contradicción con gate verde vuelve al carril como bloqueante; la reinterpretación no se decide en silencio | C12, C12b |
| `adapters/odd-*` | B3, B5 | los 7 adaptadores son entrada manual (`disable-model-invocation: true`), completos y sin referencias a rutas de un checkout concreto | C5 |
| `docs/review-and-pr.md` | B5 | reseña de dos ejes en una pasada y PR con Antes/Después/Reversión/Verificación | C1 |
| `docs/testing-decisions.md` | B5 | Strict TDD se enciende con seam y runner; las tres formas de falso verde se controlan en reseña | C1 |
| `docs/handoff-and-retro.md` | B5 | handoff de una pantalla y retro solo a pedido del dueño | C1 |
| `docs/gates.md` | B5 | ningún gate se declara verde sin salida observada; lo no disponible se reporta con motivo | C1, C8 |
| `docs/out-of-scope.md` | B4, B5 | no se ejecutan cuerpos mutantes ni se toca configuración global, trust o trackers externos | C4, C6 |
| `docs/glossary.md` | B5 | el vocabulario se usa con una sola lectura por término | C1 |
| `package.json` | B3, B4 | el manifest declara solo `adapters/`; nunca `reference/` | C4b, C4c |
| `README.md` | B1, B2, B3, B9, B10 | distingue publicar por primera vez de usar/clonar; activa, verifica y revierte con PowerShell sin sobreescribir | C8 |
| `GUIA-ACTIVACION.md` | B2, B9, B10 | pasos exactos, respaldo privado, reversión por respaldo o borrado del bloque, nunca `git init` en `<agent-dir>` | C8 |
| `exports/activacion-global.md` | B2, B5 | bloque copiable sin ruta absoluta (placeholder) que cita las reglas globales sin redefinirlas | C1, C6b |
| `reference/PROVENANCE.md` | B6, B7, B8 | procedencia verificable y licencia MIT acreditada con el texto exacto del commit | C2, C14, C15 |

## Checks ejecutables

Probe desechable (temp, read-only) sobre el bundle, con `expected` derivado de las decisiones de arriba:

| Check | Qué afirma | `expected` |
| --- | --- | --- |
| C1 | toda ruta del bundle citada en backticks existe | 100% de las rutas citadas |
| C2 | el `sha256` de cada `SKILL.md` copiado está registrado en `MANIFEST.md` | 17/17 |
| C2b | las dependencias del upstream viajaron | más archivos que skills |
| C3 | sin rutas personales, credenciales, cookies ni datos del repo de origen | 0 coincidencias en archivos del bundle |
| C4 | ninguna copia vive en una ruta que Pi descubra; el bundle no tiene `.pi/` ni `.agents/` | 0 rutas |
| C4b | el manifest declara solo `./adapters` | `["./adapters"]` |
| C4c | el manifest no declara `reference` | sin coincidencias en `pi` |
| C5 | 7 adaptadores completos: `name` = directorio, `description` no vacía, `disable-model-invocation: true`, sin referencias antiguas | 7/7 |
| C6 | `grill-me` y `security-audit` no están copiadas | 0 archivos |
| C6b | el cierre global se cita sin redefinirse | a lo sumo 2 archivos lo mencionan |
| C7 | ninguna ruta de workflow apunta al vendor del origen ni a un checkout concreto | 0 rutas de workflow |
| C8 | el README cubre publicar/clonar/activar/verificar/desactivar/respaldo/manifest | 7/7 secciones |
| C9 | el default por fase no depende del manifest | `read` de ruta + manifest opcional |
| C10 | sin binarios ni archivos inesperados | solo `.md`, `.json`, `.yaml`, `.sh` |
| C10b | sin archivos de entorno ni cookies | 0 |
| C11 | cinco motivos de omisión | 5 |
| C12 | cadena de cuatro pasos presente | 4/4 |
| C12b | «aunque esté verde» + «bloqueante» presentes | ambos |
| C13 | las rutas del default por fase existen | 12/12 únicas |
| C14 | `reference/LICENSE-UPSTREAM` es el texto MIT completo del commit fijado | empieza en `MIT License`, contiene `Copyright (c) 2026 Matt Pocock`, cierra en `SOFTWARE.` |
| C15 | la licencia queda acreditada | `sha256` y commit registrados en `PROVENANCE.md` y `MANIFEST.md` |

Equivalente manual en Windows PowerShell: `GUIA-ACTIVACION.md` §4.

## Verificación observada en este staging

Probe desechable en temp (Node 26, read-only sobre el bundle), resultado **21/21 OK, 0 FAIL**:

| Check | Resultado observado |
| --- | --- |
| C1 rutas citadas | 32/32 existen |
| C2 hashes registrados | 17/17 |
| C2b dependencias | 48 archivos para 17 skills |
| C3 datos privados | 0 coincidencias en 70 archivos |
| C4/C4b/C4c autodescubrimiento | 0 rutas; manifest `["./adapters"]`; sin `reference` |
| C5 adaptadores | 7/7 |
| C6/C6b reglas globales | 0 copias; 2 menciones del cierre sin redefinirlo |
| C7 rutas de workflow | 0 rutas hacia el vendor del origen o a un checkout concreto |
| C8 README | 7/7 secciones |
| C9 default independiente del manifest | OK |
| C10/C10b tipos de archivo | `.md`, `.json`, `.yaml`, `.sh`; sin archivos de entorno |
| C11 omisiones | 5 |
| C12/C12b cadena | 4/4 pasos; «aunque esté verde» y «bloqueante» |
| C13 rutas de fase | 12/12 |
| C14 licencia upstream | presente y completa: `sha256 0e7ac423…`, 1068 bytes |
| C15 licencia acreditada | `sha256` + commit en `PROVENANCE.md` y `MANIFEST.md` |

Comparaciones adicionales fuera del probe: copias byte a byte contra el vendor **48/48 sin diferencias**;
`reference/LICENSE-UPSTREAM` byte a byte contra el `LICENSE` del commit fijado (1068 bytes, `sha256`
`0e7ac423bf2c6e223b7c5b156f8cf72da49d748e56a1641402c31f22ad07dbb5`).

## Límites de esta verificación

- **Licencia verificada contra una sola vía**: el `LICENSE` se obtuvo del `raw.githubusercontent.com` del
  commit fijado (`6bb33ef4…`) y se comparó byte a byte. No se contrastó contra un mirror ni contra una copia
  local del upstream, porque no existe en el árbol de origen.
- **`computedHash` de `skills-lock.json` no reproducible** localmente como `sha256` del `SKILL.md`. La
  integridad se afirma contra el vendor del que se copió (byte a byte, 48/48 archivos), no contra el remoto.
- **Trust y descubrimiento reales no ejercitados**: no se cambió ni se simuló el trust de Pi. La garantía
  de "el upstream no se autodescubre" es estructural (las copias no están en ninguna ruta que Pi escanee)
  y se comprueba por C4.
- **Los comandos PowerShell de `GUIA-ACTIVACION.md` no se ejecutaron** en este staging: se documentan, no
  se corren. Los checks reportados arriba se corrieron con Node sobre el árbol.
- **Publicación no ejecutada**: sin `git init`, commit ni push. Estado y autorización en
  `GUIA-ACTIVACION.md` §1.
