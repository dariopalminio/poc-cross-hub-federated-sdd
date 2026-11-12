# Decisions (ADR)

Registro de decisiones de arquitectura (*Architecture Decision Records*) para Blue Express
SpecKit y las soluciones construidas bajo este framework.

## Convención de nombres

```
NNNN-titulo-corto-en-kebab-case.md
```

Ejemplo: `0001-adoptar-arquitectura-hexagonal.md`.

## Cuándo crear un ADR

- Se elige entre dos o más enfoques arquitectónicos válidos.
- Se introduce una excepción a un guardrail existente (debe referenciar la aprobación según
  [constitution/governance.md](../../constitution/README.md)).
- Se reemplaza una decisión previa (el nuevo ADR debe referenciar al anterior como "supersedes").

Ver plantilla en [0000-adr-template.md](0000-adr-template.md).

## Decisiones registradas

- [0001. Ubicación canónica y vendoring gobernado del contrato OpenAPI](0001-ruta-canonica-openapi.md)
