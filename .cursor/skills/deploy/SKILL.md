---
name: deploy
description: >-
  Deploy Lead of the InfoSails harness. Commits and pushes product repo(s) and
  the harness home (memory) to main. Asks whether to deploy dev and prod or
  prod only, publishes Vercel dev when both were chosen, then asks again before
  tagging semver and publishing production. Writes Deploy Reports under
  memory/deploys/. Use when Orchestra activates Deploy, or when the user asks
  to deploy, release, or land on main.
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

Deploy = **git a `main`**, después Vercel según lo que el usuario elija:

1. **Ambientes** — antes de commit, push o Vercel, preguntar si va a **dev y prod** o **solo prod**. El pedido inicial de Deploy no elige.
2. **Repos de producto** — commit + `git push origin main`.
3. **Fase dev** — solo si eligieron ambos: `vercel deploy --yes` **sin** `--prod` y alias `-dev` ([vercel.md](vercel.md)). Solo prod: esta fase va `skipped`.
4. **Autorización de prod** — preguntar siempre antes del tag. Con dev, incluye la URL. Sin un sí explícito, no hay tag ni `vercel --prod`. Elegir “solo prod” no es ese sí.
5. **Fase prod** — solo tras ese sí: tag semver anotado en ese SHA, push del tag, y **después** `vercel --prod --yes`.
6. **Home** (`PROJECT_ROOT`) — commit + push de `memory/` (incluye el Deploy Report).

Si eligieron ambos, prod no arranca cuando dev falló. `vercel --prod` no arranca si el tag no está en `origin`.

Si un path de producto **es** el home, un solo push git (no duplicar). El home **no** se publica en Vercel y **no** se taguea.

`complete` con prod = `origin/main` tiene el commit, el usuario autorizó, el tag `vX.Y.Z` apunta a ese SHA en `origin`, y las apps tienen URL de prod en el DR (y URL de dev si eligieron ambos).  
Si el usuario no autoriza prod: `complete` sin tag ni prod (`prod_not_authorized`). Si había elegido ambos y dev quedó bien, el DR lleva esa URL.  
Si no hay target Vercel (solo GCP, o nada que hostear): `complete` con la pregunta, dev, autorización, tag y prod en `skipped`. No inventes tag. No preguntes ambientes ni prod.

Playground del kit: **no** pushear el git del kit. Sí git y tags de apps de producto, y las dos fases Vercel si están linkeadas.

Bump semver: `constraints` del inbox (`semver_bump=patch|minor|major`) o el pedido del usuario. Si no viene, **patch**. Regla: [git.md](git.md).

## Arranque

1. Leer inbox (`start` | `resume` | `abort`).
2. En `start`, copiar [graph.yaml](graph.yaml) a `state.graph` (nodos `idle`), aunque ya hubiera un grafo. `resume` no lo reemplaza.
3. Resolver BR en focus (y DS/BP). Sin BR → `blocked` pidiendo Build.
4. Anunciar: `Deploy Lead activo — primero pregunto si es dev y prod, o solo prod.`

## Agentes internos

| Orden | Agente | Rol | kind |
|-------|--------|-----|------|
| 1 | `surveyor` | Gits + Vercel; instanciar committer / publisher:dev / tagger / publisher:prod | serial |
| 2 | `choose:targets` | Preguntar dev+prod o solo prod. El Lead lo encarna. Antes de git y de Vercel | human |
| 3 | `committer` | Commit + merge a `main` + push | parallel por git de **producto** |
| 4 | `gate:git` | SHA en `origin/main`; sin secretos; no `--force` | gate |
| 5 | `publisher:dev` | `vercel deploy --yes` sin `--prod`, luego alias `-dev`. Solo si eligieron ambos | parallel por app Vercel |
| 6 | `gate:vercel:dev` | Alias `-dev` apunta al deployment; exit 0 | gate |
| 7 | `approve:prod` | Pedir autorización de prod. El Lead lo encarna | human |
| 8 | `tagger` | Tag `vX.Y.Z` anotado + push del tag | parallel por git de **producto** |
| 9 | `gate:tag` | Tag en `origin` sobre el SHA de `main` | gate |
| 10 | `publisher:prod` | `vercel --prod --yes` | parallel por app Vercel |
| 11 | `gate:vercel:prod` | URL de producción; exit 0 | gate |
| 12 | `critic` | SHA + tag + URLs o skip; sin secretos | gate |
| 13 | `scribe` | Deploy Report (SHAs + tag + URLs) | serial |
| 14 | `committer:home` | Tras el DR: commit + push del home | serial |
| 15 | `gate:home` | SHA del home en `origin/main` | gate |

