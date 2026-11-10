# Spec Derivation

## Alternativa 1: Proceso de Spec Derivation

Inicialización y derivación trazable de la spec hija desde la padre. Abarca preparar el repositorio, fijar la revisión de la iniciativa, crear spec-origin.yaml, derivar la spec local y validar su correspondencia con el manifiesto padre.

SPEC-NAME: 001-health-endpoint
ROL: backend-health

Para crear una spec hija derivada de una spec padre seguir este flujo:

1. En este repositorio, para esta nueva spec de épica, usa los valores del manifiesto: rol [ROL] y spec local specs/
[SPEC-NAME]/spec.md. El rol del manifiesto prevalece sobre el ejemplo genérico backend-producer de la plantilla.

2. Fija una revisión del Hub que contenga tanto la spec padre como su manifiesto. En el Hub, una vez que esos cambios estén en un commit, obtén su SHA completo con git rev-parse HEAD. No uses un SHA de cambios sin confirmar.

3. Crea specs/[SPEC-NAME]/spec-origin.yaml con esos datos:

4. Crea specs/[SPEC-NAME]/spec.md derivándola de la spec padre. Incluye esos requisitos y sus criterios aplicables; no incorpores FR-* que no correspondan a este repositorio. Conserva los IDs para permitir comprobar la trazabilidad.

## Alternativa 2: Delegar proceso de Spec Derivation al speckit-specify:

Prompt: "/speckit-specify Inicialización y derivación trazable de la spec hija desde la spec padre specs\001-health-status. Abarca preparar el repositorio, fijar la revisión de la iniciativa, crear spec-origin.yaml, derivar la spec local y validar su correspondencia con el manifiesto padre."
