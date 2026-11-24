# Decisiones de test y seams

Objetivo: que "¿probamos esto?" y "¿cómo lo reproduzco?" no sean preguntas. Lo que el repo consumidor ya
contestó se usa tal cual; lo que falta se decide con esta regla y se anota.

Referencia upstream, solo lectura y en inglés: `reference/skills/tdd/SKILL.md` (ciclo) y sus dependencias
`tests.md`, `mocking.md`.

## Runner: se verifica antes de citarlo

Antes de citar un comando de test se comprueba que su binario y su config existen en el repo (`ls`). Un
gate con un comando inexistente es un gate inventado. Si las dependencias no están instaladas, se reporta
`no disponible: dependencias no instaladas` y no se instala nada para forzarlo.

Formas honestas de citar el runner del repo consumidor (ejemplo genérico; se reemplaza por el del repo):

```bash
<runner> <ruta-o-patrón>     # un solo spec, el más chico que ejerce el cambio
<type-check> --noEmit        # type check del proyecto
<linter> <rutas>             # solo si es read-only; si escribe, no es gate
```

## Strict TDD: se enciende solo

| Situación | Decisión | Sin preguntar |
| --- | --- | --- |
| Existe seam (el comportamiento se ejerce por una interfaz pública) y existe runner | Strict TDD **ON**: RED observado antes de implementar, GREEN después | sí |
| Existe el spec del módulo y solo falta el caso nuevo | se agrega el caso al spec existente, RED/GREEN igual | sí |
| No hay seam (lógica enterrada en un método privado, dependencia de red no sustituible) | se declara la excepción con el motivo y se verifica con lo que sí existe (type check, e2e, salida observada) | sí, y se anota |
| El cambio es solo documentación/configuración | Strict TDD no aplica: se reporta `RED: not active` y los gates reales (JSON válido, rutas existentes, loader) | sí |

Un "test" que no puede fallar no es RED. Si al desenchufar el cambio el test sigue verde, no es
load-bearing y se dice.

## Evidencia que no prueba lo que dice probar

Tres formas de falso verde:

1. **Releer el estado no serializa dos operaciones.** Un test que ejecuta una operación y después vuelve a
   leer el estado prueba el efecto de *una* llamada, no la exclusión mutua entre dos. Si dos operaciones
   pueden competir por el mismo recurso, se prueban por separado, cada una con su aserción sobre el
   resultado del que llega segundo.

2. **Un test que asume exclusividad mutua entre dos estados** sin declararlo en el fixture falla por la
   razón equivocada, o pasa por accidente cuando el fixture no tiene el estado previo. El fixture declara
   qué estado previo existe.

3. **Un `expected` copiado de la salida observada no prueba la decisión.** Si el test afirma lo que el diff
   hace —porque el valor esperado se tomó de la corrida o de la implementación—, queda verde con la regla
   rota: prueba el código, no el `S#`. El `expected` se escribe desde el `S#`. La reseña Spec lo controla
   en la cadena (`docs/review-and-pr.md`).

Estas tres reglas son de **reseña**, no de feature: si un diff toca esas secuencias, se pide la evidencia
correcta antes de aceptar el gate.

## Diagnóstico reproducible

- Si hay un comando que reproduce (un spec rojo, una request exacta, un log), se corre tal cual y se pega
  la salida real. No se pregunta si se puede correr.
- Si no hay camino reproducible, el primer trabajo de la unidad es construirlo (test que falla o script en
  temp), no adivinar la causa.
- Los scripts de diagnóstico son desechables y viven en temp: nunca en el checkout.
