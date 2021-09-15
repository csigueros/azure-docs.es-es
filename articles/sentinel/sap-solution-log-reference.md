---
title: 'Solución Azure Sentinel para SAP: referencia de registros disponibles | Microsoft Docs'
description: Obtenga información sobre los registros de SAP disponibles en la solución Azure Sentinel para SAP.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 07/21/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: a1511581e3cc80d5f2f71bb82dc1bcb0ba639acb
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681519"
---
# <a name="azure-sentinel-sap-solution-logs-reference-public-preview"></a>Referencia sobre de los registros de la solución Azure Sentinel para SAP (versión preliminar pública)

En este artículo se describen los registros de SAP disponibles en el conector de datos de Azure Sentinel para SAP, entre los que se incluyen los nombres de tabla de Azure Sentinel, los fines del registro y los esquemas de registro detallados. Las descripciones de los campos de los esquemas se basan en las descripciones de los campos de la [documentación de SAP](https://help.sap.com/) pertinente.

Este artículo está destinado a usuarios de SAP avanzados.

> [!NOTE]
> Si se usa la interfaz de XBP 3.0, la solución de Azure Sentinel para SAP usa servicios *no publicados.* Estos servicios no afectan al comportamiento del conector ni del sistema back-end.
>
> Para "publicar" estos servicios, implemente las [funciones XBP sin publicar que encontrará en SAP Note 2910263](https://launchpad.support.sap.com/#/notes/2910263).

> [!IMPORTANT]
> La solución Azure Sentinel para SAP está actualmente en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="abap-application-log"></a>Registro de aplicaciones de ABAP

- **Nombre en Azure Sentinel**: `ABAPAppLog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcc9f36611d3a6510000e835363f.html)

- **Fin del registro**: registra el progreso de la ejecución de una aplicación para que pueda reconstruirla más adelante si fuera necesario.

    Disponible mediante RFC con un servicio personalizado basado en servicios estándar de la interfaz de XBP. Este registro se genera por cliente.


### <a name="abapapplog_cl-log-schema"></a>Esquema del registro ABAPAppLog_CL

| Campo                 | Descripción                    |
| --------------------- | ------------------------------ |
| AppLogDateTime        | Fecha y hora del registro de aplicaciones      |
| CallbackProgram       | Programa de devolución de llamada               |
| CallbackRoutine       | Rutina de devolución de llamada               |
| CallbackType          | Tipo de devolución de llamada                  |
| ClientID              | Identificador de cliente de ABAP (MANDT)         |
| ContextDDIC           | Estructura de DDIC de contexto         |
| ExternalID            | Identificador de registro externo                |
| administrador de flujos de trabajo                  | administrador de flujos de trabajo                           |
| Instancia              | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>`              |
| InternalMessageSerial | Serie de mensajes de registro de aplicaciones |
| LevelofDetail         | Nivel de detalle                |
| LogHandle             | Manipulador de registro de aplicaciones         |
| LogNumber             | Número de registro                     |
| MessageClass          | Clase Message                  |
| MessageNumber         | Número de mensaje                 |
| MessageText           | Texto del mensaje                   |
| MessageType           | Tipo de mensaje                   |
| Object                | Objeto registro de aplicaciones         |
| OperationMode         | Modo de operación                 |
| ProblemClass          | Clase de problema                  |
| ProgramName           | Nombre de programa                   |
| SortCriterion         | Criterio de ordenación                 |
| StandardText          | Texto estándar                  |
| SubObject             | Objeto secundario del registro de aplicaciones     |
| SystemID              | Identificador del sistema                      |
| SystemNumber          | Número del sistema                  |
| TransactionCode       | Código de transacción               |
| Usuario                  | Usuario                           |
| UserChange            | Cambio de usuario                    |
| | |



## <a name="abap-change-documents-log"></a>Registro de documentos de cambio de ABAP

- **Nombre en Azure Sentinel**: `ABAPChangeDocsLog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/6f51f5216c4b10149358d088a0b7029c/7.01.22/en-US/b8686150ed102f1ae10000000a44176f.html)

- **Fin del registro**: registros:

    - ABAP del servidor de aplicaciones de SAP NetWeaver registra en los cambios en los objetos de datos empresariales en los documentos de los cambios.

    - Otras entidades del sistema SAP, como datos de usuario, roles o direcciones.

    Disponible mediante RFC con un servicio personalizado basado en servicios estándar. Este registro se genera por cliente.

### <a name="abapchangedocslog_cl-log-schema"></a>Esquema de registro ABAPChangeDocsLog_CL


| Campo                    | Descripción                 |
| ------------------------ | ---------------------------- |
| ActualChangeNum          | Número de cambio real         |
| ChangedTableKey          | Clave de tabla cambiada            |
| ChangeNumber             | Número de cambio                |
| ClientID                 | Identificador de cliente de ABAP (MANDT)       |
| CreatedfromPlannedChange | Creado a partir de un cambio planeado, con la siguiente sintaxis: `(‘X’ , ‘ ‘)`|
| CurrencyKeyNew           | Clave de moneda: nuevo valor      |
| CurrencyKeyOld           | Clave de moneda: valor anterior      |
| FieldName                | Nombre del campo                   |
| FlagText                 | Texto de la marca                    |
| administrador de flujos de trabajo                     | administrador de flujos de trabajo                         |
| Instancia                 | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>` |
| Lenguaje                 | Lenguaje                     |
| ObjectClass              | Clase de objeto, como `BELEG`, `BPAR`, `PFCG`, `IDENTITY` |
| ObjectID                 | Identificador de objeto  |
| PlannedChangeNum         | Número de cambio planeado  |
| SystemID                 | Identificador del sistema    |
| SystemNumber             | Número del sistema     |
| TableName                | Nombre de la tabla        |
| TransactionCode          | Código de transacción   |
| TypeofChange_Header      | Tipo de encabezado del cambio, lo que incluye: <br>`U` = Cambiar; `I` = Insertar; `E` = Eliminar documento individual; `D` = Eliminar; `J` = Insertar documento individual |
| TypeofChange_Item        | Tipo de elemento del cambio, lo que incluye: <br>`U` = Cambiar; `I` = Insertar; `E` = Eliminar documento individual; `D` = Eliminar; `J` = Insertar documento individual |
| UOMNew                   | Unidad de medida: nuevo valor  |
| UOMOld                   | Unidad de medida: valor anterior |
| Usuario                     | Usuario  |
| ValueNew                 | Contenido del campo: nuevo valor |
| ValueOld                 | Contenido del campo: valor anterior |
| Versión                  | Versión          |
| | |

## <a name="abap-cr-log"></a>Registro de CR de ABAP

- **Nombre en Azure Sentinel**: `ABAPCRLog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd5f36611d3a6510000e835363f.html)

- **Fin del registro**: incluye los registros de Change & Transport System (CTS), incluidos los objetos de directorio y las personalizaciones en que se realizaron los cambios.

    Disponible mediante RFC con un servicio personalizado basado en tablas y servicios estándar. Este registro se genera con datos en todos los clientes.

> [!NOTE]
> Además del registro de aplicaciones, los documentos de cambio y la grabación de tablas, todos los cambios que realice en el sistema de producción mediante Change & Transport System se documentan en los registros de CTS y TMS.
>


### <a name="abapcrlog_cl-log-schema"></a>Esquema de registro ABAPCRLog_CL

| Campo        | Descripción                       |
| ------------ | --------------------------------- |
| Categoría     | Categoría (Workbench, Personalización) |
| ClientID     | Identificador de cliente de ABAP (MANDT)            |
| Descripción  | Descripción                       |
| Host         | administrador de flujos de trabajo                              |
| Instancia     | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>` |
| ObjectName   | Nombre del objeto                       |
| ObjectType   | Tipo de objeto                       |
| Propietario        | Propietario                             |
| Solicitud      | Solicitud de cambio                    |
| Estado       | Estado                            |
| SystemID     | Identificador del sistema                         |
| SystemNumber | Número del sistema                     |
| TableKey     | Clave de tabla                         |
| TableName    | Nombre de la tabla                        |
| ViewName     | Nombre de la vista                         |
| | |

## <a name="abap-db-table-data-log"></a>Registro de datos de tabla de base de datos de ABAP

- **Nombre en Azure Sentinel**: `ABAPTableDataLog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd2f36611d3a6510000e835363f.html)

- **Fin del registro**: proporciona registro para las tablas que son críticas o susceptibles a auditorías.

    Disponible mediante RFC con un servicio personalizado. Este registro se genera con datos en todos los clientes.

### <a name="abaptabledatalog_cl-log-schema"></a>Esquema de registro ABAPTableDataLog_CL

| Campo            | Descripción                           |
| ---------------- | ------------------------------------- |
| DBLogID          | Id. de registro de base de datos                             |
| administrador de flujos de trabajo             | administrador de flujos de trabajo                                  |
| Instancia         | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>` |
| Lenguaje         | Lenguaje                              |
| LogKey           | Clave de registro                               |
| NewValue         | Nuevo valor del campo                       |
| OldValue         | Valor anterior del campo                       |
| OperationTypeSQL | Tipo de operación, `Insert`, `Update`, `Delete` |
| Programa          | Nombre de programa                          |
| SystemID         | Identificador del sistema                             |
| SystemNumber     | Número del sistema                         |
| TableField       | Campo de tabla                           |
| TableName        | Nombre de la tabla                            |
| TransactionCode  | Código de transacción                      |
| UserName         | Usuario                                  |
| VersionNumber    | Número de la versión                        |
| | |

## <a name="abap-gateway-log"></a>Registro de puerta de enlace de ABAP

- **Nombre en Azure Sentinel**: `ABAPOS_GW_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/62b4de4187cb43668d15dac48fc00732/7.5.7/en-US/48b2a710ca1c3079e10000000a42189b.html)

- **Fin del registro:** supervisa las actividades de la puerta de enlace. Disponible por el servicio web de control de SAP. Este registro se genera con datos en todos los clientes.

### <a name="abapos_gw_cl-log-schema"></a>Esquema de registro ABAPOS_GW_CL

| Campo        | Descripción      |
| ------------ | ---------------- |
| Host         | administrador de flujos de trabajo             |
| Instancia     | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | Texto del mensaje     |
| severity     | Gravedad del mensaje: `Debug`, `Info`, `Warning`, `Error`  |
| SystemID     | Identificador del sistema        |
| SystemNumber | Número del sistema    |
| | |

## <a name="abap-icm-log"></a>Registro de ICM de ABAP

- **Nombre en Azure Sentinel**: `ABAPOS_ICM_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/683d6a1797a34730a6e005d1e8de6f22/7.52.4/en-US/a10ec40d01e740b58d0a5231736c434e.html)

- **Fin del registro**: registra las solicitudes entrantes y salientes, y compila las estadísticas de las solicitudes HTTP.

    Disponible por el servicio web de control de SAP. Este registro se genera con datos en todos los clientes.

### <a name="abapos_icm_cl-log-schema"></a>Esquema de registro ABAPOS_ICM_CL

| Campo        | Descripción      |
| ------------ | ---------------- |
| Host         | administrador de flujos de trabajo             |
| Instancia     | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | Texto del mensaje     |
| severity     | Gravedad del mensaje, se incluyen: `Debug`, `Info`, `Warning`, `Error`   |
| SystemID     | Identificador del sistema        |
| SystemNumber | Número del sistema    |
| | |

## <a name="abap-job-log"></a>Registro de trabajos de ABAP

- **Nombre en Azure Sentinel**: `ABAPJobLog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/b07e7195f03f438b8e7ed273099d74f3/7.31.19/en-US/4b2bc0974c594ba2e10000000a42189c.html)

- **Propósito del registro**: combina todos los registros de trabajos de procesamiento en segundo plano (SM37).

    Disponible mediante RFC con un servicio personalizado basado en servicios estándar de la interfaz de XBP. Este registro se genera con datos en todos los clientes.

### <a name="abapjoblog_cl-log-schema"></a>Esquema de registro ABAPJobLog_CL


| Campo               | Descripción                      |
| ------------------- | -------------------------------- |
| ABAPProgram         | Programa ABAP                     |
| BgdEventParameters  | Parámetros de eventos en segundo plano      |
| BgdProcessingEvent  | Evento de procesamiento en segundo plano      |
| ClientID            | Identificador de cliente de ABAP (MANDT)           |
| DynproNumber        | Número de Dynpro                    |
| GUIStatus           | Estado de GUI                       |
| administrador de flujos de trabajo                | administrador de flujos de trabajo                             |
| Instancia            | Instancia de ABAP(HOST_SYSID_SYSNR), con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>` |
| JobClassification   | Clasificación del trabajo               |
| JobCount            | Recuento de trabajos                        |
| JobGroup            | Grupo de trabajo                        |
| JobName             | Nombre del trabajo                         |
| JobPriority         | Prioridad del trabajo                     |
| MessageClass        | Clase Message                    |
| MessageNumber       | Número de mensaje                   |
| MessageText         | Texto del mensaje                     |
| MessageType         | Tipo de mensaje                     |
| ReleaseUser         | Usuario de versión del trabajo                 |
| SchedulingDateTime  | Fecha y hora de programación             |
| StartDateTime       | Fecha y hora de inicio                  |
| SystemID            | Identificador del sistema                        |
| SystemNumber        | Número del sistema                    |
| TargetServer        | Servidor de destino                    |
| Usuario                | Usuario                             |
| UserReleaseInstance | Instancia de ABAP: versión del usuario     |
| WorkProcessID       | Identificador de proceso de trabajo                  |
| WorkProcessNumber   | Número de proceso de trabajo              |
| | |

## <a name="abap-security-audit-log"></a>Registro de auditoría de seguridad de ABAP

- **Nombre en Azure Sentinel**: `ABAPAuditLog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/280f016edb8049e998237fcbd80558e7/7.5.7/en-US/4d41bec4aa601c86e10000000a42189b.html)

- **Fin del registro**: registra los siguientes datos:

    - Cambios relacionados con la seguridad en el entorno del sistema SAP, como por ejemplo, los cambios en los registros del usuario principal
    - Información que proporciona un mayor nivel de datos, como los intentos de inicio de sesión correctos e infructuosos
    - Información que permite la reconstrucción de una serie de eventos, como los inicios de transacciones correctos o incorrectos

    Disponible mediante el uso de interfaces XAL/SAL de RFC. SAL está disponible a partir de la versión Basis 7.50. Este registro se genera con datos en todos los clientes.

### <a name="abapauditlog_cl-log-schema"></a>Esquema de registro ABAPAuditLog_CL

| Campo                      | Descripción                     |
| -------------------------- | ------------------------------- |
| ABAPProgramName            | Nombre del programa, solo SAL                    |
| AlertSeverity              | Gravedad de la alerta                  |
| AlertSeverityText          | Texto de gravedad de alerta, solo SAL             |
| AlertValue                 | Valor de alerta                     |
| AuditClassID               | ID de clase de auditoría, solo SAL                 |
| ClientID                   | Identificador de cliente de ABAP (MANDT)          |
| Computer                   | Equipo de usuario, solo SAL                   |
| Correo electrónico                      | Correo electrónico del usuario                      |
| administrador de flujos de trabajo                       | administrador de flujos de trabajo                                                   |
| Instancia                   | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>`                  |
| MessageClass               | Clase Message                   |
| MessageContainerID         | Identificador de contenedor de mensajes, solo XAL            |
| MessageID                  | Identificador de mensaje, como `‘AU1’,’AU2’…`                     |
| MessageText                | Texto del mensaje                    |
| MonitoringObjectName       | Nombre de objeto MTE Monitor, solo XAL        |
| MonitorShortName           | Nombre corto de MTE Monitor, solo XAL          |
| SAPProcesType              | Registro del sistema: tipo de proceso de SAP, solo SAL    |
| B*: procesamiento en segundo plano |                                 |
| D*: procesamiento de cuadros de diálogo     |                                 |
| U*: Actualizar tareas         |                                 |
| SAPWPName                  | Registro del sistema: número de proceso de trabajo, solo SAL |
| SystemID                   | Identificador del sistema                       |
| SystemNumber               | Número del sistema                   |
| TerminalIPv6               | IP de la máquina del usuario, solo SAL |
| TransactionCode            | Código de transacción, solo SAL |
| Usuario                       | Usuario                            |
| Variable1                  | Variable de mensaje 1              |
| Variable2                  | Variable de mensaje 2              |
| Variable3                  | Variable de mensaje 3              |
| Variable4                  | Variable de mensaje 4              |
| | |

## <a name="abap-spool-log"></a>Registro de spool de ABAP

- **Nombre en Azure Sentinel**: `ABAPSpoolLog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae791c40f72045e10000000a421937.html)

- **Fin del registro**: sirve como registro principal para la impresión de SAP con el historial de solicitudes de spool. (SP01).

    Disponible mediante RFC con un servicio personalizado basado en tablas estándar. Este registro se genera con datos en todos los clientes.

### <a name="abapspoollog_cl-log-schema"></a>Esquema de registro ABAPSpoolLog_CL

| Campo                               | Descripción                                |
| ----------------------------------- | ------------------------------------------ |
| ArchiveStatus                       | Estado de archivo                             |
| ArchiveType                         | Tipo de archivo                               |
| ArchivingDevice                     | Dispositivo de archivo                           |
| AutoRereoute                        | Reenrutamiento automático                              |
| ClientID                            | Identificador de cliente de ABAP (MANDT)                     |
| CountryKey                          | Clave de país                                |
| DeleteSpoolRequestAuto              | Eliminación automática de la solicitud de spool                  |
| DelFlag                             | Marca de eliminación                              |
| department                          | department                                 |
| DocumentType                        | Tipo de documento                              |
| ExternalMode                        | Modo externo                              |
| Tipo de formato                          | Tipo de formato                                |
| administrador de flujos de trabajo                                | administrador de flujos de trabajo                                       |
| Instancia                            | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>` |
| NumofCopies                         | Número de copias                           |
| OutputDevice                        | Dispositivo de salida                              |
| PrinterLongName                     | Nombre largo de impresora                          |
| PrintImmediately                    | Imprimir inmediatamente                          |
| PrintOSCoverPage                    | Imprimir página de OSCover                         |
| PrintSAPCoverPage                   | Imprimir página de SAPCover                        |
| Priority                            | Priority                                   |
| RecipientofSpoolRequest             | Destinatario de solicitud de spool                 |
| SpoolErrorStatus                    | Estado de error de spool                         |
| SpoolRequestCompleted               | Solicitud de spool completada                    |
| SpoolRequestisALogForAnotherRequest | La solicitud de spool es un registro para otra solicitud |
| SpoolRequestName                    | Nombre de solicitud de spool                         |
| SpoolRequestNumber                  | Número de solicitud de spool                       |
| SpoolRequestSuffix1                 | Sufijo 1 de solicitud de spool                      |
| SpoolRequestSuffix2                 | Sufijo 2 de solicitud de spool                      |
| SpoolRequestTitle                   | Título de solicitud de spool                        |
| SystemID                            | Identificador del sistema                                  |
| SystemNumber                        | Número del sistema                              |
| TelecommunicationsPartner           | Asociado de telecomunicaciones                 |
| TelecommunicationsPartnerE          | Asociado de telecomunicaciones E               |
| TemSeGeneralcounter                 | Contador de Temse                              |
| TemseNumAddProtectionRule           | Regla de protección de adición de número de Temse              |
| TemseNumChangeProtectionRule        | Regla de protección de cambio de número de Temse           |
| TemseNumDeleteProtectionRule        | Regla de protección de eliminación de número de Temse           |
| TemSeObjectName                     | Nombre de objeto de Temse                          |
| TemSeObjectPart                     | Parte de objeto de TemSe                          |
| TemseReadProtectionRule             | Regla de protección de lectura de Temse                 |
| Usuario                                | Usuario                                       |
| ValueAuthCheck                      | Comprobación de autenticación de valor                           |
| | |

## <a name="apab-spool-output-log"></a>Registro de salida de spool de APAB

- **Nombre en Azure Sentinel**: `ABAPSpoolOutputLog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae779e40f72045e10000000a421937.html)

- **Fin del registro**: sirve como registro principal para la impresión de SAP con el historial de solicitudes de salida del spool. (SP02).

    Disponible mediante RFC con un servicio personalizado basado en tablas estándar. Este registro se genera con datos en todos los clientes.

### <a name="abapspooloutputlog_cl-log-schema"></a>Esquema de registro ABAPSpoolOutputLog_CL

| Campo                              | Descripción                               |
| ---------------------------------- | ----------------------------------------- |
| AppServer                          | Servidor de aplicaciones                        |
| ClientID                           | Identificador de cliente de ABAP (MANDT)                    |
| Comentario                            | Comentario                                   |
| CopyCount                          | Número de copias                                |
| CopyCounter                        | Contador de copias                              |
| department                         | department                                |
| ErrorSpoolRequestNumber            | Número de solicitud de error                      |
| Tipo de formato                         | Tipo de formato                               |
| administrador de flujos de trabajo                               | administrador de flujos de trabajo                                      |
| HostName                           | Nombre de host                                 |
| HostSpoolerID                      | Identificador de spooler de host                          |
| Instancia                           | Instancia de ABAP                             |
| LastPage                           | Última página                                 |
| NumofCopies                        | Número de copias                              |
| OutputDevice                       | Dispositivo de salida                             |
| OutputRequestNumber                | Número de solicitud de salida                     |
| OutputRequestStatus                | Estado de solicitud de salida                     |
| PhysicalFormatType                 | Tipo de formato físico                      |
| PrinterLongName                    | Nombre largo de impresora                         |
| PrintRequestSize                   | Tamaño de solicitud de impresión                        |
| Priority                           | Priority                                  |
| ReasonforOutputRequest             | Razón de solicitud de salida                 |
| RecipientofSpoolRequest            | Destinatario de solicitud de spool                 |
| SpoolNumberofOutputReqProcessed    | Número de solicitudes de salida: procesadas     |
| SpoolNumberofOutputReqWithErrors   | Número de solicitudes de salida: con errores   |
| SpoolNumberofOutputReqWithProblems | Número de solicitudes de salida: con problemas |
| SpoolRequestNumber                 | Número de solicitud de spool                      |
| StartPage                          | Página de inicio                                |
| SystemID                           | Identificador del sistema                                 |
| SystemNumber                       | Número del sistema                             |
| TelecommunicationsPartner          | Asociado de telecomunicaciones                |
| TemSeGeneralcounter                | Contador de Temse                             |
| Título                              | Título                                     |
| Usuario                               | Usuario                                      |
| | |


## <a name="abap-syslog"></a>Syslog ABAP

- **Nombre en Azure Sentinel**: `ABAPOS_Syslog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcbaf36611d3a6510000e835363f.html)

