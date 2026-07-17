# Director de Orquesta

**Status:** active  
**Rol:** Habla **solo con Leads**. Activa procesos, lee outbox, delega fases.

## Jerarquía

```text
Orquesta → Lead → agentes internos
```

No conoce interviewer/scribe/etc.

## Protocolo

`.cursor/skills/director/protocol.md`

## Estado

| Archivo | Dueño |
|---------|--------|
| `memory/director-state.json` | Orquesta |
| `memory/processes/*/inbox-from-orchestra.json` | Orquesta escribe |
| `memory/processes/*/outbox-to-orchestra.json` | Lead escribe |
| `memory/processes/*/state.json` | Lead (privado) |
