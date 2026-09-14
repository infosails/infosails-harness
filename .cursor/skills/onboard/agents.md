# Agentes internos — Onboard

La Orquesta no ve esta lista. Solo el **Onboard Lead**.

Grafo: [graph.yaml](graph.yaml). Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).

| Agente | Input | Output | kind |
|--------|-------|--------|------|
| `code-surveyor` | árbol del repo, manifests, infra | notas: stack, módulos, repos, entrypoints | parallel |
| `capability-scout` | rutas, dominios, UI, APIs, tests | lista de capacidades candidatas a BP Done | parallel |
| `docs-scout` | README, docs/, OpenAPI, ADRs en repo | hechos documentados + gaps explícitos | parallel |
| `synthesizer` | salidas de los tres scouts | draft landscape + lista final de BPs | serial |
| `critic` | draft | pass/fail (no inventar DS/BR) | gate |
| `scribe` | draft que pasó critic | landscape, BPs Done, índices, backlog, ONBOARD-*.md | serial |

Frontier al start (con lista de repos): los tres scouts en paralelo.

`critic` § Onboard: [../_shared/critic.md](../_shared/critic.md).

## code-surveyor / capability-scout / docs-scout

Sin lista de repos del Lead → no están ready (el Lead no lanza el grafo hasta tener repos).  
No escribir `memory/` aún (`writes: []`).

## synthesizer

Fusionar scouts; código > docs. El Lead puede hacer **una** pregunta estructural; luego `critic`.

## scribe

Escribe todo de una pasada tras `critic` `done`. `writes` del nodo en [graph.yaml](graph.yaml).
