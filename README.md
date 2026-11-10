# PoC: itiative-First SDD con Spec Kit Federado

## Resumen ejecutivo

Este repositorio es el Hub de coordinación del modelo federado Initiative-First con GitHub
Spec Kit. Contiene las iniciativas (épicas) transversales y sus manifiestos de enlace; la ejecución
técnica, el código y los planes locales viven en los repositorios satélite.

La iniciativa (épica) de ejemplo `001-health-status` coordina el endpoint `GET /health`, su contrato
OpenAPI y la página React que consume el estado:

- Backend proveedor: [poc-be-federated-sdd](https://github.com/dariopalminio/poc-be-federated-sdd).
- Frontend consumidor: [poc-fe-federated-sdd](https://github.com/dariopalminio/poc-fe-federated-sdd).
- Hub: [poc-cross-hub-federated-sdd](https://github.com/dariopalminio/poc-cross-hub-federated-sdd).

## Problema y hipótesis

El problema de fondo no es solo cómo configurar el workspace, sino dónde reside la autoridad
de la especificación y del cambio cuando el alcance cruza límites organizativos distintos:
varios paquetes o aplicaciones dentro de un monorepo, varios repositorios en un polyrepo, o
varios equipos funcionales como Backend y Frontend. En esos casos, una especificación puede
perder trazabilidad al resumirse manualmente, los contratos duplicados o fijados a rutas
mutables pueden divergir, y un equipo puede confundirse con el responsable del cierre de
criterios que pertenecen a la iniciativa completa.

Esta PoC aborda específicamente el escenario polyrepo y multifuncional Backend-Frontend; no
pretende resolver la organización interna de un monorepo. La hipótesis es que una spec padre
en el Hub, un `parent-spec-manifest.yaml` con alcances por rol y un `spec-origin.yaml` por spec hija
permiten mantener una fuente de verdad transversal y derivar trabajo local conservando los
IDs de requisitos y fijando revisiones inmutables de la spec y de los contratos.

### Decisión: Planificación basada en la iniciativa/épica con cambios locales en el repositorio vinculados

En iniciativas que abarcan varios equipos y repositorios, el repositorio no es por sí solo la
unidad principal de planificación: Backend y Frontend son contribuciones distintas al mismo
trabajo. Este proyecto toma la iniciativa y su spec padre como objeto de planificación; cada
repositorio mantiene su spec y cambio local, vinculados a la iniciativa por
`parent-spec-manifest.yaml` y `spec-origin.yaml`. Así se conserva la autonomía de implementación sin
fragmentar el objetivo ni perder la trazabilidad transversal.

Esta decisión se inspira en la propuesta de OpenSpec sobre planificación por iniciativa con
cambios locales vinculados: [Workspace architecture: initiative-first planning](https://github.com/Fission-AI/OpenSpec/blob/main/openspec/explorations/workspace-architecture.md#decision-initiative-first-planning-with-linked-repo-local-changes).

El Hub de producto (`bx-spec-cosmos`) es dueño de la especificación funcional global y
fuente de verdad a nivel de producto; también audita que las contribuciones derivadas sigan
alineadas. Los repositorios satélite reciben solo los requisitos y el contexto necesarios
para su alcance, implementan sus responsabilidades locales y no tienen permisos para
escribir o alterar la especificación global. Esta autoridad funcional no cambia la propiedad
de los contratos técnicos compartidos, que permanece explícita en `owner_repo`.

## Mecanismo técnico

### Estructura del ecosistem de repositorios

Orquestación de polyrepo en el Modelo Federado Initiative-First con GitHub Spec Kit.

```text
poc-cross-hub-federated-sdd/
├── .specify/
│   └── memory/
│       └── constitution.md
├── docs/
│   ├── architecture/
│   └── decisions/
└── specs/
    └── 001-health-status/
        ├── spec.md
        └── parent-spec-manifest.yaml

poc-be-federated-sdd/
├── .specify/
│   └── memory/constitution.md
├── docs/
│   ├── architecture/
│   └── decisions/
├── specs/
│   └── 001-health-endpoint/
│       ├── spec-origin.yaml
│       ├── spec.md
│       ├── plan.md
│       └── tasks.md
├── specs/contracts/openapi/
│   └── health-api.yaml
└── src/

poc-fe-federated-sdd/
├── .specify/
│   └── memory/constitution.md
├── docs/
│   ├── architecture/
│   └── decisions/
├── specs/
│   └── 001-health-page/
│       ├── spec-origin.yaml
│       ├── spec.md
│       ├── plan.md
│       └── tasks.md
└── src/
```

### Mecanismo de trazabilidad y coordinación

Los archivos de trazabilidad y coordinación entre spec padre y specs hijas son:

**parent-spec-manifest.yaml** vive junto a la spec padre del Hub (spec de iniciativa o épica). Declara cómo se reparte la spec padre: para cada satélite (spec hija) indica su repositorio, rol, ruta de spec local y requisitos asignados (scope). También puede registrar contratos compartidos con su propietario, ruta y revisión inmutable.

**spec-origin.yaml** vive junto a la spec de cada satélite (spec hija). Registra de dónde viene esa spec (spec padre): identifica la iniciativa/épica y el Hub, fija la ruta y revisión de la spec padre, y declara el rol y los requisitos que cubre esa contribución.

En conjunto permiten comprobar que cada spec hija corresponde a la iniciativa correcta y cubre exactamente el alcance asignado; por sí solos no hacen la derivación automáticamente. 


### Decisiones

#### Decisión: Identificadores de proyecto estables, no rutas

Los enlaces entre repositorios usan identificadores estables del proyecto: en esta PoC,
las URLs canónicas de GitHub de cada repositorio en los campos `repo`, `owner_repo` y
`hub_repo`. No deben guardar rutas absolutas locales ni depender de que los clones estén en
carpetas vecinas. Así, cada repositorio puede clonarse en una ubicación distinta sin romper
la identidad del enlace.

Los campos `local_spec`, `spec_source` y `path` siguen identificando archivos, pero sus
rutas se interpretan desde la raíz del repositorio correspondiente y junto con la revisión
inmutable fijada. No representan la ubicación local del clon. Esta decisión se inspira en
[OpenSpec: identificadores de proyecto estables, no rutas](https://github.com/Fission-AI/OpenSpec/blob/main/openspec/explorations/workspace-architecture.md#decision-stable-project-identifiers-not-paths).

#### Decisión: Resolución de primera instancia fuera de línea

La estrategia objetivo resuelve primero cada URL canónica del manifiesto contra clones
locales configurados para esos repositorios. Solo si la revisión requerida no está disponible
localmente se recurre a la fuente remota para recuperar el commit exacto. La resolución no
debe inferir la identidad del repositorio a partir de rutas relativas entre clones.

Esta capacidad no está implementada todavía en este Hub: no hay registro de workspaces ni
comando de derivación que haga esa resolución automáticamente. Hasta que exista, el proceso
debe recibir una ubicación local explícita o resolver manualmente la revisión remota. La
propuesta se inspira en [OpenSpec: resolución offline-first](https://github.com/Fission-AI/OpenSpec/blob/main/openspec/explorations/workspace-architecture.md#decision-offline-first-resolution).

#### Decisión: Repositorio explícito del propietario para contratos compartidos

Un contrato compartido puede servir a varios repositorios sin pertenecer en exclusiva a la
spec de ninguno de ellos. Por eso `shared_contracts` declara explícitamente `owner_repo`,
además de `id`, `path` y `revision`; el propietario identifica la única copia canónica y los
consumidores la referencian desde sus propios enlaces y `spec-origin.yaml`. El propietario no se
deduce de la ubicación de la spec ni del primer consumidor. Para `health-api`, el propietario
es `poc-be-federated-sdd`.

Esta regla se inspira en [OpenSpec: propietario explícito de contratos compartidos](https://github.com/Fission-AI/OpenSpec/blob/main/openspec/explorations/workspace-architecture.md#decision-explicit-owner-repo-for-shared-contracts).

- La spec padre define el objetivo y los requisitos transversales.
- `parent-spec-manifest.yaml` registra las specs hijas por `repo`, `role`, `local_spec` y `scope`,
	y los contratos compartidos por `id`, `owner_repo`, `path` y `revision`.
- Cada hija mantiene `spec-origin.yaml` junto a `spec.md`, con `parent_spec_id`, `hub_repo`, `spec_source`,
	`source_revision`, `role` y `requirements`. La revisión de origen identifica el commit del
	Hub que contiene la spec y el manifiesto.
- El Backend es el propietario del contrato OpenAPI; el Frontend consume la revisión fijada
	por el Hub y no mantiene una copia autoritativa alternativa.
- Para `001-health-status`, el manifiesto registra `health-api` en
	`specs/contracts/openapi/health-api.yaml`, propiedad del Backend, con revisión
	`f01c2a394824b6dd921a1287fe38ff93dda33908`. El contrato no se duplica en este Hub.
- Las nuevas revisiones canónicas OpenAPI se definen en YAML. El directorio
	`specs/contracts/asyncapi/` queda disponible para contratos AsyncAPI futuros; todavía no
	implica que exista uno.

Referencias: [spec padre](specs/001-health-status/spec.md),
[manifiesto](specs/001-health-status/parent-spec-manifest.yaml),
[constitución](.specify/memory/constitution.md) y
[ADR de contrato](docs/decisions/0001-ruta-canonica-openapi.md).

## Flujo con Spec Kit

1. En el Hub, especificar la iniciativa con `/speckit-specify` y resolver ambigüedades con
	 `/speckit-clarify`.
2. Mantener el manifiesto padre alineado con los requisitos, roles, rutas y revisiones fijadas.
3. En cada satélite, crear o actualizar `spec-origin.yaml` y derivar la spec local conservando los
	 IDs y criterios asignados por el manifiesto. La derivación requiere revisión de trazabilidad;
	 no se presupone un comando personalizado `/speckit-derive`.
4. Continuar el ciclo local con `/speckit-clarify`, `/speckit-plan`, `/speckit-tasks` y
	 `/speckit-implement`, según los comandos disponibles en cada instalación.
5. Conciliar las entregas en el Hub. El cierre local de Backend o Frontend no demuestra por sí
	 mismo que se hayan satisfecho los criterios de aceptación transversales.

Guías operativas: [derivación de specs](docs/guides/derivation-process.md) y
[registro de contratos en el manifiesto](docs/guides/registering-contract-in-manifest-process.md).

## Ventajas

- Hace visible la relación entre iniciativa, requisito central y trabajo de cada satélite.
- Permite que Backend y Frontend avancen con ciclos locales independientes, sujetos a un
	contrato y a una revisión de origen acordados.
- Reduce el riesgo de definiciones contractuales duplicadas o de consumidores que sigan
	referencias mutables.
- Distingue el avance de un repositorio del cierre de la iniciativa completa.

## Desventajas

- Añade coordinación y mantenimiento de metadatos entre Hub, Backend y Frontend.
- Una hija con revisión de origen antigua debe resincronizarse antes de declararse alineada.
- La revisión humana de requisitos y contratos sigue siendo necesaria; los punteros por sí
	solos no garantizan fidelidad ni compatibilidad.
- La automatización de derivación y conciliación no se presupone como disponible.

## Hallazgo

El manifiesto actual ya fija el contrato YAML y su revisión Backend, pero las guías de registro
y el ADR todavía contienen referencias a una etapa anterior con `openapi.json` o
`shared_contracts: []`. Esos documentos deben reconciliarse con el manifiesto vigente antes de
usarse como instrucciones operativas. La spec padre también contiene una suposición anterior
que afirma que la referencia contractual está pendiente.

## Qué se validó

- Se validó la viabilidad de trabajar con Contract Driven Development (CDD): el contrato
	compartido tiene un propietario Backend, una ruta canónica y una revisión inmutable que el
	Hub registra para el consumo del Frontend.
- Se validó el uso de Spec Kit en un flujo polyrepo con una spec de iniciativa (Epic) en el
	Hub y specs hijas por repositorio satélite, enlazadas mediante el manifiesto y `spec-origin.yaml`.
- Se comprobó que el manifiesto declara los roles `backend-health` y `frontend-health`, sus
	rutas locales y sus alcances de requisitos.
- Se comprobó que `shared_contracts` contiene el propietario Backend, la ruta YAML canónica y
	una revisión SHA completa.
- Se comprobó que la ruta del contrato no existe dentro del Hub; aquí se registra la referencia,
	no una copia del contrato.
- Estas validaciones confirman el flujo de trabajo y la coordinación documental; no demuestran
	por sí solas la integración funcional del endpoint con la página React.
- No se ejecutaron pruebas ni compilaciones Backend-Frontend desde este Hub. No se declaran
	comandos de ese tipo en los manifiestos o en la configuración CI disponible aquí.
