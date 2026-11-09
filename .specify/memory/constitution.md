<!--
Sync Impact Report
Version change: 1.0.0 -> 1.1.0 (ampliacion de obligaciones de coordinacion)
Modified principles:
- III. Derivacion jerarquica trazable: manifiesto padre y origen de las specs hijas.
- IV. Contratos inmutables con propietario explicito: ruta vigente de la plantilla.
- V. Verificacion basada en evidencia: comprobacion bidireccional de metadatos.
Added sections: Mecanismo declarativo padre-hijas, dentro de Flujo y puertas de revision.
Expanded sections: Flujo y puertas de revision, con resincronizacion y cierre transversal.
Removed sections: ninguna.
Deferred items: TODO(RATIFICATION_DATE), confirmar la fecha de adopcion original.
Follow-up: alinear la plantilla de manifiesto con los campos aqui exigidos antes de
automatizar su consumo; no se modifica la plantilla ni se implementan comandos en esta enmienda.
Nota: informe temporal de revision; retirar antes de confirmar la constitucion en Git.
-->
# poc-cross-hub-federated-sdd Constitution

## Core Principles

### I. Initiative-First

Toda iniciativa (Epic) transversal DEBE tener una especificacion de epica de negocio en el Hub
antes de derivar especificaciones locales. Esta DEBE identificar el objetivo de negocio,
el alcance, los repositorios participantes y los criterios de aceptacion transversales.
Los cambios de alcance compartido DEBEN actualizar primero esa especificacion y registrar
el impacto en sus derivados. El Hub es la fuente de verdad del alcance transversal para
evitar decisiones de negocio contradictorias entre repositorios.

### II. Separacion de planos y autonomia local

El Hub DEBE contener la coordinacion estrategica: iniciativas, referencias a contratos y
orquestacion documental. El codigo de producto, la especificacion local, el plan tecnico
y las tareas de ejecucion DEBEN residir en el repositorio satelite correspondiente.
Cada satelite DEBE decidir su implementacion tecnica dentro del alcance y de los contratos
acordados; NO DEBE redefinir unilateralmente requisitos transversales ni contratos ajenos.
Esta separacion mantiene la coordinacion sin convertir el Hub en un repositorio de producto.

### III. Derivacion jerarquica trazable

Cada `spec.md` local DEBE derivarse mecanicamente de la iniciativa central mediante
punteros explicitos al repositorio, ruta y revision inmutable de origen. Su alcance DEBE
identificar los requisitos y criterios de aceptacion centrales que cubre. Cada `plan.md`
DEBE referenciar su especificacion local y cada `tasks.md`, su plan tecnico.
La spec padre DEBE mantener `links-manifest.yaml` junto a su `spec.md`; cada spec hija
DEBE mantener `origin.yaml`, sin punto inicial, junto a su propio `spec.md`.
Ambos archivos DEBEN implementar el mecanismo declarativo padre-hijas definido mas abajo.
La derivacion NO DEBE introducir requisitos transversales independientes de la iniciativa.
Una actualizacion del origen DEBE incluir una revision de impacto y la actualizacion de los
derivados afectados antes de declarar su alineacion. Los punteros permiten comprobar el
linaje sin mantener copias divergentes como fuentes de verdad alternativas.

### IV. Contratos inmutables con propietario explicito

Todo contrato compartido DEBE identificar un propietario unico mediante `owner_repo`,
un identificador, una ruta y una revision o version inmutable. El manifiesto de enlaces
DEBE registrar los contratos compartidos siguiendo la estructura de
`docs/template/links-manifest-template.yaml` y los campos exigidos por esta constitucion;
la referencia DEBE permitir resolver la revision
exacta del artefacto. El propietario DEBE mantener la definicion autoritativa del contrato;
los consumidores NO DEBEN modificarla ni sustituirla por una copia local independiente.
Para la API Backend-Frontend, `poc-be-federated-sdd` es el productor y propietario del
contrato OpenAPI y `poc-fe-federated-sdd` es su consumidor.
Una revision publicada NO DEBE alterarse: cualquier cambio DEBE producir una nueva revision.
Los cambios incompatibles DEBEN identificar consumidores afectados y un plan de migracion
acordado con ellos antes de adoptar la nueva revision.

### V. Verificacion basada en evidencia

