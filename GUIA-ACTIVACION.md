# Guía de activación y desactivación (Windows PowerShell)

Dos caminos distintos que no hay que mezclar:

- **Publicar por primera vez** — lo hace el dueño del repo `shammael/pi-gentle-shell`, una sola vez, desde
  el staging. Requiere autorización explícita de push.
- **Usar** — lo hace cualquiera que clone el repo ya publicado. No requiere permisos sobre el repo y no
  publica nada.

Ninguno de los dos pasos toca el checkout de otro proyecto, ni usa `git init` dentro de `~/.pi/agent`, ni
requiere credenciales embebidas.

---

## 0. Requisitos

| Requisito | Cómo se comprueba |
| --- | --- |
| Pi instalado y funcionando | `pi --version` |
| Git instalado | `git --version` |
| Node disponible para los checks de JSON | `node --version` |
| AGENTS global existente o creable | `Test-Path "$env:USERPROFILE\.pi\agent\AGENTS.md"` |
| Skills globales del ciclo presentes | `Get-ChildItem "$env:USERPROFILE\.pi\agent\skills" -Name` |

Si falta `grill-me` o `security-audit`, el flujo igual corre, pero esas fases quedan
`no disponible: regla global ausente`. El bundle no las reemplaza.

---

## 1. Publicar por primera vez (solo dueño, una vez)

Ruta de trabajo: el **árbol del bundle**, es decir el staging que ya está verificado. No es
`~/.pi/agent` y no es el checkout de ningún proyecto de negocio.

```powershell
# Ubicarse en la raíz del bundle (donde están README.md y ODD.md)
cd <RUTA_DEL_STAGING>

# La licencia upstream viaja en el bundle: es comprobación, no acción pendiente
Test-Path .\reference\LICENSE-UPSTREAM          # True
(Get-FileHash .\reference\LICENSE-UPSTREAM -Algorithm SHA256).Hash.ToLower()
# 0e7ac423bf2c6e223b7c5b156f8cf72da49d748e56a1641402c31f22ad07dbb5

# Inicializar SOLO acá, nunca en %USERPROFILE%\.pi\agent
git init
git add .
git commit -m "pi-gentle-shell: flujo ODD portable con workflows de Matt Pocock"
git branch -M main
git remote add origin https://github.com/shammael/pi-gentle-shell.git
git push -u origin main
```

Reglas duras:

- `git init` **solo** en la raíz del bundle. `~/.pi/agent` es configuración del usuario: no se versiona y no
  se sube.
- No se publica contenido de ningún checkout de negocio: lo que va al remoto es exactamente el inventario
  de `MANIFEST.md`.
- Sin credenciales en el remoto, en el commit ni en ningún archivo: el push usa la autenticación que ya
  tenga el dueño.

---

## 2. Usar el bundle (una vez publicado)

```powershell
# Clonar en una ruta propia, estable y absoluta
git clone https://github.com/shammael/pi-gentle-shell.git C:\src\pi-gentle-shell

# Confirmar que el clon está completo
Get-ChildItem C:\src\pi-gentle-shell -Name
Test-Path C:\src\pi-gentle-shell\ODD.md                                  # True
(Get-ChildItem C:\src\pi-gentle-shell\reference\skills -Directory).Count # 17
(Get-ChildItem C:\src\pi-gentle-shell\adapters -Directory).Count         # 7
```

El clon **no activa nada por sí solo**: no escribe configuración global, no registra skills y no modifica
el repo donde trabajás.

---

## 3. Activar: bloque pequeño en el AGENTS global

La activación es una sola edición manual del archivo de instrucciones globales de Pi. La ruta canónica es
`<agent-dir>\AGENTS.md`, por defecto `%USERPROFILE%\.pi\agent\AGENTS.md`.

Un paquete de Pi **no** agrega instrucciones globales: aporta extensiones, skills, prompts y temas. Por eso
la activación es este bloque y no `pi install`.

### 3.1 Respaldo privado