Ambientes primero; después git; dev solo si eligieron ambos; autorización; tag; prod; `critic` antes de `scribe`; home al final. El `surveyor` rewirea el grafo ([graph.md](../_shared/graph.md)). `choose:targets` y `approve:prod` no son subagentes.

## Ambientes

La encarna el Lead en `choose:targets`, cuando hay target Vercel y **antes** de commit, push o Vercel. Anota la respuesta en `working.targets` (`both` | `prod`).

`¿Deploy a dev y prod, o solo a prod?`

- **Ambos** (`dev y prod`, `los dos`, `ambos`) → `both`. Sigue el grafo con la fase dev.
- **Solo prod** (`solo prod`, `solo producción`, `no hay dev`) → `prod`. Marca `publisher:dev:*` y `gate:vercel:dev` como `skipped` (`prod_only`). No corras `vercel deploy` ni el alias `-dev`.
- Otra cosa, o el “deploy” con el que arrancó el proceso → no elige. Outbox `blocked`, nodo `running`. No git. No Vercel.

```json
{
  "event": "blocked",
  "process": "deploy",
  "summary": "¿Deploy a dev y prod, o solo a prod?",
  "blocked_reason": "deploy_targets",
  "next_hint": null
}
```

En `resume`, lee la respuesta nueva. No recopies el grafo.

## Autorización de prod

La encarna el Lead cuando `gate:vercel:dev` está `done` o `skipped` por `prod_only`. Calcula el tag siguiente ([git.md](git.md)) y muéstralo; no lo crees todavía.

Si hubo dev:

`Dev está en <alias>. El tag sería <vX.Y.Z>. ¿Autorizas el tag y vercel --prod?`

Si eligieron solo prod:

`Solo prod. El tag sería <vX.Y.Z>. ¿Autorizas el tag y vercel --prod?`

- **Sí** explícito a esta pregunta (`sí`, `autorizo`, `a producción`) → `approve:prod` `done` y sigue el grafo.
- **No** explícito (`no`, `solo dev`) → `approve:prod` `done` y marca `tagger:*`, `gate:tag`, `publisher:prod:*` y `gate:vercel:prod` como `skipped` con `prod_not_authorized`. Sigue a `critic`.
- Otra cosa, silencio, o el “deploy” con el que arrancó el proceso → no es autorización. Outbox `blocked` y el nodo sigue `running`. No tag. No `vercel --prod`.

```json
{
  "event": "blocked",
  "process": "deploy",
  "summary": "Dev en <alias>. ¿Autorizas tag vX.Y.Z y vercel --prod?",
  "blocked_reason": "prod_authorization",
  "next_hint": null
}
```

En `resume`, lee la respuesta nueva. No recopies el grafo.

## Surveyor

Leer BR, DS, landscape `repositories` e infra. Armar:

- Gits: cada `path` de producto + home (si no es playground-kit)
- Targets Vercel: path con `.vercel/`, `vercel.json`, o hosting Vercel en landscape/DS
- Bump: `semver_bump` en constraints, o `patch`

Por cada git: `git status -u`, rama, remote, **untracked**, tags `v*` ya mergeados en `HEAD`.  
Por cada target Vercel: ¿CLI? ¿`.vercel/`? ¿`VERCEL_TOKEN` o `vercel whoami`?

