# Agentes internos — Discovery

Solo el **Discovery Lead** los conoce y activa. La Orquesta no.

| id | Rol | Entrada | Salida |
|----|-----|---------|--------|
| `interviewer` | Periodista: itera, clasifica qué ya existe vs qué construir | inbox / intent | Borrador detallado + inventario de existencia |
| `scribe` | Persistir Feature Blueprint rico | Borrador confirmado | `memory/blueprints/*` + índices |

## interviewer

- Sigue [interview.md](interview.md).
- Prioridad: claridad, detalle e **inventario de existencia** (`YA_EXISTE` / `PARCIAL` / `NUEVO`).
- No marca `done` hasta pasar el checklist de cierre.

## scribe

- No entrevista.
- No inventa: solo materializa lo confirmado, con el máximo detalle disponible.
- Si detecta un hueco → devuelve control al `interviewer` (no complete a Orquesta).

## Estados

`idle` → `running` → `done` | `blocked` | `skipped`
