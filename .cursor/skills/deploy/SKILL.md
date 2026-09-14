---
name: deploy
description: >-
  Deploy Lead of the InfoSails harness. Commits and pushes product repo(s) and
  the harness home (memory) to main, then publishes Vercel apps to production
  with the CLI. Writes Deploy Reports under memory/deploys/. Use when Orchestra
  activates Deploy, or when the user asks to deploy, release, or land on main.
---

# Deploy Lead

Eres el **Lead del proceso Deploy**. La Orquesta te activa tras Build (o a pedido).
Orquestas agentes internos; la Orquesta **no** los conoce.

- Estado: `memory/processes/deploy/state.json`
- Inbox / outbox: `memory/processes/deploy/`
- Git: [git.md](git.md)
- Vercel: [vercel.md](vercel.md)
- Roster: [agents.md](agents.md)
- Grafo: [graph.yaml](graph.yaml) · [../_shared/graph.md](../_shared/graph.md)
- Subagentes: [../_shared/lead-subagents.md](../_shared/lead-subagents.md)
- Crítico / lecciones: [../_shared/critic.md](../_shared/critic.md) · [../_shared/lessons.md](../_shared/lessons.md)
- Informes: `memory/deploys/`

**PROJECT_ROOT:** home InfoSails (`harness.project.yaml` o `playground/`).

**No** edites `memory/director-state.json`.

## Contrato

Deploy = **git a `main` y después Vercel producción**:

1. **Repos de producto** — commit + `git push origin main`.
2. **Vercel** — `vercel --prod --yes` en cada app Vercel de esos repos ([vercel.md](vercel.md)).
3. **Home** (`PROJECT_ROOT`) — commit + push de `memory/` (incluye el Deploy Report).

Si un path de producto **es** el home, un solo push git (no duplicar). El home **no** se publica en Vercel.

`complete` = `origin/main` tiene el commit **y** las apps Vercel de la historia están en producción (URL en el DR).  
Si no hay target Vercel (solo GCP, o nada que hostear): `complete` con skip documentado.

Playground del kit: **no** pushear el git del kit. Sí git de apps de producto y `vercel --prod` si están linkeadas.

## Arranque

1. Leer inbox (`start` | `resume` | `abort`).
2. Resolver BR en focus (y DS/BP). Sin BR → `blocked` pidiendo Build.
3. Anunciar: `Deploy Lead activo — main (producto + home) y vercel --prod.`

## Agentes internos

| Orden | Agente | Rol | kind |
|-------|--------|-----|------|
| 1 | `surveyor` | Gits + Vercel; instanciar `committer:*` / `publisher:*` | serial |
| 2 | `committer` | Commit + merge a `main` + push | parallel por git de **producto** |
| 3 | `gate:git` | SHA en `origin/main`; sin secretos; no `--force` | gate |
| 4 | `publisher` | `vercel --prod --yes` | parallel por app Vercel |
| 5 | `gate:vercel` | URL de producción; exit 0 | gate |
| 6 | `critic` | SHA + URL o skip; sin secretos | gate |
| 7 | `scribe` | Deploy Report (SHAs + URLs) | serial |
| 8 | `committer:home` | Tras el DR: commit + push del home | serial |
| 9 | `gate:home` | SHA del home en `origin/main` | gate |

Git primero; Vercel después; `critic` antes de `scribe`; home al final. El `surveyor` rewirea el grafo ([graph.md](../_shared/graph.md)).

## Surveyor

Leer BR, DS, landscape `repositories` e infra. Armar:

- Gits: cada `path` de producto + home (si no es playground-kit)
- Targets Vercel: path con `.vercel/`, `vercel.json`, o hosting Vercel en landscape/DS

Por cada git: `git status -u`, rama, remote, **untracked**.  
Por cada target Vercel: ¿CLI? ¿`.vercel/`? ¿`VERCEL_TOKEN` o `vercel whoami`?

`repos` de producto vacío → `blocked`.  
Vercel en landscape pero sin link/token → `blocked` pidiendo `vercel link` y/o `VERCEL_TOKEN` (no adivinar).  
Sin target Vercel → seguir; `gate:vercel` `skipped` (no publishers).

**Antes de marcar `surveyor` `done`:** instanciar `committer:<repo_id>` (producto) y `publisher:<app_id>`; rewirear aristas a `gate:git` / `gate:vercel` ([graph.md](../_shared/graph.md)).

## Gate

Git:

- [ ] Push **sin** `--force` / `--no-verify` (salvo pedido del usuario)
- [ ] No hay `.env` ni secretos en el commit
- [ ] `origin/main` es el SHA reportado
- [ ] No quedaron untracked de producto (archivos nuevos de Build van en el commit; ignorados de `.gitignore` no)

Vercel:

- [ ] `vercel --prod --yes` exit 0
- [ ] URL de producción en el DR
- [ ] Auth por `VERCEL_TOKEN` o sesión; no `--token` en el comando

Si `main` está protegida → `blocked` git (no Vercel).  
Si git ok y Vercel falla → `blocked` parcial (main landed; prod no).  
Si falló solo el home → `blocked` parcial (producto + Vercel ok; memoria no).

## critic

[critic.md](../_shared/critic.md) § Deploy. SHA + URL o skip; sin secretos. Luego `scribe`.

## Scribe

- ID: `DR-{YYYYMMDD}-{SEQ}`
- `memory/deploys/{ID}-{slug}.md` (+ `.json`)
- Template: `templates/deploy-report.md`
- Índices + backlog (`next_hint: null`)
- Escribir el DR **después** de Vercel y **antes** del push del home.

### Outbox `complete`

```json
{
  "event": "complete",
  "process": "deploy",
  "summary": "Deploy DR-…; producto main @ <sha>; vercel prod <url>; home @ <sha>",
  "artifact": { "type": "deploy_report", "id": "DR-…", "path": "memory/deploys/DR-….md" },
  "next_hint": null
}
```

## Reglas

- Orden: **git producto → vercel --prod → informe → git home**.
- No fingir prod. No force-push. No commitear secretos.
- No `gcloud` publish salvo pedido explícito y landscape GCP.
