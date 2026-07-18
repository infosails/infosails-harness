# Agentes internos — Discovery

Solo el **Discovery Lead** los conoce y activa (como **subagentes** cuando aplique). La Orquesta no.

Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).

| id | Rol | Entrada | Salida | Paralelo |
|----|-----|---------|--------|----------|
| `interviewer` | Periodista: usuarios, tipo de app, existencia, integraciones, alcance | inbox / intent | Borrador detallado | no (usuario) |
| `user-scout` (opcional) | Roles/perfiles en BPs y landscape previos | brief | notas de usuarios existentes | **sí** |
| `inventory-scout` (opcional) | Solapes en blueprints/código/landscape | brief parcial | notas `YA_EXISTE`/`PARCIAL` | **sí** |
| `scribe` | Persistir Feature Blueprint rico | Borrador confirmado | `memory/blueprints/*` + índices | no |

## interviewer

- Sigue [interview.md](interview.md).
- Prioridad: **usuarios**, **tipo de aplicación**, existencia, integraciones exteriores, Core/Gherkin.
- No marca `done` hasta pasar el checklist de cierre.
- Puede pedir al Lead que lance `user-scout` / `inventory-scout` en paralelo entre rondas.

## user-scout (subagente)

- No entrevista al usuario final.
- Busca roles/perfiles ya tipados en `memory/blueprints/`, landscape y designs.
- Devuelve hallazgos al Lead; el interviewer confirma o completa con el stakeholder.

## inventory-scout (subagente)

- No entrevista al usuario.
- Devuelve hallazgos al Lead; el interviewer confirma tags con el humano.

## scribe

- No entrevista.
- No inventa: solo materializa lo confirmado (incluye § usuarios y § tipo de aplicación).
- Si detecta un hueco → devuelve control al `interviewer` (no complete a Orquesta).
- Un solo scribe; no paralelo con otro writer del mismo BP.

## Estados

`idle` → `running` → `done` | `blocked` | `skipped`
