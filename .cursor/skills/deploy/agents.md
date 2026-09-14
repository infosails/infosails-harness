# Agentes internos — Deploy

Solo el **Deploy Lead** los conoce. La Orquesta no.

Grafo: [graph.yaml](graph.yaml). Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).  
Git: [git.md](git.md). Vercel: [vercel.md](vercel.md). Crítico: [../_shared/critic.md](../_shared/critic.md).

| id | Rol | kind |
|----|-----|------|
| `surveyor` | Gits + targets Vercel; **instancia** committer/publisher | serial |
| `committer` | Commit + merge a `main` + push (`committer:<repo_id>`, luego `committer:home`) | parallel entre gits de producto |
| `publisher` | `vercel --prod --yes` (`publisher:<app_id>`) | parallel por app |
| `gate` | SHA git; URL Vercel; sin secretos; sin `--force` (`gate:git`, `gate:vercel`, `gate:home`) | gate |
| `critic` | SHA + URL o skip; sin secretos | gate |
| `scribe` | Deploy Report | serial |

## Flujo

`surveyor` instancia nodos y rewirea ([graph.md](../_shared/graph.md)):

```text
surveyor → [committer:<repo> ∥ …] → gate:git → [publisher:<app> ∥ …]
  → gate:vercel → critic → scribe → committer:home → gate:home
```

Sin Vercel: `gate:vercel` `skipped`. Home **después** del DR. Producto no toca `memory/`.

## committer (subagente)

1. `id` = `committer:<repo_id>`; `writes` = path de ese git.
2. Seguir [git.md](git.md) en ese path.
3. Producto: no tocar `memory/` ni `director-state.json`.
4. Home: sí `memory/` (incluido el DR); **no** editar `director-state.json`.
5. Devolver: SHA, rama, rol, commit nuevo, push, untracked agregados, error exacto.

## publisher (subagente)

1. `id` = `publisher:<app_id>`; `writes: []` (CLI Vercel, no git).
2. Seguir [vercel.md](vercel.md).
3. No pushear git. No tocar `director-state.json`.
4. Devolver: URL de producción, exit code, error exacto.
