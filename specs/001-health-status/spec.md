# Feature Specification: Health Status

**Feature Branch**: `001-health-status`

**Created**: 2026-11-09

**Status**: Draft

**Input**: User description: "Añadir un endpoint GET /health {\"status\":\"ok\"} y una página react que muestre el status. El contrato OpenAPI debe reflejarlo."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Consultar la salud del servicio (Priority: P1)

Como consumidor del servicio, quiero consultar su estado de salud para saber si está
disponible.

**Why this priority**: La consulta del estado es el comportamiento base del que depende
su visualización en la página.

**Independent Test**: Consultar `GET /health` mientras el servicio está disponible y
comprobar la respuesta HTTP y el cuerpo JSON.

**Acceptance Scenarios**:

1. **Given** que el servicio está disponible, **When** un consumidor consulta `GET /health`,
   **Then** recibe HTTP 200 y el cuerpo JSON `{"status":"ok"}`.
2. **Given** la operación de salud definida, **When** un consumidor revisa el contrato API,
   **Then** encuentra `GET /health`, su respuesta satisfactoria y el esquema de su cuerpo.

---

### User Story 2 - Ver el estado en la página (Priority: P2)

Como usuario de la aplicación, quiero ver el estado devuelto por el servicio en una página
para comprobarlo sin realizar manualmente una llamada al endpoint.

**Why this priority**: La página facilita la consulta, pero depende de que el servicio
exponga primero el estado de forma consistente.

**Independent Test**: Abrir la página con el servicio disponible y verificar que muestra
el valor `ok` recibido del servicio.

**Acceptance Scenarios**:

1. **Given** que la página puede contactar con el servicio, **When** se carga la página,
   **Then** consulta el estado y muestra `ok` al recibirlo.
2. **Given** que la consulta no puede completarse o no devuelve una respuesta válida,
   **When** la página presenta el resultado, **Then** no muestra el servicio como saludable
   y comunica que el estado no está disponible.

### Edge Cases

- Si el servicio no está disponible, la página comunica que no pudo obtener el estado y
  no reutiliza `ok` como si fuera una respuesta actual.
- Si la respuesta no contiene el valor de estado esperado, la página no la presenta como
  una comprobación satisfactoria.
- Si la consulta está pendiente, la página no presenta un estado saludable antes de recibir
  una respuesta satisfactoria.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: El servicio MUST exponer `GET /health`.
- **FR-002**: Cuando el servicio esté disponible, `GET /health` MUST responder con HTTP 200
  y el cuerpo JSON `{"status":"ok"}`.
- **FR-003**: El contrato OpenAPI MUST describir la operación `GET /health`, la respuesta
  HTTP 200 y el esquema del cuerpo JSON, incluyendo el valor `status`.
- **FR-004**: La página MUST consultar el estado del servicio y mostrar el valor recibido
  cuando la respuesta sea satisfactoria.
- **FR-005**: La página MUST distinguir una consulta pendiente o fallida de una respuesta
  satisfactoria y MUST NOT indicar que el servicio está saludable si no recibió un resultado
  satisfactorio vigente.
- **FR-006**: El Backend MUST ser el propietario de la definición autoritativa del contrato
  OpenAPI; el Frontend MUST consumir el contrato acordado y no mantener una definición
  autoritativa independiente.

### Key Entities

- **Resultado de salud**: Estado comunicado por el servicio; para una respuesta satisfactoria
  de esta funcionalidad, su valor es `ok`.
- **Contrato de salud**: Definición compartida de la operación, su respuesta y el esquema
  del resultado, con el Backend como propietario.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: El 100% de las consultas satisfactorias de `GET /health` devuelve HTTP 200 y
  exactamente el cuerpo JSON `{"status":"ok"}`.
- **SC-002**: En todas las comprobaciones satisfactorias de la página, el valor mostrado
  coincide con el estado recibido del servicio.
- **SC-003**: En todas las comprobaciones con consulta pendiente, fallida o inválida, la
  página no comunica `ok` como estado actual del servicio.
- **SC-004**: El contrato OpenAPI permite a un consumidor identificar la operación, la
  respuesta satisfactoria y el esquema del cuerpo sin depender de documentación externa.

## Assumptions

- El significado de `ok` se limita a que el servicio puede responder a la consulta; no
  garantiza la salud de dependencias externas ni constituye monitorización integral.
- La página se integra en el Frontend React existente, conforme a la solicitud y al contexto
  del proyecto; esta especificación no prescribe estructura visual ni detalles adicionales
  de presentación.
- El Backend es el propietario del contrato OpenAPI, conforme a la constitución del Hub.
- El manifiesto de la iniciativa registrará la revisión exacta del contrato cuando el
  artefacto canónico y su commit estén disponibles. Hasta entonces, la relación con el
  contrato está pendiente y no se debe declarar alineación contractual.