```powershell
$agents  = "$env:USERPROFILE\.pi\agent\AGENTS.md"
$backup  = "$env:USERPROFILE\pi-gentle-shell-backup"
New-Item -ItemType Directory -Force $backup | Out-Null
if (Test-Path $agents) {
  Copy-Item $agents "$backup\AGENTS.md.$(Get-Date -Format 'yyyyMMdd-HHmmss').bak"
} else {
  New-Item -ItemType Directory -Force (Split-Path $agents) | Out-Null
  New-Item -ItemType File -Path $agents | Out-Null
}
Get-ChildItem $backup -Name    # debe listar el .bak
```

El respaldo vive fuera de cualquier repo: es privado, no se versiona y no se publica.

### 3.2 Copiar el bloque (sin sobreescribir)

El bloque está en `exports\activacion-global.md` con el placeholder `<RUTA_BUNDLE>`. Este comando lo
agrega **al final** del archivo, con la ruta absoluta ya resuelta, y no hace nada si el bloque ya está:

```powershell
$bundle = 'C:\src\pi-gentle-shell'
$agents = "$env:USERPROFILE\.pi\agent\AGENTS.md"
$block  = (Get-Content -Raw "$bundle\exports\activacion-global.md").Replace('<RUTA_BUNDLE>', $bundle)
if (-not (Select-String -Path $agents -Pattern 'pi-gentle-shell' -Quiet)) {
  Add-Content -Path $agents -Value "`n$block"
} else {
  'Bloque ya presente: no se duplica.'
}
```

Si preferís pegar a mano: abrir el archivo en el editor, ir al final y pegar el bloque. No usar `>`, que
trunca el archivo y borra las reglas globales existentes.

### 3.3 Recargar

`/reload` en Pi, o reiniciar la sesión. El bloque es texto de instrucciones: se aplica a cualquier
directorio en el que corras Pi, sin trust de proyecto.

---

## 4. Verificación

```powershell
$bundle = 'C:\src\pi-gentle-shell'
$agents = "$env:USERPROFILE\.pi\agent\AGENTS.md"

# 1. El bloque está y apunta a una ruta que existe
Select-String -Path $agents -Pattern 'pi-gentle-shell'
Test-Path "$bundle\ODD.md"                                              # True

# 2. Rutas de control del bundle
Test-Path "$bundle\reference\skills\tdd\SKILL.md"                       # True
Test-Path "$bundle\reference\skills\pr\SKILL.md"                        # True
Test-Path "$bundle\exports\activacion-global.md"                        # True

# 3. Inventario
(Get-ChildItem "$bundle\reference\skills" -Directory).Count             # 17
(Get-ChildItem "$bundle\adapters" -Directory).Count                     # 7

# 4. Manifest parseable
node -e "JSON.parse(require('fs').readFileSync('C:/src/pi-gentle-shell/package.json','utf8'))"

# 5. El upstream no se autodescubre: no hay rutas que Pi escanee dentro del bundle
Test-Path "$bundle\.agents\skills"                                      # False
Test-Path "$bundle\.pi\skills"                                          # False

# 6. El manifest declara solo adapters (nunca reference)
node -e "const p=require('C:/src/pi-gentle-shell/package.json'); console.log(p.pi.skills.join(','))"  # ./adapters

# 7. Sin colisión con las skills globales
Get-ChildItem "$env:USERPROFILE\.pi\agent\skills" -Name                 # grill-me / security-audit; ningún odd-*
```

Si querés comparar las copias contra el vendor de origen, el comando de hash está en
`reference\PROVENANCE.md` §Verificación.

---

## 5. Desactivación y reversión

### 5.1 Reversión preferida: restaurar el respaldo

```powershell
$backup  = "$env:USERPROFILE\pi-gentle-shell-backup"
$agents  = "$env:USERPROFILE\.pi\agent\AGENTS.md"
$last    = Get-ChildItem $backup -Filter 'AGENTS.md.*.bak' | Sort-Object LastWriteTime | Select-Object -Last 1
Copy-Item $last.FullName $agents -Force
Select-String -Path $agents -Pattern 'pi-gentle-shell' -Quiet            # False
```

### 5.2 Reversión manual: borrar solo el bloque

El bloque se agrega al final del archivo. Abrir `%USERPROFILE%\.pi\agent\AGENTS.md`, seleccionar desde la
línea `## ODD portable (pi-gentle-shell)` hasta el final del bloque y borrar. No tocar el resto del
archivo.