`repos` de producto vacío → `blocked`.  
Vercel en landscape pero sin link/token → `blocked` pidiendo `vercel link` y/o `VERCEL_TOKEN` (no adivinar).  
Sin target Vercel → seguir; `choose:targets`, `gate:vercel:dev`, `approve:prod`, `gate:tag` y `gate:vercel:prod` en `skipped` (no publishers, no taggers, no preguntas). Con Vercel, `choose:targets` y `approve:prod` no se saltean. Los `publisher:dev` se instancian igual: si el usuario elige solo prod, `choose:targets` los marca `skipped`.

**Antes de marcar `surveyor` `done`:** si hay Vercel, instanciar `committer:<repo_id>`, `publisher:dev:<app_id>`, `tagger:<repo_id>` (producto, no home) y `publisher:prod:<app_id>`; rewirear aristas ([graph.md](../_shared/graph.md)). Los committers quedan **después** de `choose:targets`.

## Gate

Git:

- [ ] Push **sin** `--force` / `--no-verify` (salvo pedido del usuario)
- [ ] No hay `.env` ni secretos en el commit
- [ ] `origin/main` es el SHA reportado
- [ ] No quedaron untracked de producto (archivos nuevos de Build van en el commit; ignorados de `.gitignore` no)

Dev (solo si `working.targets` es `both`):

- [ ] `vercel deploy --yes` **sin** `--prod`, exit 0
- [ ] Alias `-dev` (host de producción con `-dev` al final de la primera etiqueta) apunta a ese deployment
- [ ] URL de dev del DR es el alias
- [ ] Auth por `VERCEL_TOKEN` o sesión; no `--token` en el comando

Tag (solo con un sí en `approve:prod`):

- [ ] Tag anotado `vMAJOR.MINOR.PATCH` en el SHA de `origin/main`
- [ ] El tag está en `origin` (push sin `--force`)
- [ ] No se tagueó el home

Prod (solo si el tag ya está en `origin`):

- [ ] `vercel --prod --yes` exit 0
- [ ] URL de producción en el DR
- [ ] Auth por `VERCEL_TOKEN` o sesión; no `--token` en el comando

Si `main` está protegida → `blocked` git (no Vercel, no tag).  
Si eligieron ambos, git ok y dev falla → `blocked` parcial (main landed; sin tag; sin prod).  
Con dev verde, o con solo prod y git verde → `blocked` `prod_authorization` hasta un sí o un no. No es un fallo.  
Si el usuario no autoriza prod → no es `blocked`: tag y prod `skipped`, y el DR cierra así.  
Si autorizó y el tag falla → `blocked` parcial (main landed; dev solo si la hubo; sin prod).  
Si tag ok y prod falla → `blocked` parcial (main + dev + tag; prod no).  
Si falló solo el home → `blocked` parcial (producto + dev + tag + prod ok; memoria no).

## critic

[critic.md](../_shared/critic.md) § Deploy. SHA + tag + URLs o skip; sin secretos. Luego `scribe`.

## Scribe

- ID: `DR-{YYYYMMDD}-{SEQ}`
- `memory/deploys/{ID}-{slug}.md` (+ `.json`)
- Template: `templates/deploy-report.md`
- Índices + backlog (`next_hint: null`)
- Escribir el DR **después** de la fase prod (o del skip de Vercel) y **antes** del push del home.

### Outbox `complete`

```json
{
  "event": "complete",
  "process": "deploy",
  "summary": "Deploy DR-…; producto main @ <sha> tag <vX.Y.Z>; vercel dev <url>; vercel prod <url>; home @ <sha>",
  "artifact": { "type": "deploy_report", "id": "DR-…", "path": "memory/deploys/DR-….md" },
  "next_hint": null
}
```

## Reglas

- Orden: **ambientes → git producto → vercel dev si eligieron ambos → autorización → tag semver → vercel --prod → informe → git home**.
- “Solo prod” salta dev y el alias. Igual hace falta un sí antes del tag.
- Prod no arranca solo porque eligieron ambos. Hace falta el sí de `approve:prod`.
- No fingir dev ni prod. No force-push. No mover tags. No commitear secretos.
- No `gcloud` publish salvo pedido explícito y landscape GCP.
