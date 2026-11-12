0002. Vendoring gobernado con lockfile criptográfico
Estado: Propuesta
Fecha: 2026-11-12
Autores: Equipo del Hub

## Contexto

El ADR 0001 establece una única copia canónica del contrato OpenAPI en el repositorio
Backend y fija su ubicación y revisión mediante el manifiesto del Hub. El consumidor
Frontend, sin embargo, necesita disponer del contrato en su propio repositorio para
desarrollar y revisar cambios junto con su código. Una copia local sin procedencia ni
verificación puede separarse silenciosamente de la revisión aprobada.

Se busca conservar la autonomía de desarrollo y la visibilidad de los cambios en los
pull requests, manteniendo una relación comprobable entre la copia del consumidor y el
contrato canónico.

## Decisión

El consumidor mantendrá una copia vendorizada del contrato junto a un archivo
`contracts.lock.json`. La copia local es un artefacto derivado para consumo: no reemplaza
ni convierte en autoritativa la copia canónica del repositorio propietario declarada por
`shared_contracts`.

Cada entrada del lockfile identificará el contrato y registrará, como mínimo, el
repositorio propietario, la ruta canónica, el Git SHA completo que fija la revisión de
origen, la ruta de la copia local y el checksum SHA-256 del contenido original en esa
revisión. El checksum se calcula sobre los bytes del archivo, sin normalización ni
reescritura del formato.

La CI del consumidor verificará el lockfile y calculará el SHA-256 de cada copia local
antes de compilar. Si el checksum local no coincide con el declarado, la compuerta falla
y bloquea la compilación. Al actualizar el contrato, el cambio debe incluir la nueva copia
y la actualización correspondiente de la revisión y del checksum en el mismo pull request;
el diff resultante queda visible para revisión.

El lockfile es un registro versionado y sujeto a revisión. La comprobación de CI garantiza
que la copia local coincide con el checksum declarado, pero por sí sola no autentica una
actualización coordinada de la copia y el lockfile ni sustituye la revisión de su
procedencia. La validación de la correspondencia entre el checksum declarado y el archivo
canónico en el Git SHA registrado debe ejecutarse al generar o actualizar el lockfile, o
en CI cuando esta pueda resolver el repositorio de origen.

## Alternativas consideradas

- **Consumir el contrato directamente desde el repositorio propietario:** evita duplicar
  el archivo, pero acopla el desarrollo y la compilación del consumidor a la disponibilidad
  del repositorio remoto y hace menos visible el cambio contractual en sus pull requests.
- **Copiar el contrato sin lockfile ni compuerta:** es sencillo y permite trabajar en local,
  pero no detecta automáticamente una copia desactualizada o modificada respecto de la
  revisión acordada.
- **Usar solo una referencia Git sin copia local:** conserva la revisión de origen, pero
  no proporciona el diff local del contrato ni la disponibilidad autónoma buscada durante
  el desarrollo.

## Consecuencias

- El consumidor obtiene una copia local apta para el desarrollo y los diffs de pull request,
  con procedencia registrada mediante un Git SHA completo y un SHA-256.
- La compilación queda bloqueada si el contenido vendorizado deriva del checksum fijado en
  `contracts.lock.json`.
- Las actualizaciones requieren cambiar conjuntamente la copia y el lockfile, y revisar
  ambos cambios. El propietario y la revisión canónica siguen gobernados por
  `shared_contracts` en el Hub.
- El checksum no es una firma digital: la confianza en una actualización depende de la
  revisión del cambio y de verificar que su checksum corresponde al contenido canónico en
  la revisión de origen registrada.
- Este ADR define la estrategia, no implementa el lockfile, el proceso de actualización ni
  el paso de CI. Su esquema concreto y comandos deberán acordarse en los repositorios que
  incorporen esas herramientas.