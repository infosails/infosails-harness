# Agentes internos — Discovery

Solo el **Discovery Lead** los conoce y activa (como **subagentes** según el grafo). La Orquesta no.

Grafo: [graph.yaml](graph.yaml). Orquestación: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).  
Crítico: [../_shared/critic.md](../_shared/critic.md). Lecciones: [../_shared/lessons.md](../_shared/lessons.md).

| id | Rol | Entrada | Salida | kind |
|----|-----|---------|--------|------|
| `interviewer` | Periodista: usuarios, tipo de app, existencia, integraciones, alcance | inbox / intent + lecciones | Borrador detallado | human |
| `user-scout` | Roles/perfiles en BPs y landscape previos | brief | notas de usuarios existentes | parallel (optional) |
| `inventory-scout` | Solapes en blueprints/código/landscape | brief parcial | notas `YA_EXISTE`/`PARCIAL` | parallel (optional) |
| `critic` | Checklist de cierre | borrador | pass/fail | gate |
| `scribe` | Persistir Feature Blueprint rico | Borrador que pasó critic | `memory/blueprints/*` + índices | serial |

Frontier al start: `interviewer` ∥ scouts. `critic` espera a los tres (`skipped` cuenta). Luego `scribe`.

## interviewer

- Sigue [interview.md](interview.md).
- Prioridad: **usuarios**, **tipo de aplicación**, existencia, integraciones exteriores, Core/Gherkin.
- Lee lecciones `to_process: discovery`.
- No marca el nodo `done` hasta el checklist de cierre; `critic` lo vuelve a puntuar.

## user-scout / inventory-scout

- No entrevistan al usuario final.
- Devuelven hallazgos al Lead; el interviewer confirma con el stakeholder.
- Si no aplican: `skipped` **antes** del frontier de `critic`.

## critic

- [critic.md](../_shared/critic.md) § Discovery.
- Fail → rewind a `interviewer` (`idle`/`running`); no `complete`.

## scribe

- No entrevista. No inventa: materializa lo que pasó `critic`.
- Un solo scribe; `writes`: `memory/blueprints/`, `memory/backlog.json`.
