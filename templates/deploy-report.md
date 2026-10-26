# [DR-ID] Deploy Report: [Nombre corto]

> **Build Report:** [BR-ID]
> **Design Package:** [DS-ID]
> **Blueprint / Bug:** [BP-ID | BUG-ID]
> **Status:** Live | On main | Blocked
> **Creado por:** Deploy Lead
> **Contrato:** git `main`, vercel dev (sin `--prod`), tag semver, `vercel --prod`
> **Bump:** patch | minor | major

## 1. Resumen

* Producto (git):
* Tag semver:
* Ambientes: dev y prod | solo prod | skipped
* Vercel dev (alias `-dev`):
* Autorización prod: sí | no | skipped
* Vercel prod:
* Home (memoria):
* Push git: sí | ya estaba | skipped (playground-kit) | blocked
* Vercel dev: sí | skipped | blocked
* Tag: sí | reutilizado | skipped | blocked
* Vercel prod: sí | skipped | blocked

## 2. Git (producto)

| Repo ID | Path | SHA `main` | Remote | Commit nuevo |
|---------|------|------------|--------|--------------|
| | | | origin | sí / no |

## 3. Vercel (dev)

Deployment sin `--prod`. El alias es el host de producción con `-dev` al final de la primera etiqueta (`acme.vercel.app` → `acme-dev.vercel.app`). No es producción.

| App | Path | Deployment | Alias dev | Nota |
|-----|------|------------|-----------|------|
| | | https://…-hash.vercel.app | https://…-dev… | o skipped: no es Vercel / sin link |

## 4. Tag semver

Anotado sobre el SHA de `main`, pusheado a `origin` **antes** de `vercel --prod`. El home no se taguea.

| Repo ID | Tag | SHA | Bump | En origin |
|---------|-----|-----|------|-----------|
| | vX.Y.Z | | patch / minor / major | sí / reutilizado / skipped |

## 5. Vercel (producción)

| App | Path | URL producción | CLI | Nota |
|-----|------|----------------|-----|------|
| | | https://… | `vercel --prod --yes` | o skipped: no es Vercel / sin link |

## 6. Git (home — memoria del producto)

El repo donde corre el harness (`harness.project.yaml` + `memory/`). No se publica en Vercel y no lleva tag de release.

| Path | SHA `main` | Remote | Commit nuevo | Nota |
|------|------------|--------|--------------|------|
| | | origin | sí / no | o skipped: playground dentro del kit |

## 7. Qué no hizo este proceso

* No `vercel --prod` antes del tag.
* No dejó el deployment de dev sin el alias `-dev`.
* No trató el alias `-dev` como producción.
* No `gcloud` publish salvo pedido y landscape GCP.
* No afirma Live si la CLI de prod falló o se skipeó.
