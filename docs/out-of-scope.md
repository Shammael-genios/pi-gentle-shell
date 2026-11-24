# Fuera de alcance del flujo

Nada de esta lista se hace "de paso" porque un workflow upstream lo sugiera. Si aparece la tentación, gana
esta lista y el motivo queda escrito en el `## Log` de la ficha vigente.

## Superficies que este flujo no toca

- Configuración global del dueño: `~/.pi/agent/**`, `~/.agents/**`. La activación es un acto del dueño
  (`README.md`), no un paso del flujo.
- Trust de proyecto: solo lo otorga el dueño por proyecto. El flujo no lo cambia, no lo simula en disco y
  no lo autoaprueba.
- `reference/skills/**`: se copia intacto y se lee. No se edita, no se renombra, no se traduce.
- Dependencias, lockfiles, hooks de git, CI y publicación: el flujo no los muta.

## Cuerpos mutantes del upstream: referencia, no ejecución

| Skill upstream | Qué haría si se ejecutara | Disposición |
| --- | --- | --- |
| `setup-matt-pocock-skills` | reescribe `AGENTS.md`/`CLAUDE.md` y crea un tracker | solo lectura; la activación se escribe a mano |
| `setup-pre-commit` | instala hooks y lint-staged | exclusión: muta dependencias y hooks |
| `setup-ts-deep-modules` | instala herramientas y reescribe estructura de paquetes | exclusión |
| `scaffold-exercises` | crea directorios de ejercicios y commitea | exclusión |
| `migrate-to-shoehorn` | migra specs a una dependencia nueva | exclusión: deuda ajena |
| `wizard` | genera un script bash interactivo de infra | exclusión |
| `git-guardrails-claude-code` | instala hooks `PreToolUse` de Claude Code | exclusión: específica de Claude Code |
| `claude-handoff` | lanza un agente de fondo de Claude Code | exclusión: específica de Claude Code |
| `implement`, `implement-spec`, `to-spec`, `to-tickets` | operan sobre un issue tracker externo; `implement-spec` es un segundo orquestador | exclusión: el tracker es `odd/tasks/` y el orquestador es ODD |

Fuera de alcance también: rutas de orquestador del upstream (`ask-matt`, `chief-of-staff`,
`software-factory`, `loop-me`), agentes específicos de otro cliente (`.claude/agents/**`), y cualquier
tracker externo (`gh`, `glab`, labels, Linear, Jira, Notion).

## Trackers

- `odd/tasks/` es el tracker. `odd/pr-bodies/` son cuerpos de PR.
- Un tracker preexistente ajeno (por ejemplo `openspec/` en un repo que lo tenga) **no** se repurpose, no
  se edita y no se cita como fuente de verdad.

## Autodescubrimiento: apagado por construcción

- Los 17 workflows vigentes viven en `reference/skills/**`, que no es una ruta que Pi escanee
  (`~/.pi/agent/skills`, `.pi/skills`, `~/.agents/skills`, `.agents/skills`). No hay lista de exclusiones
  que mantener y no hay colisión de nombres con las skills globales.
- Los 7 adaptadores viven en `adapters/**`, fuera de las rutas convencionales, y declaran
  `disable-model-invocation: true`. Solo aparecen si el dueño instala el manifest opcional, y aun así no
  se autoseleccionan.
- No se crea un tool `Skill` falso ni un shim que lo simule. Los punteros upstream que dicen "Call the
  Skill tool" se traducen a lectura por ruta; no se invoca un tool inexistente.
