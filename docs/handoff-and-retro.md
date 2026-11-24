# Handoff y retro

Complementan el `## Log` de la ficha. No lo reemplazan, no lo resumen en otro archivo de estado, no crean
un "estado actual" paralelo. Referencias upstream, solo lectura y en inglés:
`reference/skills/handoff/SKILL.md`, `reference/skills/retro/SKILL.md`.

## Handoff (cuando el dueño cambia de sesión o de agente)

Formato fijo, máximo una pantalla. Se escribe en la respuesta, o en temp si el dueño pide archivo: nunca
como estado persistente nuevo del repo.

```markdown
## Unidad
`odd/tasks/<slug>.md` — estado: <paso ODD actual>

## Hecho y verificado
<qué está cerrado, con el gate que lo prueba>

## Abierto
<cada decisión o tarea abierta, con el `S#` o la pregunta exacta>

## Siguiente acción
<el único próximo comando o pregunta>

## Rutas
<los archivos que el próximo va a tocar, con ruta exacta>
```

Reglas:

- Cada afirmación de "hecho" tiene su gate corrido; sin gate, es "escrito, sin verificar".
- Si el trabajo se cortó por un bloqueo humano, el handoff lo dice y qué decisión falta.
- No incluye secretos, credenciales ni contenido de archivos de entorno.

## Retro (solo a pedido del dueño)

La retro mejora el **entorno del agente**, no el código. Si aparece un defecto de código, no se arregla en
la retro: se convierte en ficha.

Cuatro preguntas, en este orden:

1. ¿Qué gate falló o faltó, y qué habría atrapado antes?
2. ¿Qué pregunta se hizo que el repo ya contestaba?
3. ¿Qué instrucción (`AGENTS.md`, adaptador, doc) generó una acción equivocada?
4. ¿Qué se dejó de hacer para poder preguntar?

Salida: hasta tres cambios concretos en las instrucciones (globales, del repo o de `adapters/odd-*`), cada
uno con su motivo y su condición de reversión. Si no hay cambios, se dice: una retro sin hallazgos es un
resultado.
