# Costos de tokens

Cursor no expone la factura por historia. El hook `stop` / `subagentStop` sí manda tokens del turno. Eso se guarda en `memory/costs/` y se atribuye al artefacto en focus (`BP-…`, `BUG-…`, `ONBOARD-…`).

- **Tokens:** dato del hook (input / output / cache). Si el payload no trae cuentas, el turno queda en 0.
- **USD:** estimación opcional con `memory/costs/rates.json` (USD por 1M tokens). `null` = no inventar dinero.
- **Factura real:** dashboard de Cursor. El ledger es consumo atribuido al producto, no el cobro.

No edites `memory/costs/` (lo escribe `scripts/record-usage`). Solo lee.

## Al arrancar / menú / ver estado

Lee `memory/costs/ledger.json` si existe. En el resumen, una línea:

```text
Costo proyecto: in=… out=… cache_r=… (~US$ … o “sin tarifa”)
```

Si hay focus, agrega el total de ese artefacto y el desglose `by_process` (discovery / design / build / bug / onboard).

## Tras `complete`

1. El hook puede atribuir tarde: el ledger se recalcula desde `events.jsonl`.
2. En el `history` de `director-state`, en el evento `complete`, copia un snapshot:

```json
"usage": {
  "input_tokens": 0,
  "output_tokens": 0,
  "cache_read_tokens": 0,
  "cache_write_tokens": 0,
  "usd_estimate": null
}
```

Usa `by_artifact[id].by_process[proceso]` si está; si no, `by_artifact[id].totals`.

3. Di al usuario esa cifra junto al `summary` del Lead.

## Tarifas

El usuario completa `memory/costs/rates.json`. Ejemplo:

```json
{
  "currency": "USD",
  "per_million": {
    "default": { "input": 3.0, "output": 15.0, "cache_read": 0.3, "cache_write": 3.75 }
  }
}
```

Tras cambiar rates, el próximo `stop` rebuild del ledger aplica las tasas a todo el jsonl.

`harness.project.yaml` → `costs.enabled: false` desactiva el hook (el script no escribe).
