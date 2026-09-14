# Git — Deploy a `main`

El Deploy Lead **commitea y pushea a `main`** en:

1. Cada **repo de producto** (código).
2. El **home** InfoSails (`PROJECT_ROOT`: `harness.project.yaml` + `memory/`), si es un git propio.

La publicación a Vercel va **después** del push de producto: [vercel.md](vercel.md).

Rama destino: **`main`**. Si no existe, usar la default de `origin` (`git symbolic-ref refs/remotes/origin/HEAD`) y anotarlo en el DR.

No duplicar: si un producto tiene `path` igual al home, un solo ciclo git.

## Orden

1. Producto (en paralelo si hay varios gits).
2. Vercel producción ([vercel.md](vercel.md)).
3. Escribir el Deploy Report en `memory/deploys/` (SHAs + URLs).
4. Home (incluye ese DR y el resto de `memory/`).

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

## Home vs playground del kit

- Proyecto real (`infosails-init`): el home **sí** se pushea. Ahí está la memoria del producto.
- Playground (`playground/` dentro de `infosails-harness`): **no** pushear el git del kit. Documentar skip en el DR.

## Prohibido

- `git push --force` / `--force-with-lease` salvo pedido explícito del usuario
- `--no-verify` / `--no-gpg-sign` salvo pedido explícito
- Commitear `.env` o credenciales
- `git add -u` como único add (deja afuera archivos **nuevos**)
- Sustituir el push a `main` por solo `vercel --prod`
- `gcloud run deploy` como el publish default
- Omitir el home en un proyecto real
- Pushear el **kit** (`infosails-harness`) al hacer Deploy desde playground

## Push rechazado (branch protection)

Outbox `blocked` con el mensaje de git. No abrir PR a menos que el usuario lo pida: el contrato es **main**.
