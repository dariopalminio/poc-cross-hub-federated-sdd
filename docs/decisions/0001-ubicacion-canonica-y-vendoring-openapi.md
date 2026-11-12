0001. Ubicación canónica y vendoring gobernado del contrato OpenAPI
Estado: APPROVED
Fecha: 2026-11-12
Autores: Equipo del Hub

## Contexto

El contrato `health-api` es propiedad del Backend. El manifiesto de la iniciativa
[`001-health-status`](../../specs/001-health-status/parent-spec-manifest.yaml) debe fijar
la ruta del contrato en el repositorio proveedor y una revisión inmutable que lo contenga.
El manifiesto y los repositorios deben alinearse con esta decisión antes de considerar
completada su adopción.

Los contratos de API deben definirse primero en el repositorio proveedor, antes de
implementar el servicio. Tanto el proveedor como cada consumidor necesitan disponer del
archivo localmente para desarrollar y revisar cambios, por lo que se requiere una ubicación
predecible y una relación verificable entre la copia maestra y las copias derivadas.

## Decisión

Los contratos OpenAPI se definirán siguiendo API First: el proveedor publicará y acordará
el contrato antes de implementar el servicio. La copia local del proveedor será la copia
maestra y residirá en `specs/contracts/openapi/<contract-name>.yaml`; todos los contratos
OpenAPI deben serializarse en YAML y usar esta ruta. El proveedor es dueño de la definición
y publica cambios mediante nuevas revisiones inmutables.

Tanto el proveedor como cada consumidor mantendrán una copia local en
`specs/contracts/openapi/<contract-name>.yaml`. La copia local del proveedor es la maestra;
la del consumidor es vendorizada, debe corresponder al mismo contenido y no se convierte en
una fuente autoritativa alternativa.

El manifiesto del Hub declarará la copia maestra mediante `owner_repo`,
`path: specs/contracts/openapi/<contract-name>.yaml` y `revision`, que identifica el commit
completo e inmutable del proveedor que contiene el contrato. La estructura permite reservar
`specs/contracts/asyncapi/` para futuros contratos AsyncAPI; esta decisión no presupone que
ya exista alguno.

Cada consumidor mantendrá junto a sus contratos vendorizados un `contracts.lock.json`.
Cada entrada identificará el contrato y registrará, como mínimo, el repositorio propietario,
la ruta de la copia maestra, el Git SHA completo que fija la revisión de origen, la ruta de
la copia local y el checksum SHA-256 del contenido original en esa revisión. El checksum se
calculará sobre los bytes del archivo, sin normalización ni reescritura del formato.

Antes de compilar, la CI del consumidor calculará el SHA-256 de cada copia local y lo
comparará con el valor registrado en el lockfile. Una discrepancia debe fallar la compuerta
y bloquear la compilación. Toda actualización incluirá en el mismo pull request la nueva
copia vendorizada y la actualización de la revisión y del checksum, de modo que el diff
quede visible para revisión.

El lockfile es un registro versionado y sujeto a revisión. La compuerta garantiza que la
copia local coincide con el checksum declarado, pero por sí sola no autentica una
actualización coordinada de la copia y el lockfile ni sustituye la revisión de procedencia.
Al generar o actualizar el lockfile, se debe verificar que el checksum corresponde al
contenido de la copia maestra en el Git SHA registrado; esta verificación también puede
ejecutarse en CI si esta puede resolver el repositorio de origen. Un checksum no es una
firma digital.

La sincronización federada se representará mediante los siguientes artefactos:

- **Plantillas:** `docs/templates/parent-spec-manifest-template.yaml` proporciona la base del
  manifiesto padre y debe contemplar `links`, además de `shared_contracts` con el campo
  `revision`. `docs/templates/spec-origin-template.yaml` proporciona la estructura del `spec-origin.yaml`
  que se copia junto a la spec de cada repositorio hijo.
- **Hub:** `parent-spec-manifest.yaml` declara el contrato en `shared_contracts` mediante `id`,
  `owner_repo`, `path` y `revision`, donde `revision` identifica el commit inmutable del
  repositorio proveedor que contiene el contrato maestro. `path` debe usar
  `specs/contracts/openapi/<contract-name>.yaml`. Las entradas `links` asignan
  `role`, `repo`, `local_spec` y `scope` al proveedor y al consumidor.
