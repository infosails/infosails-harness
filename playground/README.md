# Playground (dev)

Proyecto **simulado** dentro del kit para probar Orquesta / Leads / memoria
sin instalar el harness ni crear otro repositorio.

## Arrancar

Desde la raíz del kit:

```bash
./scripts/playground          # crea o refresca
./scripts/playground --reset  # memoria en limpio
./scripts/playground --status
```

## Probar en Cursor

Quédate en **este** workspace (el kit) y di:

- `probar playground`
- `hola` (si el playground ya existe, la Orquesta opera en modo playground)

Los agentes escriben bajo `playground/memory/` (no en la raíz del kit).

Los skills son **symlink** a `.cursor/skills/` del kit: editas un skill y lo pruebas al momento.

## Qué no es

No es un producto real. Para un proyecto de verdad: `infosails-init`.
