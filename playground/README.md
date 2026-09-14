# Playground (dev)

Proyecto **simulado** dentro del kit para probar Orquesta / Leads / memoria
sin instalar el harness ni crear otro repositorio.

## Arrancar / aplicar el kit nuevo

Desde la raíz del kit:

```bash
./scripts/playground          # crea o refresca (no borra memory/)
./scripts/playground --reset  # memoria en limpio
./scripts/playground --status
```

Un refresh copia YAML, rules, hooks de tokens, carpetas `memory/trackers` y `memory/costs` si faltan, y re-enlaza skills/schemas. **No** toca `.env`. **Sí** pisa `harness.project.yaml` con el template (Linear queda `enabled: false` hasta que lo importes).

No uses `--reset` si querés conservar blueprints/bugs. Para limpio pero con BPs: `--reset-keep-blueprints`.

## Probar en Cursor

Quédate en **este** workspace (el kit) y di:

- `probar playground`
- `hola` (si el playground ya existe, la Orquesta opera en modo playground)

Los agentes escriben bajo `playground/memory/` (no en la raíz del kit).

Deploy pushea `main` (producto; el home del kit no) y corre `vercel --prod` si el app está linkeado.

Los skills son **symlink** a `.cursor/skills/` del kit: editas un skill y lo pruebas al momento.

Los hooks de tokens del **kit** (`.cursor/hooks.json` → `scripts/record-usage`) ya apuntan al playground cuando el workspace es el repo del kit. También hay copia en `playground/.cursor/hooks.json` por si abrís esa carpeta sola.

## Linear (opcional)

La API key tiene que estar en `playground/.env` (o exportada en la shell). Crearla en Linear no alcanza.

```bash
# en playground/.env (gitignored):
# LINEAR_API_KEY=lin_api_...

# Ver teams / proyectos
python3 scripts/linear-import --project-root playground --list-teams
python3 scripts/linear-import --project-root playground --list-projects --team-key INF

# Traer issues de Linear → memory (no crea BPs)
python3 scripts/linear-import --project-root playground --import --team-key INF

# Historias de memory/ → issues + tareas + costos
# Si el proyecto no existe, lo crea y le escribe el overview.
python3 scripts/linear-import --project-root playground --push-memory --team-key INF --project playground --dry-run
python3 scripts/linear-import --project-root playground --push-memory --team-key INF --project playground

# Tras un complete de Discovery/Design/Build (lo corre la Orquesta):
# python3 scripts/linear-import --project-root playground --sync-artifact BP-20260717-001 --process discovery
```

Solo enable (MCP, sin bajar issues):

```bash
python3 scripts/linear-import --project-root playground --enable-only --team-key ENG
```

En Cursor el MCP de Linear es opcional. El Director proyecta issues por API (`LINEAR_API_KEY` en `playground/.env`) y el menú tiene la opción Linear.

## Costos

Tras unos turnos de agente: `playground/memory/costs/ledger.json` y `events.jsonl`.
Tarifas USD (opcional): `playground/memory/costs/rates.json`.

## Qué no es

No es un producto real. Para un proyecto de verdad: `infosails-init`.