```powershell
Select-String -Path "$env:USERPROFILE\.pi\agent\AGENTS.md" -Pattern 'pi-gentle-shell' -Quiet  # False
```

### 5.3 Cerrar la activación

```powershell
# Si instalaste el manifest opcional, retiralo (mirá primero la entrada exacta)
pi list
pi remove C:\src\pi-gentle-shell

# Borrar el clon
Remove-Item C:\src\pi-gentle-shell -Recurse -Force
Remove-Item "$env:USERPROFILE\pi-gentle-shell-backup" -Recurse -Force   # opcional: el respaldo ya no hace falta

# Recargar Pi
/reload
```

Después de revertir, `Get-ChildItem "$env:USERPROFILE\.pi\agent\skills" -Name` no muestra ningún `odd-*` y
el `AGENTS.md` global vuelve a su contenido previo. Nada más quedó escrito por el bundle.

---

## 6. Manifest opcional (no es el camino de activación)

Solo si querés `/skill:odd-*` como comandos manuales en todos los proyectos:

```powershell
pi install C:\src\pi-gentle-shell
pi list
```

Qué hace: escribe una declaración de paquete local en `%USERPROFILE%\.pi\agent\settings.json` y registra
los 7 adaptadores de `adapters/`. Qué no hace: no agrega instrucciones globales, no registra
`reference/skills/**` y no cambia el trust de proyecto.

Costo: ruta local dependiente de la máquina en el settings global y 7 nombres visibles en todos los
proyectos. Los adaptadores tienen `disable-model-invocation: true`, así que no se autoseleccionan.

---

## 7. Problemas típicos

| Síntoma | Causa probable | Qué hacer |
| --- | --- | --- |
| Pi no aplica el flujo | bloque ausente, ruta relativa o `/reload` pendiente | pegar el bloque con ruta absoluta y recargar |
| El bloque quedó duplicado | se pegó dos veces | dejar una sola copia; el comando de §3.2 no duplica |
| Aparecen skills de Matt en el autodescubrimiento | se copiaron a `.pi/skills`, `.agents/skills` o `~/.pi/agent/skills` | sacarlas de esas rutas; el bundle usa `reference/skills/**`, que Pi no escanea |
| `/skill:odd-*` no existe | no se instaló el manifest opcional | instalarlo (§6) o leer el adaptador por ruta |
| Colisión con `grill-me` | se instaló el `grill-me` de Matt como skill | quitarlo: el grill del ciclo es el global, el bundle no lo copia |
| El flujo pide `odd/tasks/` y el repo no lo tiene | el repo consumidor no usa ODD | no aplica: se dice y se trabaja como el repo mande |

---

## 8. Qué no hacer

- No `git init` en `%USERPROFILE%\.pi\agent` ni en `~/.pi/agent`: es configuración del usuario, no un repo.
- No publicar desde el checkout de un proyecto de negocio: el remoto `pi-gentle-shell` recibe solo el
  bundle verificado.
- No sobreescribir `AGENTS.md` global con `>` o `Set-Content`: se pierden las reglas existentes.
- No copiar `reference/skills/**` a una ruta que Pi descubra: rompe la garantía de "el upstream no se
  autodescubre" y puede colisionar con el grill global.
- No editar ni traducir `reference/skills/**`: es upstream y se cita por ruta.
- No agregar secretos, tokens ni rutas personales al bundle ni al bloque de activación.