- **Fin del registro**: registra todos los errores del sistema ABAP, advertencias, bloqueos de usuario debido a intentos de inicio de sesión con errores de usuarios conocidos y mensajes de proceso del servidor de aplicaciones de SAP NetWeaver (SAP NetWeaver AS).

    Disponible por el servicio web de control de SAP. Este registro se genera con datos en todos los clientes.

### <a name="abapos_syslog_cl-log-schema"></a>Esquema de registro ABAPOS_Syslog_CL


| Campo            | Descripción            |
| ---------------- | ---------------------- |
| ClientID         | Identificador de cliente de ABAP (MANDT) |
| administrador de flujos de trabajo             | administrador de flujos de trabajo                   |
| Instancia         | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR> ` |
| MessageNumber    | Número de mensaje         |
| MessageText      | Texto del mensaje           |
| severity         | Gravedad del mensaje, uno de los siguientes valores: `Debug`, `Info`, `Warning`, `Error`        |
| SystemID         | Identificador del sistema              |
| SystemNumber     | Número del sistema          |
| TransactionCode | Código de transacción       |
| Tipo             | Tipo de proceso de SAP       |
| Usuario             | Usuario                   |
| | |


## <a name="abap-workflow-log"></a>Registro de flujo de trabajo de ABAP

- **Nombre en Azure Sentinel**: `ABAPWorkflowLog_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcccf36611d3a6510000e835363f.html)

