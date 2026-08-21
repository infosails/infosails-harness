# Agentes internos — Onboard

La Orquesta no ve esta lista. Solo el **Onboard Lead**.

| Agente | Input | Output | Paralelo |
|--------|-------|--------|----------|
| `code-surveyor` | árbol del repo, manifests, infra | notas: stack, módulos, repos, entrypoints | **sí** |
| `capability-scout` | rutas, dominios, UI, APIs, tests | lista de capacidades candidatas a BP Done | **sí** |
| `docs-scout` | README, docs/, OpenAPI, ADRs en repo | hechos documentados + gaps explícitos | **sí** |
| `synthesizer` | salidas de los tres scouts | draft landscape + lista final de BPs | no |
| `scribe` | draft confirmado por Lead | landscape, BPs Done, índices, backlog, ONBOARD-*.md | no |

## code-surveyor (subagente)

Prompt mínimo:

0. El Lead ya resolvió la lista de repos (YAML, inbox o pregunta al usuario). Sin lista → no correr.
1. Leer la lista de repos resuelta por el Lead (`id`, `path`, `role`, `url`).
2. Por cada `path` existente: detectar lenguajes/frameworks y layout.
3. Listar módulos/paquetes con path, `repo_id` y responsabilidad inferida.
4. Inferir hosting/CI/DB solo con evidencia en ese repo.
5. Devolver bullets estructurados; no escribir `memory/` aún.

## capability-scout (subagente)

1. Agrupar por capacidad de producto (no por archivo).
2. Cada capacidad: nombre corto, evidencia (paths), usuarios aparentes, tipo de superficie.
3. Marcar confianza: alta | media | baja.
4. No inventar features que no tengan rastro en código/docs/tests.

## docs-scout (subagente)

1. Extraer propósito del producto, roles, integraciones nombradas.
2. Listar TODOs/WIP documentados (candidatos a `gaps`).
3. Si hay ADRs fuera de `memory/`, resumirlos (no copiar verbatim si copyright; sintetizar).

## synthesizer

1. Fusionar scouts; resolver conflictos a favor de código > docs.
2. Descartar capacidades de confianza baja sin evidencia doble.
3. Producir: secciones de landscape + lista ordenada de BPs Done.
4. El Lead revisa 30s; si hay duda estructural, **una** pregunta al usuario; si no, `scribe`.

## scribe

Escribe todo de una pasada:

1. `memory/architecture/landscape.md` + `landscape.json`
2. `memory/blueprints/BP-*-*.md` + `.json` (Status `Done`)
3. `memory/blueprints/_index.json`
4. `memory/backlog.json` (ítems Done)
5. `memory/onboard/ONBOARD-*.md` (+ opcional `.json`)
6. Actualizar `state.working` con ids/paths

`Creado por` / seed note: `Onboard Lead`.
