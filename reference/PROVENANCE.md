# PROVENANCE — skills upstream copiadas

## Fuente

| Campo | Valor |
| --- | --- |
| Repo | `mattpocock/skills` (`https://github.com/mattpocock/skills`) |
| Commit | `6bb33ef4dd4f12cec18a1a4b7f1446508e624bb2` |
| Tipo de fuente | `github` |
| Lock de origen | `skills-lock.json` del repo desde el que se copió el árbol vendor |
| Ruta vendor de origen | `.agents/skills/<nombre>/` |
| Fecha de copia | staging de `pi-gentle-shell` (ver `MANIFEST.md`) |

Las copias son **directorio completo**, sin edición, sin renombrar y sin traducir. Se copiaron con una
operación de copia de archivos; no se reescribió ningún `SKILL.md`.

## Qué se copió y qué no

- Copiadas (17): `code-review`, `codebase-design`, `diagnosing-bugs`, `domain-modeling`, `handoff`,
  `improve-codebase-architecture`, `pr`, `prototype`, `research`, `retro`, `tdd`, `triage`, `wayfinder`,
  `writing-beats`, `writing-for-agents`, `writing-fragments`, `writing-shape`.
- No copiadas: las 39 del catálogo completo menos esas 17. Motivos en `MANIFEST.md` y
  `docs/out-of-scope.md`. En particular `grill-me` no se copia porque colisiona con la skill global del
  mismo nombre, y `grilling` no se copia porque ningún archivo del bundle lo cita.

## Verificación de integridad

Observado al preparar el staging (repo de origen, probe desechable en temp):

- Copia byte a byte: `17/17` directorios copiados, `48` archivos incluidos sus dependencias, `0`
  diferencias de bytes contra el árbol vendor de origen.
- Licencia: `reference/LICENSE-UPSTREAM` idéntico byte a byte a la fuente del commit fijado, `sha256`
  `0e7ac423bf2c6e223b7c5b156f8cf72da49d748e56a1641402c31f22ad07dbb5`, `1068` bytes.
- `sha256` de cada `SKILL.md` registrado en `MANIFEST.md` (17 filas).
- **Limitación**: los `computedHash` de `skills-lock.json` no se reprodujeron localmente como `sha256` del
  `SKILL.md` (probado también con normalización CRLF/LF, concatenación de dependencias y variantes de
  ruta). Se usan como procedencia de fuente y commit, no como checksum verificable en este árbol. No se
  afirma que las copias coincidan con el remoto: se afirma que coinciden con el vendor del que se copiaron.

Comando reproducible de la comparación byte a byte (con el árbol vendor disponible):

```powershell
$a = 'C:\ruta\al\repo-de-origen\.agents\skills'
$b = 'C:\src\pi-gentle-shell\reference\skills'
Get-ChildItem $b -Directory | ForEach-Object {
  $n = $_.Name
  $hashA = Get-ChildItem "$a\$n" -Recurse -File | Sort-Object FullName | Get-FileHash -Algorithm SHA256
  $hashB = Get-ChildItem "$b\$n" -Recurse -File | Sort-Object FullName | Get-FileHash -Algorithm SHA256
  [pscustomobject]@{ Skill = $n; Iguales = (($hashA.Hash -join ',') -eq ($hashB.Hash -join ',')) }
}
```

## Licencia

**Verificada contra la fuente del commit fijado.** `reference/LICENSE-UPSTREAM` es el `LICENSE` del
upstream, copiado sin cambios:

```
https://raw.githubusercontent.com/mattpocock/skills/6bb33ef4dd4f12cec18a1a4b7f1446508e624bb2/LICENSE
```

| Campo | Valor |
| --- | --- |
| `sha256` | `0e7ac423bf2c6e223b7c5b156f8cf72da49d748e56a1641402c31f22ad07dbb5` |
| Tamaño | 1068 bytes, 21 líneas |
| Licencia | MIT |
| Titular | `Copyright (c) 2026 Matt Pocock` |

Verificación reproducible (Linux/macOS/Git Bash; en PowerShell el equivalente es
`(Get-FileHash .\reference\LICENSE-UPSTREAM -Algorithm SHA256).Hash.ToLower()`):

```bash
curl -fsSL https://raw.githubusercontent.com/mattpocock/skills/6bb33ef4dd4f12cec18a1a4b7f1446508e624bb2/LICENSE \
  | sha256sum   # 0e7ac423bf2c6e223b7c5b156f8cf72da49d748e56a1641402c31f22ad07dbb5
cmp <(curl -fsSL https://raw.githubusercontent.com/mattpocock/skills/6bb33ef4dd4f12cec18a1a4b7f1446508e624bb2/LICENSE) \
  reference/LICENSE-UPSTREAM && echo 'identico'
```

Condición MIT de redistribución: el aviso de copyright y el permiso se incluyen con todas las copias o
porciones sustanciales. El bundle los incluye en `reference/LICENSE-UPSTREAM`, que no se edita ni se omite
al copiar el bundle.

Historial (superseded): la primera pasada no pudo demostrar la licencia porque el árbol vendor de origen no
traía `LICENSE` ni `NOTICE`. Se resolvió consultando la fuente del commit fijado; el estado anterior ya no
aplica.

## Atribución que las copias ya traen

`reference/skills/pr/CREDITS.md` atribuye la sección "Summary" a Dex Horthy (`show-me`). Se conserva tal
cual, sin editar.
