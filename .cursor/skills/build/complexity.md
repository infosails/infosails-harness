# Agente `complexity-gate` — Complejidad ciclomática (Build)

Gate de **complejidad ciclomática** (McCabe) del proceso Build.  
Complementa TDD y cobertura: no los sustituye. Un test que pasa no justifica un método inentendible.

## Dónde encaja

```text
tdd-dev (refactor si una función se dispara)
    → coverage-gate
    → [ sast ∥ mutation ∥ e2e ∥ complexity-gate ]
    → integrator
```

Corre **después** de cobertura (el código de la historia ya está escrito) y **en paralelo** con sast / mutación / e2e.

## Umbral

| Métrica | Default | Ajuste |
|---------|---------|--------|
| CCN (McCabe) por función **nueva o tocada** | **≤ 10** | Solo con ADR Accepted |

- Scope: diff / paths de la historia (mismos `writes[]` de los WPs), no todo el repo.
- Funciones **no tocadas** se ignoran, aunque ya superen 10.
- Si el WP **toca** una función que ya estaba > 10: hay que bajarla a ≤ 10 (o partirla). No empeorar.
- Gate rojo → no `complete`. Listar ofensores (archivo, símbolo, CCN) en el Build Report.

## Herramientas

Preferir una ya en el repo; si no hay, instalar/configurar una razonable:

| Stack | Opciones |
|-------|----------|
| JS/TS | ESLint `complexity`, `eslint-plugin-sonarjs` |
| Python | `radon cc`, lizard |
| Go | `gocyclo` |
| Java/Kotlin | PMD CyclomaticComplexity, checkstyle |
| Genérico | **lizard** (default org si no hay otra) |

Documentar herramienta + comando + versión en el Build Report.  
No fingir PASS sin correr el análisis. Si no se puede instalar → `blocked` (igual que mutación/SAST).

Ejemplo lizard (CCN > 10 = fail):

```bash
lizard --CCN 10 <paths del WP>
```

## Durante `tdd-dev` (antes del gate)

Opcional y rápido: correr la misma herramienta sobre `writes[]` del WP antes de marcarlo `done`. El gate formal sigue siendo `complexity-gate`.

Si una función nueva ya va > 10 en Red/Green: **refactor** en ese WP (el paso Refactor del TDD), no dejarlo para “después”.
