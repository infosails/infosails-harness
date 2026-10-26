# Git — Deploy a `main`

El Deploy Lead **commitea y pushea a `main`** en:

1. Cada **repo de producto** (código).
2. El **home** InfoSails (`PROJECT_ROOT`: `harness.project.yaml` + `memory/`), si es un git propio.

Antes de este push, el usuario elige dev+prod o solo prod. Después van dev (solo si eligió ambos), la autorización de prod y, solo con un sí, el tag y prod ([vercel.md](vercel.md)). El tag lo crea `tagger`, no `committer`.

Rama destino: **`main`**. Si no existe, usar la default de `origin` (`git symbolic-ref refs/remotes/origin/HEAD`) y anotarlo en el DR.

No duplicar: si un producto tiene `path` igual al home, un solo ciclo git.

## Orden

1. **Ambientes:** dev y prod, o solo prod. Nada de git ni Vercel antes de esa respuesta.
2. Producto: commit + push a `main` (en paralelo si hay varios gits).
3. **Fase dev:** solo si eligieron ambos. `vercel deploy --yes` sin `--prod` ([vercel.md](vercel.md)).
4. **Autorización** de prod. Sin un sí explícito, no hay tag. “Solo prod” no es ese sí.
5. **Fase prod:** tag semver en ese SHA + push del tag; después `vercel --prod --yes`.
6. Escribir el Deploy Report en `memory/deploys/` (SHAs + tag + URLs).
7. Home (incluye ese DR y el resto de `memory/`). El home no lleva tag de release.

## Por cada git (producto o home)

`cd` al path del git.

1. `git status -u` y `git fetch origin` (si hay remote). Anotar **modified, staged y untracked**.
2. Si hay cambios (incluidos **archivos nuevos** sin trackear):
   - **Incluir** untracked de código, tests, configs de app, `vercel.json`, `.vercel/project.json` si ya se versiona, lockfiles, y lo que Build dejó en el working tree.
   - No alcanza `git add -u` (solo toca archivos ya trackeados). Usar `git add -A` (o `git add .`) para que entren **también los nuevos**, respetando `.gitignore`.
   - Después revisar `git status`: si se coló algo prohibido, `git restore --staged` de esos paths.
   - **No agregar:** `.env`, `.env.*` (sí `.env.example`), secretos, `node_modules/`, `.next/`, `dist/`, artefactos de build, `.vercel/*.log`.
   - **Producto:** no agregar `memory/` de otro git.
   - **Home:** `memory/` (blueprints, designs, builds, deploys, landscape, backlog, procesos), `harness.project.yaml`, `AGENTS.md` y lo demás del home. No `.env`.
   - `git commit` con mensaje que cite `BR-…` / `BP-…`.
3. Si no estás en `main`:
   - `git checkout main` (crear desde origin si hace falta).
   - `git merge` de la rama de trabajo (no rebase destructivo; no `--force`).
4. `git push origin main`.
5. Anotar SHA: `git rev-parse HEAD` y confirmar que `origin/main` lo apunta.
6. Tras el push, `git status -u`: no deben quedar untracked de producto (salvo ignorados a propósito). Si quedan → no `complete`; volver a add/commit/push o `blocked`.

Si ya está en `main` y no hay nada que commitear ni pushear (tampoco untracked de producto) → anotar el SHA actual.

## Tag semver (fase prod)

Solo repos de **producto**, solo si la fase dev quedó verde y el usuario autorizó prod en `approve:prod`. Un tag por git, no por app. El home no se taguea. Sin target Vercel, o con un no: no hay tag.

El SHA es el de `origin/main` que ya pasó `gate:git`.

### Qué tag

Forma estricta: `vMAJOR.MINOR.PATCH` (semver, sin pre-release ni metadata). Ejemplo: `v1.4.2`.

1. `git tag --merged HEAD --list 'v*'`.
2. Quedarte con los que cumplen `^v(0|[1-9][0-9]*)\.(0|[1-9][0-9]*)\.(0|[1-9][0-9]*)$`.
3. El mayor es numérico (major, luego minor, luego patch). `v1.10.0` es mayor que `v1.9.0`.
4. Bump, desde `semver_bump` del inbox o el pedido del usuario. Si no viene: **patch**.
   - Hay tag `vX.Y.Z`: `major` → `v(X+1).0.0`; `minor` → `vX.(Y+1).0`; `patch` → `vX.Y.(Z+1)`.
   - No hay tag: `major` → `v1.0.0`; `minor` o `patch` → `v0.1.0`.
5. Si **ese SHA** ya tiene un tag de esa forma: no crees otro. Si falta en `origin`, pushea ese tag (sin `--force`) y sigue. Si hay varios, usa el mayor.

### Crear y pushear

```bash
git tag -a vX.Y.Z -m "Release vX.Y.Z (BR-…)" <sha>
git push origin refs/tags/vX.Y.Z
```

Comprobar:

- `git rev-parse vX.Y.Z^{}` es el SHA de `origin/main`.
- `git ls-remote --tags origin refs/tags/vX.Y.Z` muestra el tag. En un tag anotado, el peeled (`refs/tags/vX.Y.Z^{}`) es el commit.

Si el nombre ya existe y apunta a **otro** SHA → `blocked`. No uses `--force` ni borres el tag anterior.

El tag tiene que estar en `origin` **antes** de `vercel --prod`.

## Home vs playground del kit

- Proyecto real (`infosails-init`): el home **sí** se pushea. Ahí está la memoria del producto.
- Playground (`playground/` dentro de `infosails-harness`): **no** pushear el git del kit. Documentar skip en el DR.

## Prohibido

- `git push --force` / `--force-with-lease` salvo pedido explícito del usuario
- `--no-verify` / `--no-gpg-sign` salvo pedido explícito
- Commitear `.env` o credenciales
- `git add -u` como único add (deja afuera archivos **nuevos**)
- Sustituir el push a `main` por solo Vercel
- `git tag` / `git push` de tags con `--force`
- Mover o borrar un tag que ya apunta a otro SHA
- Taguear el home, o taguear si no hubo fase dev
- Correr `vercel --prod` antes de que el tag esté en `origin`
- `gcloud run deploy` como el publish default
- Omitir el home en un proyecto real
- Pushear el **kit** (`infosails-harness`) al hacer Deploy desde playground

## Push rechazado (branch protection)

Outbox `blocked` con el mensaje de git. No abrir PR a menos que el usuario lo pida: el contrato es **main**.
