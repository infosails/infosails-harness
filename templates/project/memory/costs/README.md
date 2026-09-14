# Costos de tokens (Cursor)

El hook `stop` / `subagentStop` escribe aquí. **No** es la factura de Cursor (suscripción / dashboard); es el consumo atribuido a este proyecto.

| Archivo | Rol |
|---------|-----|
| `ledger.json` | Totales del proyecto, por artefacto (`BP-…` / `BUG-…`) y por proceso |
| `events.jsonl` | Un turno por línea (historia operativa) |
| `rates.json` | USD / 1M tokens (opcional). `null` = solo tokens |

El Director lee el ledger al mostrar estado y al cerrar un proceso. No lo edita.
