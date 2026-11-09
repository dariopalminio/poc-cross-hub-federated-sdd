0001. Ubicación canónica y sincronización del contrato OpenAPI
Estado: APPROVED
Fecha: 2026-11-09
Autores: Equipo del Hub

## Contexto

El contrato `health-api` es propiedad del Backend. El manifiesto de la iniciativa
[`001-health-status`](../../specs/001-health-status/links-manifest.yaml) actualmente fija
la ruta `specs/001-health-endpoint/contracts/openapi.json` y la revisión Backend
`44ff3c4576062262370b4f7798b24e1eddc2abde`.

En la ubicación actual, el contrato vive dentro del directorio de una feature
(`specs/001-health-endpoint/contracts/openapi.json`). Organizar contratos de vida larga junto
a los artefactos específicos de una feature puede mezclarlos con documentación histórica,
dificultar su descubrimiento y aumentar el riesgo de dispersión y deriva. Esta observación
describe la organización actual del proyecto; no afirma un comportamiento predeterminado
de Spec Kit.

Se necesita una ubicación estable para la copia canónica y un mecanismo común que permita
al proveedor Backend y al consumidor Frontend resolver el mismo contrato y la misma revisión
sin mantener copias autoritativas alternativas.

## Decisión

Se propone que la única copia canónica del contrato `health-api` resida en el repositorio
Backend en `specs/contracts/openapi.json`. Esta ubicación y el mecanismo descrito quedan
sujetos a la aceptación del ADR por el Hub; hasta entonces, la ruta y revisión vigentes del
manifiesto continúan siendo la referencia acordada.

La sincronización federada se representará mediante los siguientes artefactos:

- **Plantillas:** `docs/templates/links-manifest-template.yaml` proporciona la base del
  manifiesto padre y debe contemplar `links`, además de `shared_contracts` con el campo
  `revision`. `docs/templates/origin-template.yaml` proporciona la estructura del `origin.yaml`
  que se copia junto a la spec de cada repositorio hijo.
- **Hub:** `links-manifest.yaml` declara el contrato en `shared_contracts` mediante `id`,
  `owner_repo`, `path` y `revision`, donde `revision` identifica el commit inmutable del
  repositorio Backend que contiene el contrato canónico. Las entradas `links` asignan
  `role`, `repo`, `local_spec` y `scope` al proveedor y al consumidor.
- **Backend proveedor:** mantiene la copia canónica en la ruta declarada, desarrolla la
  implementación contra el contrato y verifica que el servicio cumple la interfaz publicada.
  Su `origin.yaml` fija `epic_id`, `hub_repo`, `spec_source`, `source_revision`, el rol Backend
  y los requisitos que le corresponden.
- **Frontend consumidor:** referencia el contrato fijado por el manifiesto, sin copiarlo
  como definición autoritativa. Su `origin.yaml` fija los mismos campos de procedencia,
  la misma `source_revision` del Hub, el rol Frontend y su propio conjunto de requisitos.
- **Sincronización:** ambos `origin.yaml` identifican la misma `source_revision` inmutable
  del Hub; sus roles, rutas de spec e IDs de requisitos deben corresponder a sus entradas en `links`.
  El contrato consumido debe coincidir con `owner_repo`, `path` y `revision` de
  `shared_contracts` en esa revisión del Hub.

Se propone desarrollo orientado por contratos (CDD): la spec padre acuerda primero las
necesidades de la interfaz. Antes de implementar, el plan Backend identifica al propietario,
la ruta canónica y los cambios previstos al contrato. El Backend define una propuesta de
contrato y la revisa con el consumidor; una vez acordada, publica una revisión inmutable que
el Hub registra en `shared_contracts`. El plan del proveedor confirma que la implementación
seguirá esa revisión; el consumidor planifica su integración contra la misma revisión fijada.
Ambos implementan contra el contrato acordado y el proveedor valida que el servicio cumple
la interfaz publicada. Las herramientas de validación concretas se determinan a partir de
las configuraciones existentes de cada repositorio.

Este ADR define el modelo objetivo y la ubicación propuesta; no ejecuta el cambio ni modifica
el contrato, el manifiesto o los `origin.yaml`.

## Alternativas consideradas

- **Conservar el contrato bajo la spec local** (`specs/001-health-endpoint/contracts/openapi.json`):
  mantiene el artefacto junto a la feature Backend, pero hace menos explícita su condición
  de contrato canónico compartido.
- **Mantener copias en Backend y Frontend**: facilita cambios locales independientes, pero
  permite divergencias y no ofrece una revisión autoritativa única; se descarta.
- **Definir el contrato a partir de la implementación sin acordarlo primero**: puede descubrir
  diferencias tardíamente y deja al consumidor sin una interfaz estable durante su planificación.

## Consecuencias

- Una aceptación fija el contrato de salud en una ruta canónica común; cualquier modificación
  posterior se publica como una nueva revisión inmutable.
- El Hub coordina la revisión del contrato y el origen común; proveedor y consumidor conservan
  sus propios alcances mediante sus respectivos `origin.yaml`.
- Los planes y las specs referencian el contrato en vez de copiar su definición, y la validación
  comprueba la implementación del proveedor frente a la interfaz publicada.
- La ruta actualmente registrada sigue en vigor mientras este ADR permanezca como Propuesta;
  aceptar este modelo requerirá que las referencias federadas reflejen de forma coherente la
  ubicación canónica aprobada.