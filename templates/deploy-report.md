# [DR-ID] Deploy Report: [Nombre corto]

> **Build Report:** [BR-ID]
> **Design Package:** [DS-ID]
> **Blueprint / Bug:** [BP-ID | BUG-ID]
> **Status:** Live | On main | Blocked
> **Creado por:** Deploy Lead
> **Contrato:** git `main` (producto + home) y `vercel --prod`

## 1. Resumen

* Producto (git):
* Vercel prod:
* Home (memoria):
* Push git: sí | ya estaba | skipped (playground-kit) | blocked
* Vercel: sí | skipped | blocked

## 2. Git (producto)

| Repo ID | Path | SHA `main` | Remote | Commit nuevo |
|---------|------|------------|--------|--------------|
| | | | origin | sí / no |

## 3. Vercel (producción)

| App | Path | URL producción | CLI | Nota |
|-----|------|----------------|-----|------|
| | | https://… | `vercel --prod --yes` | o skipped: no es Vercel / sin link |

## 4. Git (home — memoria del producto)

El repo donde corre el harness (`harness.project.yaml` + `memory/`). No se publica en Vercel.

| Path | SHA `main` | Remote | Commit nuevo | Nota |
|------|------------|--------|--------------|------|
| | | origin | sí / no | o skipped: playground dentro del kit |

## 5. Qué no hizo este proceso

* No `gcloud` publish salvo pedido y landscape GCP.
* No afirma Live si la CLI de Vercel falló o se skipeó.
