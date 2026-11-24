# README — pi-gentle-shell

Bundle portable del flujo **ODD** con los workflows de **Matt Pocock** para Pi. Lleva las reglas del
proceso, los adaptadores manuales y las copias intactas de las 17 skills upstream que el flujo usa. No es
un proyecto de aplicación y no contiene código de negocio.

Repo destino: `https://github.com/shammael/pi-gentle-shell.git`

## Qué es y qué no es

- **Es** un árbol autocontenido: entrar por `ODD.md` alcanza para operar el flujo, y cada ruta citada
  existe dentro del bundle.
- **No es** un paquete de Pi que instale instrucciones globales. Un paquete de Pi aporta extensiones,
  skills, prompts y temas; el `AGENTS.md` global (`<agent-dir>\AGENTS.md`, por defecto
  `%USERPROFILE%\.pi\agent\AGENTS.md`) **lo escribe el dueño**, no el paquete.
- **No copia** las reglas globales que ya existen: `grill-me`, `security-audit` y el cierre de siete partes
  con audit de hash se usan como están y se citan, nunca se duplican.
- **No ejecuta** los cuerpos mutantes del upstream (`setup-*`, `scaffold-exercises`,
  `migrate-to-shoehorn`, `wizard`): se leen, no se corren (`docs/out-of-scope.md`).
- **No traduce** el upstream: `reference/skills/**` está en inglés y byte a byte igual al original.

## Contenido

| Ruta | Qué es |
| --- | --- |
| `ODD.md` | entrada operativa: dueño del ciclo, default por fase, cinco omisiones, cadena decisión → `S#` → comportamiento → evidencia |
| `GUIA-ACTIVACION.md` | guía paso a paso (Windows PowerShell): publicar, clonar, activar, verificar, revertir |
| `docs/` | reseña y PR, decisiones de test, handoff y retro, fuera de alcance, glosario, gates |
| `adapters/odd-*` | 7 adaptadores manuales con `disable-model-invocation: true` |
| `reference/skills/` | 17 skills upstream intactas, con sus dependencias |
| `reference/PROVENANCE.md` | fuente, commit, hashes y estado de licencia |
| `TRACEABILITY.md` | decisiones del bundle, cadena de trazabilidad y verificación observada |
| `MANIFEST.md` | inventario e integridad |
| `exports/activacion-global.md` | bloque de activación copiable |
| `package.json` | manifest Pi **opcional**: registra solo `adapters/` como skills manuales |

## Estado

Staging preparado y verificado en el árbol de origen, **sin publicar**: no hay `git init`, commit, push
ni release. Publicar requiere autorización explícita del dueño (`GUIA-ACTIVACION.md` §1).

## ¿Publicar o usar?

| Situación | Quién | Sección |
| --- | --- | --- |
| El repo `pi-gentle-shell` todavía no existe en GitHub: publicarlo por primera vez | dueño del repo, con autorización explícita de push | `GUIA-ACTIVACION.md` §1 |
| El repo ya está publicado: usarlo en una máquina | cualquiera, sin permisos sobre el repo | §Uso rápido de acá abajo |

Nada de este bundle se publica solo: el staging queda sin `git init` hasta que el dueño lo autorice.

## Uso rápido (una vez publicado el repo)

```powershell
# 1. Clonar donde quieras tener el bundle (ruta absoluta propia, sin espacios si querés evitar comillas)
git clone https://github.com/shammael/pi-gentle-shell.git C:\src\pi-gentle-shell

# 2. Respaldo privado del AGENTS global (fuera de cualquier repo; lo crea si no existe)
$agents = "$env:USERPROFILE\.pi\agent\AGENTS.md"
$backup = "$env:USERPROFILE\pi-gentle-shell-backup"
New-Item -ItemType Directory -Force $backup | Out-Null
New-Item -ItemType Directory -Force (Split-Path $agents) | Out-Null
if (Test-Path $agents) { Copy-Item $agents "$backup\AGENTS.md.$(Get-Date -Format 'yyyyMMdd-HHmmss').bak" } else { New-Item -ItemType File -Path $agents | Out-Null }

# 3. Agregar el bloque al FINAL del AGENTS global, sin sobreescribir
$bundle = 'C:\src\pi-gentle-shell'
$block  = (Get-Content -Raw "$bundle\exports\activacion-global.md").Replace('<RUTA_BUNDLE>', $bundle)
if (-not (Select-String -Path $agents -Pattern 'pi-gentle-shell' -Quiet)) { Add-Content -Path $agents -Value "`n$block" }