Toda revision DEBE comprobar la trazabilidad de requisitos, la resolucion de punteros y
la coherencia de las referencias a contratos y propietarios. La revision DEBE comprobar
bidireccionalmente `links-manifest.yaml` y `origin.yaml` contra la revision central fijada;
la existencia de ambos archivos por si sola NO DEBE considerarse evidencia de alineacion.
Los cambios de contratos
DEBEN aportar evidencia de compatibilidad o declarar la incompatibilidad y su migracion.
Los comandos de verificacion DEBEN obtenerse de manifiestos, herramientas o configuracion
de CI existentes; NO DEBEN suponerse comandos de instalacion, compilacion o pruebas.
Si una comprobacion no puede ejecutarse, la revision DEBE registrar la limitacion y el
riesgo pendiente. Una comprobacion no ejecutada NO DEBE presentarse como superada.

## Limites del modelo federado

- Hub de coordinacion: `https://github.com/dariopalminio/poc-cross-hub-federated-sdd`.
- Productor Backend: `https://github.com/dariopalminio/poc-be-federated-sdd`.
- Consumidor Frontend: `https://github.com/dariopalminio/poc-fe-federated-sdd`.
- Los contratos DEBEN tener una unica fuente autoritativa en el repositorio propietario;
	el Hub DEBE coordinar sus referencias sin asumir su propiedad por alojar una iniciativa.
- Las decisiones de arquitectura y configuracion DEBEN documentarse en el README, docs o en
	documentacion especifica, con un enlace desde `AGENTS.md` en lugar de duplicar contenido.
- Esta constitucion gobierna los artefactos del Hub y sus acuerdos de federacion; NO DEBE
	interpretarse como una modificacion automatica de las constituciones de los satelites.

## Flujo y puertas de revision

### Mecanismo declarativo padre-hijas

El mecanismo se basa en el diseno de [docs/poc-borrador.md](../../docs/poc-borrador.md).
Las siguientes reglas son obligatorias para los acuerdos de derivacion del Hub; NO DEBEN
interpretarse como una automatizacion ya disponible en Spec Kit.

- **Manifiesto padre:** `specs/<iniciativa>/links-manifest.yaml` DEBE contener `schema`,
	`id`, `title`, `shared_contracts` y `links`. Su `id` DEBE identificar la iniciativa
	de la spec padre. Cada entrada de `links` DEBE declarar `role`, `repo`, `local_spec`
	y `scope`, con los IDs de requisitos centrales asignados a esa contribucion.
	Cada combinacion de `repo` y `role` DEBE identificar una unica spec hija en la iniciativa.
- **Contratos compartidos:** cada entrada de `shared_contracts` DEBE declarar `id`,
	`owner_repo`, `path` y `revision`. `revision` DEBE fijar el SHA completo del commit del
	contrato en el repositorio propietario. El Hub DEBE referenciar el contrato canonico,
	no alojar una copia autoritativa alternativa. Si no hay contratos compartidos,
	`shared_contracts` DEBE ser una lista vacia explicita.
- **Origen hijo:** `specs/<spec-local>/origin.yaml` DEBE declarar `epic_id`,
	`hub_repo`, `spec_source`, `source_revision`, `role` y `requirements`.
	`spec_source` DEBE apuntar al `spec.md` padre mediante una ruta relativa al repositorio
	del Hub y `source_revision` DEBE fijar el SHA completo del commit que contiene tanto
	esa spec como su manifiesto. `requirements` DEBE enumerar los IDs centrales cubiertos.
	Una fecha de sincronizacion o una rama mutable NO DEBE sustituir al SHA de origen.
- **Coherencia bidireccional:** al resolver el origen fijado, `epic_id` DEBE coincidir
	con el `id` del manifiesto. El repositorio de la hija, su ruta y su `role` DEBEN coincidir
	con `repo`, `local_spec` y `role` de su entrada en `links`. Los IDs de `requirements`
	DEBEN coincidir con `scope` y existir en la spec padre. La spec hija DEBE conservar esos
	IDs y los criterios de aceptacion aplicables, incluidas las dependencias de integracion.
	Los requisitos transversales nuevos DEBEN acordarse primero en la spec padre.
