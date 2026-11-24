---
name: odd-architecture
description: Recursos de arquitectura bajo demanda — vocabulario de módulos profundos y barrido de fricción estructural. Opt-in y fuera de cualquier unidad mutante. No refactoriza dentro de una unidad de negocio.
disable-model-invocation: true
---

# ODD + arquitectura (opt-in)

Adaptador de `pi-gentle-shell`. Referencias upstream, solo lectura y en inglés:
`reference/skills/codebase-design/SKILL.md` (vocabulario de módulos profundos) y
`reference/skills/improve-codebase-architecture/SKILL.md` (barrido y reporte). El upstream produce un
reporte HTML y después grilla; acá el barrido **reporta**, y grilla solo si el dueño decide convertirlo en
unidad.

## Cuándo

- El dueño pide explícitamente mirar la arquitectura, o una unidad quedó bloqueada por fricción
  estructural que no puede resolverse dentro de su alcance.
- Nunca se invoca en medio de una unidad de negocio para "aprovechar" y refactorizar.

## Trabajo

1. Vocabulario: profundidad (mucho comportamiento detrás de una interfaz chica), seam, superficie
   pública, módulo shallow. Se usa el idioma del repo consumidor, no uno nuevo.
2. Barrido: listar 3–5 oportunidades de deepening con su evidencia (archivo, síntoma, qué test se vuelve
   posible). Sin propuesta de código.
3. Propuesta al dueño como **unidad ODD aparte**: una por oportunidad, con su `S#` y su reversión.
4. Si no se aprueba, la oportunidad se anota en `## Log` de la ficha que la motivó, con destino explícito.

## Reglas

- Sin reporte HTML en el repo: el reporte va al `## Log` o a un archivo en temp. El checkout no acumula
  artefactos de análisis.
- Sin refactors preventivos, sin herramientas de análisis nuevas y sin tocar el gestor de dependencias
  (los `setup-*` del upstream son exclusión explícita: instalan cosas y reescriben estructura).
- Una oportunidad no aprobada no se implementa "de paso".

## Gate de salida

- Cada oportunidad tiene evidencia citable y una unidad propuesta, o se descartó con motivo escrito.
- `git status --short` no muestra cambios de código de producto producidos por este paso.