# 4. Verificar
Select-String -Path $agents -Pattern 'pi-gentle-shell'
Test-Path "$bundle\ODD.md"
```

Después de activar: `/reload` en Pi o reiniciar la sesión. El bloque es texto: Pi lo lee como instrucción
global, no como paquete.

Detalle completo, casos borde y reversión exacta: `GUIA-ACTIVACION.md`.

## Verificación mínima del bundle

```powershell
node -e "JSON.parse(require('fs').readFileSync('C:/src/pi-gentle-shell/package.json','utf8'))"
(Get-ChildItem 'C:\src\pi-gentle-shell\reference\skills' -Directory).Count   # 17
(Get-ChildItem 'C:\src\pi-gentle-shell\adapters' -Directory).Count            # 7
Test-Path 'C:\src\pi-gentle-shell\.agents\skills'                            # False: el upstream no se autodescubre
Test-Path 'C:\src\pi-gentle-shell\.pi\skills'                                # False
Test-Path 'C:\src\pi-gentle-shell\reference\LICENSE-UPSTREAM'                # True: licencia MIT del upstream
```

Resultados observados en el staging (detalle en `TRACEABILITY.md`): rutas de control **32/32**, hashes de
`SKILL.md` **17/17** registrados, copias byte a byte contra el vendor **48/48**, adaptadores **7/7**
completos, licencia MIT **verificada** (`sha256` registrado, 1068 bytes), coincidencias de datos privados
**0**, rutas de autodescubrimiento **0**.

## Desactivación

1. Abrir `%USERPROFILE%\.pi\agent\AGENTS.md` y borrar el bloque completo, desde la línea
   `## ODD portable (pi-gentle-shell)` hasta el final del bloque. Es el último bloque del archivo, así que
   borrar desde esa línea hasta el final es exacto.
2. Alternativa: restaurar el respaldo privado creado antes de activar.
3. Verificar: `Select-String -Path "$env:USERPROFILE\.pi\agent\AGENTS.md" -Pattern 'pi-gentle-shell' -Quiet`
   debe dar `False`. Después, `/reload`.
4. Borrar el clon si ya no se usa. Nada más quedó instalado: la activación no escribe configuración.

Si además se instaló el manifest opcional, retiralo con `pi remove C:\src\pi-gentle-shell` (o el comando
que reporte `pi list`).

## Manifest opcional (no necesario para el default)

`package.json` declara `pi.skills: ["./adapters"]`. Instalarlo registra los 7 adaptadores como comandos
manuales `/skill:odd-*` en **todos** los proyectos:

```powershell
pi install C:\src\pi-gentle-shell   # escribe la declaración en ~/.pi/agent/settings.json (acto del dueño)
pi list                              # para ver la entrada exacta antes de retirarla
```

Costo y límite: agrega una ruta local dependiente de la máquina al settings global y expone 7 nombres
`odd-*` en todos los proyectos. No aporta nada al default por fase, que es `read` de una ruta. Si no querés
esa superficie global, no lo instales: `ODD.md` y los adaptadores se leen por ruta igual.

## Alcance y límites

- No escribe configuración global por sí mismo, no cambia el trust de proyecto, no autoaprueba trust, no
  usa red y no hace `git init`, commit ni push.
- No hay tool `Skill` falso ni shim: los punteros upstream que lo invocan se traducen a lectura por ruta.
- Las 17 copias de `reference/skills/**` se distribuyen con `reference/LICENSE-UPSTREAM`: el texto MIT
  completo del commit fijado (`Copyright (c) 2026 Matt Pocock`), verificado por `sha256`
  `0e7ac423…` y acreditado en `reference/PROVENANCE.md`. Ese archivo no se edita ni se omite al copiar el
  bundle.
- El flujo asume que el repo consumidor usa ODD con tracker `odd/tasks/`. Si no, el flujo no aplica y se
  dice.