- **Resolucion:** las rutas de artefactos DEBEN interpretarse dentro del repositorio al que
	pertenecen, no respecto a la ubicacion de otros clones. Un clon local configurado o una
	recuperacion remota DEBE resolver los mismos artefactos en las revisiones fijadas.
	Una referencia ausente, un SHA no resoluble o una discrepancia de metadatos DEBE bloquear
	la declaracion de alineacion, aunque no exista todavia un validador automatizado.
- **Resincronizacion:** cada cambio de la spec padre, su manifiesto o un contrato DEBE
	analizar el impacto sobre las hijas afectadas. Para adoptar una nueva revision central,
	cada hija afectada DEBE revisar su spec y sus derivados y actualizar `origin.yaml` en
	el mismo cambio local. Si cambia un contrato, el manifiesto DEBE fijar su nueva revision
	y las hijas afectadas DEBEN adoptar la revision central que la referencia. El Hub DEBE
	registrar el estado de actualizacion; una hija fijada a una revision anterior NO DEBE
	presentarse como alineada con la nueva. La evidencia historica NO DEBE sobrescribirse.

### Secuencia y comprobaciones de aceptacion

1. Especificar en el Hub la iniciativa y sus criterios de aceptacion; identificar satelites,
	 responsabilidades y contratos compartidos con sus propietarios.
2. Registrar `links-manifest.yaml` junto a la spec padre y fijar las revisiones de los
	 contratos. Seleccionar el commit central que sera el origen de la derivacion.
3. Derivar en cada satelite su `spec.md` junto a `origin.yaml`; comprobar su correspondencia
	 con el manifiesto fijado antes de generar `plan.md` y despues `tasks.md`.
	 Cada transicion DEBE comprobar que no contradice el artefacto del que deriva. Si se usa IA
	 para seleccionar contenido, la derivacion DEBE incluir revision humana de fidelidad.
4. Antes de aceptar una PR, el revisor DEBE comprobar los cinco principios, los criterios
	 afectados, la coherencia padre-hijas, los contratos y la evidencia de las comprobaciones.
	 Las desviaciones DEBEN resolverse o contar con una excepcion aprobada segun Governance.
5. Ante cambios de iniciativa o contrato, registrar los repositorios y derivados afectados,
	 su estado de actualizacion y las limitaciones de verificacion. NO DEBE declararse
	 completada la iniciativa sin evidencia de sus criterios de aceptacion transversales.
6. El cierre local de una hija DEBE aportar evidencia vinculada al commit implementado,
	 a su revision de origen, a los contratos aplicables y a los requisitos cubiertos.
	 El Hub DEBE conciliar las contribuciones de `links` sin confundir cierre local con cierre
	 transversal. Todas las entregas locales son necesarias, pero NO DEBEN considerarse
	 suficientes sin evidencia de integracion y de los criterios globales acordados.

## Governance

Esta constitucion prevalece sobre instrucciones y practicas locales contradictorias del Hub.
`AGENTS.md` recoge la orientacion operativa y los comandos verificados, sin sustituir estas
reglas. Toda PR que cambie iniciativas, enlaces, contratos o documentos derivados DEBE
incluir una revision de conformidad con esta constitucion.

Una enmienda DEBE proponerse mediante PR con motivacion, reglas afectadas, impacto en
repositorios y artefactos, y plan de migracion cuando corresponda. Un mantenedor del Hub
DEBE aprobarla antes de su integracion; si cambia acuerdos compartidos, DEBE constar tambien
la conformidad de los propietarios de los repositorios afectados. La enmienda DEBE actualizar
la version y la fecha de ultima modificacion, conservando la fecha de ratificacion original.

El versionado DEBE seguir SemVer: MAJOR para eliminar o redefinir reglas de forma
incompatible, MINOR para agregar principios, secciones o ampliar obligaciones, y PATCH para
aclaraciones sin cambio de obligaciones. `1.0.0` establece la primera base normativa.

Toda excepcion DEBE documentar la regla afectada, la justificacion, el alcance, el responsable
y una fecha de expiracion o condicion verificable de cierre. DEBE contar con aprobacion
del mantenedor del Hub y de los propietarios afectados antes de aceptar el cambio.
Las excepciones NO DEBEN modificar implicitamente esta constitucion ni autorizar cambios
retroactivos en revisiones publicadas de contratos.

**Version**: 1.1.0 | **Ratified**: TODO(RATIFICATION_DATE): confirmar fecha de adopcion original | **Last Amended**: 2026-11-09
