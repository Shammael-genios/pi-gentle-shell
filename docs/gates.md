# Gates y artefactos del flujo

Un gate se declara verde solo con **salida observada**. Cada paso ODD deja un artefacto; cada gate deja un
comando exacto y su resultado real.

Los probes son desechables y viven en el temp del sistema (`/tmp`, `%TEMP%`), nunca en el checkout.

## Gates del flujo (los corre el repo consumidor)

| # | Gate | Comando exacto | Verde |
| --- | --- | --- | --- |
| G1 | El puntero de activación existe y apunta a un archivo real | `Test-Path <RUTA_BUNDLE>\ODD.md` (PowerShell) | `True` |
| G2 | `ODD.md` no cita rutas inexistentes del bundle | probe: parsear las rutas `reference/skills/**` y `docs/**` citadas y comprobar existencia | todas existen |
| G3 | Config/manifest parseable | `node -e "JSON.parse(require('fs').readFileSync('<RUTA_BUNDLE>/package.json','utf8'))"` | exit 0 |
| G4 | Adaptadores completos | probe: frontmatter con `name` = directorio, `description` no vacía, `disable-model-invocation: true` | 7/7 |
| G5 | Sin colisión con skills globales | `Get-ChildItem $env:USERPROFILE\.pi\agent\skills -Name` | ningún `odd-*` repetido; `grill-me` y `security-audit` presentes si existen |
| G6 | Upstream intacto | probe: comparar byte a byte `reference/skills/**` contra el origen vendor del repo que lo copió | idénticos |
| G7 | Superficie acotada | `git status --short` en el repo consumidor | solo rutas de la unidad; nada de configuración global |
| G8 | Default por fase aplicado / omisión justificada | walkthrough de la tabla de abajo | 9/9 como se espera |

Si el repo consumidor no está trustado, Pi no descubre skills de proyecto; el default de `ODD.md` funciona
igual porque es `read` de una ruta conocida. Los adaptadores son entrada manual y no dependen del default.

## Gates que el flujo NO puede correr

| Gate | Motivo | Qué se reporta |
| --- | --- | --- |
| Suite completa del repo | no hay cambio de producto que probar en este paso | `no disponible: sin cambio de código` |
| Carga real con trust de proyecto | la aprobación es del dueño | `no disponible: requiere aprobación del dueño` |
| Publicación del bundle | requiere autorización explícita del dueño | `no disponible: sin autorización de push` |

## Walkthrough del default (G8)

El default es una instrucción de texto, así que se verifica en dos partes: que cada ruta de la tabla de
`ODD.md` exista y sea el workflow que dice ser, y que las salidas representativas caigan donde la regla
manda.

| Escenario | Fase y hechos | Salida esperada |
| --- | --- | --- |
| D1 default aplicado | implementación, seam y runner presentes, interfaz en duda | carga `tdd` y `codebase-design`; artefacto RED/GREEN |
| D2 default aplicado en grill | vocabulario de la unidad en disputa | carga `domain-modeling` dentro de la ronda vigente; sin segunda entrevista |
| D3 omisión por criterio 1 | cambio trivial de una línea, ODD corto | no carga workflow: la fase no aplica |
| D4 omisión por criterio 2 | reseña pedida para un diff ya reseñado en la unidad | no carga `code-review`: la evidencia ya existe y está vigente |
| D5 adaptación por criterio 4 | PR sin autoridad de push | carga `pr` y omite solo el push; el cuerpo de PR se escribe igual |
| D6 adaptación por criterio 5 | reseña sin subagentes en paralelo | carga `code-review` y omite el reparto en subagentes; los dos ejes se hacen en una pasada |
| D7 contradicción con gate verde | el `S#` dice «el duplicado se rechaza»; el diff desempata; el test afirma el desempate porque el `expected` se copió de la implementación, y está verde | cadena rota en el paso 4: comportamiento ≠ `S#`; el verde no cuenta como gate y la contradicción vuelve al carril como **bloqueante** |
| D8 control conforme | mismo `S#`; el diff rechaza el duplicado; el test afirma el rechazo con `expected` derivado del `S#` | cadena completa `S#` → comportamiento → evidencia; el eje Spec pasa y el verde cuenta |
| D9 reinterpretación | el `S#` no cubre un duplicado con payload distinto; el diff elige desempatar sin regla que lo respalde | no se decide en silencio: `S#` nuevo (o deuda declarada); se pide decisión del dueño **solo** si la expectativa cambia |

## Artefactos por paso

| Paso ODD | Artefacto |
| --- | --- |
| decisión | `S#` en `odd/tasks/<unidad>.md` con procedencia y reversión |
| reglas/routing | `ODD.md`, `docs/**`, `adapters/odd-*` |
| verificación | salida observada de cada gate, con su número (N/N) |
| reseña | líneas en `## Log` con `S#`, severidad y destino |
| PR | `odd/pr-bodies/<unidad>/NN.md` con Antes/Después/Reversión/Verificación |
| cierre | reporte de siete partes + audit con hash (regla global del ciclo, no vive en este bundle) |

## Regla de honestidad

Un gate no disponible se reporta como no disponible con su motivo. Un gate que se corrió y falló se
reporta fallando. No se declara verde por inspección visual ni por "debería pasar".
