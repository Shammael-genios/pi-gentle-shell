# MANIFEST — inventario, procedencia e integridad

Estado de este árbol: **staging portable**, preparado para publicarse como repo `pi-gentle-shell`. No se
publicó: no hay `git init`, commit ni push (ver `README.md` §Estado).

## Raíz del bundle

| Ruta | Qué es |
| --- | --- |
| `ODD.md` | entrada operativa del flujo: dueño, default por fase, cinco omisiones, cadena de trazabilidad |
| `README.md` | qué es, publicar vs usar, activar, verificar, revertir, alcance |
| `GUIA-ACTIVACION.md` | pasos Windows PowerShell, bloque global, respaldo, verificación y reversión |
| `exports/activacion-global.md` | bloque de activación copiable, sin ruta absoluta |
| `MANIFEST.md` | este archivo: inventario, procedencia, integridad |
| `TRACEABILITY.md` | decisiones del bundle, cadena decisión → `S#` → regla → check, verificación observada |
| `package.json` | manifest Pi **opcional**: registra solo `adapters/` como skills manuales |
| `docs/**` | detalle portable del flujo (reseña/PR, tests, handoff/retro, fuera de alcance, glosario, gates) |
| `adapters/odd-*/SKILL.md` | 7 adaptadores manuales, `disable-model-invocation: true` |
| `reference/skills/**` | 17 skills upstream copiadas intactas (inglés), fuera del manifest |
| `reference/LICENSE-UPSTREAM` | licencia MIT del upstream, texto exacto del commit fijado |
| `reference/PROVENANCE.md` | fuente, commit, hashes y estado de licencia de las copias |

## Skills upstream copiadas (17)

Origen: `mattpocock/skills` @ `6bb33ef4dd4f12cec18a1a4b7f1446508e624bb2`, copiadas desde el árbol vendor
del repo de origen (`.agents/skills/<nombre>`) sin editar. `sha256` es el de la copia en este bundle: sirve
para detectar drift futuro del bundle, no como hash del upstream remoto.

| Skill | Archivos | `sha256(SKILL.md)` | Por qué está |
| --- | --- | --- | --- |
| `code-review` | 2 | `36847f010579f3bccd54c49793e1fd043f10cc49b4607bac5a3067522e528b91` | default de reseña |
| `codebase-design` | 4 | `362b0bec828219d9f4b08ca7c466164e67253bdd817359258e60d2e9c5692a8f` | implementación cuando interfaz/seam en duda |
| `diagnosing-bugs` | 3 | `aa87b0ac245c6d1b203c4678eb34dc101af02291448780c55c5e58ccc1619bd7` | default de diagnóstico |
| `domain-modeling` | 4 | `b62d997256cc073e123d4303da8e9db00db0e28de9739660ca3e55ab76e09cdb` | vocabulario dentro del grill |
| `handoff` | 2 | `739d8fbe3e6ed688581eb62b35f04834822eae47e57bc35a79c8d8dc9ac703d3` | default de cambio de sesión |
| `improve-codebase-architecture` | 3 | `ab526ca26246137fe96048b072d93aeff45f763da5e6c0eca5e51012dfd34433` | barrido de `adapters/odd-architecture` |
| `pr` | 3 | `57658baca2e50e9cf98fdb6b74a51f2afd34b71019d516f7c5d45a51a28354b0` | default de PR (solo cuerpo) |
| `prototype` | 4 | `f74ae01f4fc454d3a4ef21271920f1706e626271e715dda0559e489f45c792c3` | default de pregunta de diseño |
| `research` | 2 | `4422d07f1375712f0ecfadc05e6210bad2880f1d36eab32f62760eed344f935a` | default de hecho externo |
| `retro` | 2 | `42110f533f169026d037cf141b9f6c2752c6d914903be4758c38a68349e83e78` | retro a pedido del dueño |
| `tdd` | 4 | `969583080caf842ae86a53501a0091eced13fd89cad65d3aa75c09112318ff33` | default de implementación |
| `triage` | 4 | `797f4371a4cea1b564ecfdc7a603089acf3c023454e47ccb0b3307c62de279f8` | opt-in de `adapters/odd-triage` |
| `wayfinder` | 2 | `da3b9ce26c5128c8bd5b0099f262132320e72341544146196d81987edc62b58a` | trabajo mayor a una sesión |
| `writing-beats` | 2 | `b23f7851987dab7828c83d42df98090ccebff586b64908ce6a5e772440214621` | prosa larga de `adapters/odd-writing` |
| `writing-for-agents` | 3 | `1c0c4ebf2d221917591144f0ced4fe46dae13301cb3d6d93fc88ecf0dda6aed2` | default de documento para agentes |
| `writing-fragments` | 2 | `3be6ffe9386ddd6de8b320c331295953995792fd65d0972aa2a28524ead73e90` | prosa larga de `adapters/odd-writing` |
| `writing-shape` | 2 | `c13cbc364fbceefce654d9a4b0331113ff47efe90ce2523c2de23ecef63fefbd` | prosa larga de `adapters/odd-writing` |

