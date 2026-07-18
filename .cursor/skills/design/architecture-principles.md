# Principios de arquitectura (org) — Design Lead

Directivas fijas. Solo se excepcionan con **ADR Accepted**.

## 1. Modularidad antes que microservicios

- Default: **modular monolith** (o pocos deployables) con **límites claros**.
- **Microservicios solo si es absolutamente necesario.**
- Para proponer un microservicio nuevo hay que justificar al menos uno de:
  - Escalado / ciclo de vida / fallo aislado que un módulo no resuelve
  - Ownership / compliance / equipo que exige frontera de deploy
  - Tecnología o SLA incompatible en el mismo proceso
- “Por si acaso”, “más moderno” o “separar front/back” **no** bastan.
- Si dudas → **no** splits a microservicio; profundiza capas hexagonales.

## 2. Arquitectura hexagonal (puertos y adaptadores)

Todo artefacto/deployable (aunque sea monolito) debe diseñar capas lógicas explícitas:

| Capa | Contiene | No contiene |
|------|----------|-------------|
| **Domain** | Entidades, reglas, invariantes | I/O, frameworks, HTTP, DB |
| **Application** | Casos de uso / orquestación | Detalles de infra |
| **Ports** | Interfaces (entrada/salida) | Implementaciones |
| **Adapters inbound** | HTTP, CLI, jobs, UI controllers | Reglas de negocio |
| **Adapters outbound** | DB, colas, APIs externas, storage | Reglas de negocio |

Dependencias: **adapters → application/domain** (hacia adentro), nunca al revés.

En el Design Package y en paths de Build: nombrar carpetas/módulos alineados a estas capas.

## 3. Repos y deployables

- Preferir monorepo / pocos repos con módulos hexagonales.
- Multi-repo o multi-servicio ≠ obligatorio; solo si la justificación de §1 aplica.
- Build implementa las capas del DS; no inventa otro estilo.

## 4. Checklist anti-microservicio

Antes de `propose` un servicio nuevo:

- [ ] ¿Se puede resolver con módulo + puerto/adaptador en el deployable actual?
- [ ] ¿El dolor es de frontera real (escala/fallo/ownership) o solo de organización de código?
- [ ] ¿Hay ADR Accepted que lo autorice?

Si no → diseño hexagonal dentro del artefacto existente.

## 5. UI — design system org

- Default: **`@infosails/design-system`** (React + Tailwind v4 + tokens).
- El agente `ui-designer` lee **`csf.md`**, **perfiles y tipo de app del BP**, y **pregunta sabores/características** (tema, acento, densidad, features, variantes) antes de diseñar.
- Otra kit UI → solo con **ADR Accepted**.
- Ver `.cursor/skills/design/ui-designer.md` y `config/harness.yaml` → `org.design_system`.

## 6. Diagramas Mermaid

- El agente `architect` documenta **componentes** y **secuencias** en Mermaid.
- Obligatorios en landscape (sistema), ADRs con impacto estructural/flujo, y Design Packages.
- Ver `.cursor/skills/design/diagrams.md`.
