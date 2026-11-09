# Directrices para agentes

## Propósito del proyecto

 Implementar la estrategia Initiative-First / Modelo Federado y Derivación Jerárquica de Especificaciones sobre un ecosistema polyrepo mediante GitHub Spec Kit, donde una Iniciativa Central (spec Épica de negocio transversal) vive en un Hub de Coordinación (poc-cross-hub-federated-sdd) y cada repositorio de código (poc-fe-federated-sdd, poc-be-federated-sdd) deriva mecánicamente su propia especificación local (spec.md), y sus documentos derivados como su plan técnico (plan.md) y su desglose de ejecución (tasks.md), gobernados por contratos de datos inmutables con dueño explícito.

## La aplicación

La aplicación consta de un frontend en react y un backend que se cordinan con un contrato OpenApi.

## Convenciones generales

Topología de Repositorios y Separación de Planos
Se desacopla formalmente el Plano de Coordinación Estratégico del Plano de Ejecución Técnico:

```
┌────────────────────────────────────────────────────────────────────────┐
│                   HUB CENTRAL DE INICIATIVAS                           │
│   (poc-cross-hub-federated-sdd: specs transversales, contratos y orquestación)   │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Derivación Declarativa (Punteros)
          ┌─────────────────────────┴─────────────────────────┐
          ▼                                                   ▼
┌──────────────────────────────────┐        ┌──────────────────────────────────┐
│  REPO SATÉLITE 1 (Producer)      │        │  REPO SATÉLITE 2 (Consumer)      │
│  poc-be-federated-sdd               │◄───────┤  poc-fe-federated-sdd                │
│  - Dueño del contrato API        │  Open  │  - Consume contrato API          │
│  - Spec, Plan y Tasks locales    │  API   │  - Spec, Plan y Tasks locales    │
│  - Código fuente Backend         │        │  - Código fuente Frontend        │
└──────────────────────────────────┘        └──────────────────────────────────┘
```

### Repositorios

Hub: https://github.com/dariopalminio/poc-cross-hub-federated-sdd
Frontend: https://github.com/dariopalminio/poc-fe-federated-sdd
Backend: https://github.com/dariopalminio/poc-be-federated-sdd

## Verificación

- Actualmente no hay ningún manifiesto de dependencias, scripts de compilación, conjunto de pruebas ni configuración de integración continua (CI). No des por hecho que existen comandos de instalación, compilación, análisis estático o ejecución de pruebas.
- Cuando se incorporen herramientas, determina los comandos de verificación a partir de sus manifiestos y de la configuración de CI; informa de las comprobaciones que no se hayan podido ejecutar.

## Documentación

- Documenta la configuración y la arquitectura acordadas en el README o en documentación específica y añade aquí un enlace en lugar de duplicar el contenido.
- Actualiza estas instrucciones cuando se establezcan las herramientas y las convenciones; sustituye las indicaciones propias del estado inicial del proyecto por comandos y referencias verificados.