- **Fin del registro**: SAP Business Workflow (motor de WebFlow) permite definir procesos empresariales que aún no están asignados en el sistema SAP.

    Por ejemplo, los procesos empresariales desasignados pueden ser procedimientos de aprobación o lanzamiento sencillos, o bien procesos empresariales más complejos, como la creación de material base y la posterior coordinación de los departamentos asociados.

    Disponible mediante RFC con un servicio personalizado basado en tablas y servicios estándar. Este registro se genera por cliente.

### <a name="abapworkflowlog_cl-log-schema"></a>Esquema de registro ABAPWorkflowLog_CL


| Campo               | Descripción                      |
| ------------------- | -------------------------------- |
| ActualAgent         | Agente real                     |
| Dirección             | Dirección                          |
| ApplicationArea     | Área de aplicación                 |
| CallbackFunction    | Función de devolución de llamada                |
| ClientID            | Identificador de cliente de ABAP (MANDT)           |
| CreationDateTime    | Fecha y hora de creación               |
| Creador             | Creador                          |
| CreatorAddress      | Dirección de creador                  |
| ErrorType           | Tipo de error                       |
| ExceptionforMethod  | Excepción del método             |
| administrador de flujos de trabajo                | administrador de flujos de trabajo                             |
| Instancia            | Instancia de ABAP(HOST_SYSID_SYSNR), con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>` |
| Lenguaje            | Lenguaje                         |
| LogCounter          | Contador de registros                      |
| MessageNumber       | Número de mensaje                   |
| MessageType         | Tipo de mensaje                     |
| MethodUser          | Usuario del método                      |
| Priority            | Priority                         |
| SimpleContainer     | Contenedor simple, empaquetado como una lista de entidades Clave-Valor para el elemento de trabajo |
| Estado              | Estado                           |
| SuperWI             | Súper elemento de trabajo                         |
| SystemID            | Identificador del sistema                        |
| SystemNumber        | Número del sistema                    |
| TaskID              | Identificador de tarea                          |
| TasksClassification | Clasificaciones de tareas            |
| TaskText            | Texto de tarea                        |
| TopTaskID           | Identificador de tarea principal                      |
| UserCreated         | Creado por el usuario                     |
| WIText              | Texto de elemento de trabajo                   |
| WIType              | Tipo de elemento de trabajo                   |
| WorkflowAction      | Acción Workflow                  |
| WorkItemID          | Id. de elemento de trabajo                     |
| | |





## <a name="abap-workprocess-log"></a>Registro de proceso de trabajo de ABAP

- **Nombre en Azure Sentinel**: `ABAPOS_WP_CL`

- **Documentación de SAP relacionada**: [portal de ayuda de SAP](https://help.sap.com/viewer/d0739d980ecf42ae9f3b4c19e21a4b6e/7.3.15/en-US/46fb763b6d4c5515e10000000a1553f6.html)

- **Fin del registro**: combina todos los registros de procesos de trabajo. (valor predeterminado: `dev_*`).

    Disponible por el servicio web de control de SAP. Este registro se genera con datos en todos los clientes.

### <a name="abapos_wp_cl-log-schema"></a>Esquema de registro ABAPOS_WP_CL


| Campo        | Descripción         |
| ------------ | ------------------- |
| Host         | administrador de flujos de trabajo                |
| Instancia     | Instancia de ABAP, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | Texto del mensaje     |
| severity     | Gravedad del mensaje: `Debug`, `Info`, `Warning`, `Error`  |
| SystemID     | Identificador del sistema           |
| SystemNumber | Número del sistema       |
| WPNumber     | Número de proceso de trabajo |
| | |


## <a name="hana-db-audit-trail"></a>Registro de auditoría de base de datos de HANA

- **Nombre en Azure Sentinel**: `Syslog`

- **Documentación de SAP relacionada**: [General](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/48fd6586304c4f859bf92d64d0cd8b08.html) |  [Registro de auditoría](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.03/en-US/0a57444d217649bf94a19c0b68b470cc.html)

- **Fin del registro**: registra las acciones del usuario o las acciones intentadas en la base de datos de SAP HANA. Por ejemplo, permite registrar y supervisar el acceso de lectura a datos confidenciales.

    Disponible por el agente Linux de Sentinel para Syslog. Este registro se genera con datos en todos los clientes.

### <a name="syslog-log-schema"></a>Esquema de registro de Syslog

| Campo         | Descripción  |
| ------------- | ------------ |
| Computer      | Nombre de host    |
| HostIP        | IP de host      |
| HostName      | Nombre de host    |
| ProcessID     | Identificador del proceso   |
| ProcessName   | Nombre del proceso: `HDB*` |
| SeverityLevel | Alerta        |
| SourceSystem  |   Sistema operativo del sistema de origen, `Linux`           |
| SyslogMessage | Mensaje, un mensaje de registro de auditoría no analizado      |
| | |

## <a name="java-files"></a>Archivos JAVA

- **Nombre en Azure Sentinel**: `JavaFilesLogsCL`

- **Documentación de SAP relacionada**: [General](https://help.sap.com/viewer/2f8b1599655d4544a3d9c6d1a9b6546b/7.5.9/en-US/485059dfe31672d4e10000000a42189c.html) | [Registro de auditoría de seguridad de Java](https://help.sap.com/viewer/1531c8a1792f45ab95a4c49ba16dc50b/7.5.9/en-US/4b6013583840584ae10000000a42189c.html)

- **Fin del registro**: combina todos los registros basados en archivos de Java, incluidos el registro de auditoría de seguridad y los registros del sistema (proceso de clúster y servidor), de rendimiento y de puerta de enlace. También incluye los registros de seguimientos para desarrolladores y de seguimiento predeterminado.

    Disponible por el servicio web de control de SAP. Este registro se genera con datos en todos los clientes.

### <a name="javafileslogscl-log-schema"></a>Esquema de registro de JavaFilesLogsCL


| Campo            | Descripción          |
| ---------------- | -------------------- |
| Application      | Aplicación Java     |
| ClientID         | Id. de cliente           |
| CSNComponent     | Componente CSN, como `BC-XI-IBD` |
| DCComponent      | Componente DC, como `com.sap.xi.util.misc` |
| DSRCounter       | Contador de DSR          |
| DSRRootContentID | GUID de contexto de DSR     |
| DSRTransaction   | GUID de transacción de DSR |
| administrador de flujos de trabajo             | administrador de flujos de trabajo                 |
| Instancia         | Instancia de Java, con la siguiente sintaxis: `<HOST>_<SYSID>_<SYSNR>` |
| Location         | Clase de Java           |
| LogName          | LogName de Java, como: `Available`,`defaulttrace`, `dev*`, `security`, etc.
| MessageText      | Texto del mensaje         |
| MNo              | Número de mensaje       |
| Pid              | Identificador del proceso           |
| Programa          | Nombre de programa         |
| Sesión          | Sesión              |
| severity         | Gravedad del mensaje, se incluyen: `Debug`, `Info`, `Warning`, `Error`     |
| Solución         | Solución             |
| SystemID         | Identificador del sistema            |
| SystemNumber     | Número del sistema        |
| ThreadName       | Nombre del subproceso          |
| Thrown           | Se inicia una excepción     |
| TimeZone         | Zona horaria             |
| Usuario             | Usuario                 |
| | |

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Implementación de la solución Azure Sentinel para SAP](sap-deploy-solution.md)
- [Requisitos detallados de SAP para la solución Azure Sentinel SAP](sap-solution-detailed-requirements.md)
- [Implementación del conector de datos de SAP de Azure Sentinel con SNC](sap-solution-deploy-snc.md)
- [Opciones de configuración de expertos, implementación local y orígenes de registro de SAPControl](sap-solution-deploy-alternate.md)
- [Azure Sentinel para SAP: contenido de seguridad integrado](sap-solution-security-content.md)
- [Solución de problemas de implementación de la solución SAP de Azure Sentinel](sap-deploy-troubleshoot.md)
