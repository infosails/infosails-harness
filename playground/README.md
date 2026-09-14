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

Un refresh copia YAML, rules, hooks de tokens, carpeta `memory/costs` si falta, y re-enlaza skills/schemas. **No** toca `.env`. **Sí** pisa `harness.project.yaml` con el template.

No uses `--reset` si querés conservar blueprints/bugs. Para limpio pero con BPs: `--reset-keep-blueprints`.

## Probar en Cursor

Quédate en **este** workspace (el kit) y di:

- `probar playground`
- `hola` (si el playground ya existe, la Orquesta opera en modo playground)

Los agentes escriben bajo `playground/memory/` (no en la raíz del kit).

Deploy pushea `main` (producto; el home del kit no) y corre `vercel --prod` si el app está linkeado.

Los skills son **symlink** a `.cursor/skills/` del kit: editas un skill y lo pruebas al momento.

Los hooks de tokens del **kit** (`.cursor/hooks.json` → `scripts/record-usage`) ya apuntan al playground cuando el workspace es el repo del kit. También hay copia en `playground/.cursor/hooks.json` por si abrís esa carpeta sola.

## Costos

Tras unos turnos de agente: `playground/memory/costs/ledger.json` y `events.jsonl`.
Tarifas USD (opcional): `playground/memory/costs/rates.json`.

## Qué no es

No es un producto real. Para un proyecto de verdad: `infosails-init`.
