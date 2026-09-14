# Lecciones (memoria de calidad)

Cuando Design/Build/Deploy **rechazan** el artefacto de un proceso anterior, el Lead que bloquea escribe 3–5 líneas en memoria de **producto**. El chat no cuenta. El skill del kit no se parchea.

El grafo memoriza qué falta **correr**. Las lecciones memorizan qué no volver a **entregar**.

## Dónde

Índice por **síntoma** (como se lee el landscape):

| Path | Quién |
|------|--------|
| `memory/lessons/_index.json` | `by_symptom` → ids `LSN-…` |
| `memory/lessons/LSN-YYYYMMDD-SEQ.md` + `.json` | Lead que bloquea |
| Template | `templates/lesson.md` |
| Schema | `schemas/lesson.schema.json` |

La Orquesta **no** escribe lecciones. No las mezcles con `director-state.json`.  
No uses `memory/architecture/` para el diario: si el **mismo síntoma** aparece 2+ veces, el Lead destino puede copiar **una** línea a landscape/guardrails (vivo del producto), no al skill del kit.

## Cuándo escribir

Outbox `blocked` por **calidad aguas arriba**, y rewind al proceso anterior. El Lead origen, al `resume`, lee la lección; no solo reabre el agente.

| Quién bloquea | `to_process` | `symptom` (slug) |
|---------------|--------------|------------------|
| Design | discovery | `missing_user_profiles` · `vague_app_type` · `gherkin_abstract` · `empty_inventory` |
| Build | design | `ds_missing_paths` · `ds_missing_contracts` · `ui_unmapped` · `hexagonal_violated` |
| Build | discovery | `gherkin_untestable` |
| Deploy | build | `br_not_commit_ready` · `gates_faked` |

**No** escribas lección por: falta de secreto, usuario ausente, Task de Cursor caído, JSON de subagente inválido (eso es retry del nodo).

## Cómo escribir (3–5 líneas)

1. Id `LSN-{YYYYMMDD}-{SEQ:03d}`.
2. JSON: `from_process`, `to_process`, `symptom`, `artifact_id`, `summary`, `do`, `do_not`.
3. Markdown: qué pasó / hacer / no hacer. Sin essay.
4. `_index.json`: entrada en `lessons[]` **y** `by_symptom[symptom] += id`.
5. Outbox `blocked` con `blocked_reason` = `summary` (sin nombres de agentes internos).

## Cuándo leer (igual que el landscape)

Al `start` / `resume`, **antes** del frontier, el `surveyor` / `interviewer` / `triager`:

1. `memory/lessons/_index.json` → `by_symptom`
2. Filtrar `to_process` = este proceso (y `artifact_id` en focus si hay)
3. Meter los slugs + `do` / `do_not` en el prompt del primer nodo

No reescribas el skill del kit. La lección es de **este** producto.

## Rewind interno vs lección

| Caso | Memoria |
|------|---------|
| `critic` fail en el **mismo** proceso | Rewind de nodos. Sin LSN. |
| Proceso de abajo no puede usar el artefacto de arriba | LSN + outbox `blocked` |
| Task/Cursor falló o reporte inválido | Retry del nodo. Sin `memory/`. |