Cada copia es el directorio completo del upstream: `SKILL.md` más sus dependencias (`agents/openai.yaml`,
`tests.md`, `mocking.md`, `UI.md`, `LOGIC.md`, `DEEPENING.md`, `DESIGN-IT-TWICE.md`, `SKILL-MECHANICS.md`,
`CREDITS.md`, `AGENT-BRIEF.md`, `OUT-OF-SCOPE.md`, `HTML-REPORT.md`, `ADR-FORMAT.md`,
`GLOSSARY-FORMAT.md`, `scripts/hitl-loop.template.sh`). Ningún archivo se renombró ni se tradujo.

## Skills del catálogo upstream que **no** viajan en este bundle

- `grilling`: motor del grill upstream. El grill del ciclo es `grill-me` (global, no se copia); ningún
  archivo del bundle cita `grilling`.
- `grill-me`: colisión de nombre con el grill global. No se copia y no se adapta.
- Las 21 restantes (cuerpos mutantes, rutas de orquestador, específicas de Claude Code, dependientes de
  tracker externo, `teach`, `to-questionnaire`, `wait-what`): fuera de alcance por `docs/out-of-scope.md`.
  No se copian, así que no hay nada que excluir del autodescubrimiento.

## Licencia de las copias

**Verificada.** `reference/LICENSE-UPSTREAM` es el texto MIT completo del upstream, obtenido del commit
fijado y comparado byte a byte contra esa fuente:

| Campo | Valor |
| --- | --- |
| URL de origen | `https://raw.githubusercontent.com/mattpocock/skills/6bb33ef4dd4f12cec18a1a4b7f1446508e624bb2/LICENSE` |
| `sha256` | `0e7ac423bf2c6e223b7c5b156f8cf72da49d748e56a1641402c31f22ad07dbb5` |
| Tamaño | 1068 bytes, 21 líneas, fin de línea LF |
| Titular | `Copyright (c) 2026 Matt Pocock` |
| Licencia | MIT |

Condición de redistribución de MIT cumplida por el bundle: el aviso de copyright y el permiso viajan con
las copias, en `reference/LICENSE-UPSTREAM`, sin editar. Si el upstream cambia de commit, se reemplaza el
par commit + licencia y se actualiza esta tabla.

Historial: la primera pasada de este staging no pudo demostrar la licencia (el árbol vendor de origen no
traía `LICENSE`). Quedó resuelto al consultar la fuente del commit fijado; ese estado anterior ya no
aplica.

## Integridad: cómo se comprueba

- Copias: `sha256` de la tabla anterior, más comparación byte a byte contra el árbol vendor del repo de
  origen (`reference/PROVENANCE.md` §Verificación): **48/48 archivos idénticos**.
- `skills-lock.json` del repo de origen: sus `computedHash` **no** se reprodujeron localmente como
  `sha256` del `SKILL.md` (ni con normalización de fin de línea ni con variantes de ruta); se usan como
  procedencia, no como checksum verificable acá. Límite reportado en `TRACEABILITY.md`.
- Rutas citadas: check C1 de `TRACEABILITY.md` (`32/32`).
- Licencia: check C14/C15 (`sha256` y titular registrados, texto completo del commit fijado).
- Ausencia de datos privados: check C3 (`0` coincidencias).
- Sin autodescubrimiento del upstream: check C5.