- **Proveedor:** define primero el contrato, mantiene la copia maestra en la ruta declarada
  e implementa el servicio contra esa interfaz. Su `spec-origin.yaml` fija `parent_spec_id`,
  `hub_repo`, `spec_source`, `source_revision`, su rol y sus requisitos.
- **Consumidor:** mantiene una copia local vendorizada del contrato en la misma ruta relativa,
  sincronizada con la revisión maestra y verificada mediante `contracts.lock.json` y la
  compuerta de CI. Su
  `spec-origin.yaml` fija los mismos campos de procedencia, la misma `source_revision` del Hub,
  su rol y sus propios requisitos.
- **Sincronización:** ambos `spec-origin.yaml` identifican la misma `source_revision` inmutable
  del Hub; sus roles, rutas de spec e IDs de requisitos deben corresponder a sus entradas en `links`.
  La copia local del consumidor debe coincidir byte a byte con el contrato de `owner_repo` y
  `path` en la revisión `revision` de `shared_contracts`.

El desarrollo se orientará por contratos (CDD): la spec padre acuerda primero las
necesidades de la interfaz. Antes de implementar, el plan del proveedor identifica el
propietario, la ruta maestra y los cambios previstos. El proveedor define el contrato y lo
revisa con el consumidor; una vez acordado, publica una revisión inmutable que el Hub registra
en `shared_contracts`. El proveedor implementa contra esa revisión y valida que el servicio
cumple la interfaz; cada consumidor actualiza su copia local y planifica su integración contra
la misma revisión. Las herramientas de validación concretas se determinan a partir de las
configuraciones de cada repositorio.

Este ADR define el modelo objetivo y la ubicación propuesta; no ejecuta el cambio ni modifica
el contrato, el manifiesto o los `spec-origin.yaml`.

## Alternativas consideradas

- **Ubicar el contrato junto a una feature:** mezcla un contrato compartido y de vida larga
  con artefactos específicos de una feature, y dificulta mantener una ruta estable.
- **Consumir el contrato remoto sin copia local:** evita mantener una copia derivada, pero
  reduce la disponibilidad local y la visibilidad directa de los cambios en el pull request
  del consumidor.
- **Copiar el contrato sin lockfile ni compuerta:** permite trabajar localmente, pero no
  detecta automáticamente una copia modificada o desactualizada respecto del checksum
  declarado.
- **Usar solo una referencia Git sin copia local:** conserva la revisión de origen, pero no
  ofrece el diff del contrato dentro del repositorio consumidor ni su disponibilidad autónoma.
- **Definir el contrato a partir de la implementación (API Last):** puede descubrir
  diferencias tardíamente y deja al consumidor sin una interfaz estable durante su planificación.

## Consecuencias

- El proveedor mantiene la fuente maestra y cada proveedor y consumidor mantiene el archivo
  local en `specs/contracts/openapi/<contract-name>.yaml`; solo la copia del proveedor es
  autoritativa.
- El contrato se acuerda antes de implementar. Cualquier modificación posterior se publica
  como una nueva revisión inmutable y se sincroniza con las copias consumidoras.
- El Hub coordina la revisión del contrato y el origen común; proveedor y consumidor conservan
  sus propios alcances mediante sus respectivos `spec-origin.yaml`.
- Los planes y las specs fijan la procedencia común. El `contracts.lock.json` y la compuerta
  de CI verifican que la copia local del consumidor corresponde al checksum declarado;
  la validación del proveedor comprueba la implementación frente al contrato maestro.
- El cambio de un contrato queda visible en el pull request consumidor junto con su nueva
  revisión de origen y checksum. El lockfile no demuestra por sí solo la autenticidad del
  origen, por lo que la revisión y la verificación contra la copia maestra siguen siendo
  necesarias.
- Adoptar esta decisión requiere alinear el manifiesto del Hub y las copias locales con la
  ruta YAML y la revisión inmutable del proveedor; este ADR no ejecuta esa migración.