# Arquitectura del producto

Memoria viva de **cómo está construido** el software y **qué ya existe**.

## Contenido

| Artefacto | Rol |
|-----------|-----|
| `landscape.md` / `.json` | Mapa: stack, **infra**, repos, módulos, as-built |
| `adrs/` | Decisiones (por qué) |

## Estilo de software

- **Hexagonal** (domain / application / ports / adapters) en cada artefacto.
- **Modular monolith** (o pocos deployables) por defecto.
- **Microservicios solo si es absolutamente necesario** + ADR.
- Detalle: `.cursor/skills/design/architecture-principles.md`

## Infraestructura

Nubes de la org: **Vercel** + **GCP**, suites **completas**.

- Elegir servicio por necesidad (no front→Vercel / back→GCP).
- Una, la otra, o ambas en la misma historia.
- Otra nube → solo con ADR de excepción.

## Repositorios

El **Design Lead** define cuántos repos hay (monorepo / multi / híbrido), los registra en el landscape y **los crea** cuando el diseño lo exige. Build solo consume esa lista. Preferir pocos repos con módulos hexagonales; no multiplicar servicios sin necesidad.

## As-built

La sección **Capacidades construidas** del landscape es el inventario de lo ya construido.
El **Design Lead** debe actualizarla cuando una historia **cambia, extiende o depreca** algo existente.
Si no se actualiza, el próximo Discovery/Design mentirá sobre lo que hay.

## Flujo

```text
Blueprint
  → Design lee landscape + as-built + ADRs
  → Design Package (hexagonal; microservicios solo si ADR)
  → si hay cambio: update landscape/as-built/repos (+ ADR)
  → Build consume Design Package
```
