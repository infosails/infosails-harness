# Memoria del proyecto

Resultado de procesos + arquitectura + builds. El chat no es memoria.

## Contenido

| Ruta | Qué / quién |
|------|-------------|
| `director-state.json` | Orquesta |
| `processes/<proceso>/` | Lead: state + inbox/outbox |
| `backlog.json` | Cola unificada |
| `blueprints/` | Historias (Discovery) |
| `bugs/` | Defectos (Bug) |
| `architecture/` | Landscape + ADRs (Design) |
| `designs/` | Design Packages (Design → Build) |
| `builds/` | Build Reports (gates TDD/cov/mutación/e2e) |

## Arquitectura vs diseño vs build

```text
architecture/  ← mapa vivo del producto
designs/DS-…   ← cómo construir ESTA historia
builds/BR-…    ← evidencia de que se construyó con calidad
```

## Futuro

`runs/` — ejecuciones adicionales / deploy logs.
