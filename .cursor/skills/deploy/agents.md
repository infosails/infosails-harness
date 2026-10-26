# Agentes internos — Deploy

Solo el **Deploy Lead** los conoce. La Orquesta no.

Grafo: [graph.yaml](graph.yaml). Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).  
Git: [git.md](git.md). Vercel: [vercel.md](vercel.md). Crítico: [../_shared/critic.md](../_shared/critic.md).

| id | Rol | kind |
|----|-----|------|
| `surveyor` | Gits + targets Vercel; **instancia** committer, publisher:dev, tagger, publisher:prod | serial |
| `choose:targets` | Dev+prod o solo prod. La encarna el Lead, antes de git y de Vercel | human |
| `committer` | Commit + merge a `main` + push (`committer:<repo_id>`, luego `committer:home`) | parallel entre gits de producto |
| `publisher:dev` | `vercel deploy --yes` sin `--prod`, luego alias `-dev` (`publisher:dev:<app_id>`) | parallel por app |
| `approve:prod` | Autorización de prod. La encarna el Lead | human |
| `tagger` | Tag semver anotado + push (`tagger:<repo_id>`) | parallel por git de producto |
| `publisher:prod` | `vercel --prod --yes` (`publisher:prod:<app_id>`) | parallel por app |
| `gate` | SHA; URL dev; tag; URL prod; sin secretos; sin `--force` | gate |
| `critic` | SHA + tag + URLs o skip; sin secretos | gate |
| `scribe` | Deploy Report | serial |

## Flujo

`surveyor` instancia nodos y rewirea ([graph.md](../_shared/graph.md)):

```text
surveyor → choose:targets → [committer:<repo> ∥ …] → gate:git
  → [publisher:dev:<app> ∥ …] → gate:vercel:dev
  → approve:prod
  → [tagger:<repo> ∥ …] → gate:tag
  → [publisher:prod:<app> ∥ …] → gate:vercel:prod
  → critic → scribe → committer:home → gate:home
```

Sin Vercel: `choose:targets`, `gate:vercel:dev`, `approve:prod`, `gate:tag` y `gate:vercel:prod` en `skipped` (no publishers, no taggers, no preguntas). Home **después** del DR. Producto no toca `memory/`. El home no se taguea.

## choose:targets (el Lead)

1. `kind: human`. No lances un Task. Corre antes de los committers.
2. Seguir [SKILL.md](SKILL.md) § Ambientes.
3. Solo prod: `publisher:dev:*` y `gate:vercel:dev` quedan `skipped`. No es autorización de prod.

## approve:prod (el Lead)

1. `kind: human`. No lances un Task.
2. Seguir [SKILL.md](SKILL.md) § Autorización de prod.
3. Un sí de antes (el `start`) no cuenta. Tag y `vercel --prod` esperan el sí de esta pregunta.

## committer (subagente)

1. `id` = `committer:<repo_id>`; `writes` = path de ese git.
2. Seguir [git.md](git.md) § commit y push (no el tag: eso es `tagger`).
3. Producto: no tocar `memory/` ni `director-state.json`.
4. Home: sí `memory/` (incluido el DR); **no** editar `director-state.json`.
5. Devolver: SHA, rama, rol, commit nuevo, push, untracked agregados, error exacto.

## publisher:dev (subagente)

1. `id` = `publisher:dev:<app_id>`; `writes: []` (CLI Vercel, no git).
2. Seguir [vercel.md](vercel.md) § Fase dev. Primero `vercel deploy --yes` **sin** `--prod`. Después `vercel alias set` de esa URL al host de producción con `-dev` al final de la primera etiqueta.
3. No pushear git. No crear tags. No tocar `director-state.json`. No correr `vercel --prod`.
4. Devolver: URL cruda del deployment, alias `-dev`, exit code, error exacto.

## tagger (subagente)

1. `id` = `tagger:<repo_id>`; `writes` = path de ese git de **producto**.
2. Seguir [git.md](git.md) § Tag semver. El SHA es el de `origin/main` ya verificado. Si `approve:prod` no tuvo un sí, no crees el tag.
3. No tocar `memory/`, el home, ni `director-state.json`. No correr Vercel.
4. Devolver: tag `vX.Y.Z`, SHA, bump (`major`|`minor`|`patch`), si se creó o se reutilizó, push, error exacto.

## publisher:prod (subagente)

1. `id` = `publisher:prod:<app_id>`; `writes: []`.
2. Seguir [vercel.md](vercel.md) § Fase prod. Solo si hubo sí en `approve:prod` y el tag de ese repo ya está en `origin`.
3. No pushear git. No mover el tag. No tocar `director-state.json`.
4. Devolver: URL de producción, exit code, error exacto.
