---
title: Esquema de registros y eventos
description: Obtenga información sobre el esquema utilizado por los registros y eventos de Azure Data Factory para la supervisión.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 4b64818b505863ad7f7e2878640eeb28224af2f1
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400199"
---
# <a name="schema-of-logs-and-events"></a>Esquema de registros y eventos

En este artículo se describe el esquema utilizado por los registros y eventos de Azure Data Factory para la supervisión.

## <a name="monitor-schema"></a>Esquema de Monitor
Las siguientes listas de atributos se usan para la supervisión.

### <a name="activity-run-log-attributes"></a>Atributos de registro de la ejecución de actividad

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propiedad | Tipo | Descripción | Ejemplo |
| --- | --- | --- | --- |
| **Level** |String | El nivel de los registros de diagnóstico. En el caso de los registros de ejecución de actividad, establezca el valor de la propiedad en 4. | `4` |
| **correlationId** |String | El identificador único para realizar el seguimiento de una solicitud determinada. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | La hora del evento en formato UTC de intervalo de tiempo`YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| El identificador de la ejecución de actividad. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| El identificador de la ejecución de canalización. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | El identificador asociado con el recurso de factoría de datos. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | La categoría de los registros de diagnóstico. Establezca el valor de propiedad en `ActivityRuns`. | `ActivityRuns` |
|**level**| String | El nivel de los registros de diagnóstico. Establezca el valor de propiedad en `Informational`. | `Informational` |
|**operationName**| String | El nombre de la actividad con su estado. Si la actividad es el latido de inicio, el valor de propiedad es `MyActivity -`. Si la actividad es el latido final, el valor de propiedad es `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**pipelineName**| String | El nombre de la canalización. | `MyPipeline` |
|**activityName**| String | El nombre de la actividad. | `MyActivity` |
|**start**| String | La hora de inicio de las ejecuciones de actividad en formato UTC del intervalo de tiempo. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | La hora de finalización de las ejecuciones de actividad en formato UTC del intervalo de tiempo. Si el registro de diagnóstico muestra que una actividad se ha iniciado pero aún no ha finalizado, el valor de propiedad es `1601-01-01T00:00:00Z`. | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-log-attributes"></a>Atributos de registro de la ejecución de la canalización

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propiedad | Tipo | Descripción | Ejemplo |
| --- | --- | --- | --- |
| **Level** |String | El nivel de los registros de diagnóstico. En el caso de los registros de ejecución de actividad, establezca el valor de la propiedad en 4. | `4` |
| **correlationId** |String | El identificador único para realizar el seguimiento de una solicitud determinada. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | La hora del evento en formato UTC de intervalo de tiempo`YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| El identificador de la ejecución de canalización. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | El identificador asociado con el recurso de factoría de datos. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | La categoría de los registros de diagnóstico. Establezca el valor de propiedad en `PipelineRuns`. | `PipelineRuns` |
|**level**| String | El nivel de los registros de diagnóstico. Establezca el valor de propiedad en `Informational`. | `Informational` |
|**operationName**| String | El nombre de la canalización junto con su estado. Una vez finalizada la ejecución de la canalización, el valor de propiedad es `Pipeline - Succeeded`. | `MyPipeline - Succeeded`. |
|**pipelineName**| String | El nombre de la canalización. | `MyPipeline` |
|**start**| String | La hora de inicio de las ejecuciones de actividad en formato UTC del intervalo de tiempo. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | La hora de finalización de las ejecuciones de actividad en formato UTC del intervalo de tiempo. Si el registro de diagnóstico muestra que una actividad se ha iniciado pero aún no ha finalizado, el valor de propiedad es `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | El estado final de la ejecución de canalización. Los valores de propiedad posibles son `Succeeded` y `Failed`. | `Succeeded`|

### <a name="trigger-run-log-attributes"></a>Atributos de registro de la ejecución del desencadenador

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Propiedad | Tipo | Descripción | Ejemplo |
| --- | --- | --- | --- |
| **Level** |String | El nivel de los registros de diagnóstico. En el caso de los registros de ejecución de actividad, establezca el valor de la propiedad en 4. | `4` |
| **correlationId** |String | El identificador único para realizar el seguimiento de una solicitud determinada. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | La hora del evento en formato UTC de intervalo de tiempo`YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| El identificador de la ejecución del desencadenador. | `08587023010602533858661257311` |
|**resourceId**| String | El identificador asociado con el recurso de factoría de datos. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | La categoría de los registros de diagnóstico. Establezca el valor de propiedad en `PipelineRuns`. | `PipelineRuns` |
|**level**| String | El nivel de los registros de diagnóstico. Establezca el valor de propiedad en `Informational`. | `Informational` |
|**operationName**| String | El nombre del desencadenador con su estado final, que indica si el desencadenador se activó correctamente. Si el latido se realizó correctamente, el valor de la propiedad es `MyTrigger - Succeeded`. | `MyTrigger - Succeeded` |
|**triggerName**| String | El nombre del desencadenador. | `MyTrigger` |
|**triggerType**| String | El tipo del desencadenador. Los valores de propiedad posibles son `Manual Trigger` y `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| String | El evento del desencadenador. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | La hora de inicio del desencadenador que se activa en formato UTC del intervalo de tiempo. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | El estado final que indica si el desencadenador se activó correctamente. Los valores de propiedad posibles son `Succeeded` y `Failed`. | `Succeeded`|

### <a name="ssis-integration-runtime-log-attributes"></a>Atributos de los registros de SSIS Integration Runtime

Estos son los atributos de registro de las operaciones de inicio, detención y mantenimiento del entorno de ejecución de integración (IR) de SQL Server Integration Services (SSIS).

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Propiedad                   | Tipo   | Descripción                                                   | Ejemplo                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Hora del evento en formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Nombre de la operación de SSIS IR                            | `Start/Stop/Maintenance/Heartbeat` |
| **category**               | String | Categoría de los registros de diagnóstico                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | Id. exclusivo para realizar el seguimiento de una operación determinada             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | El nombre de la factoría de datos.                                          | `MyADFv2` |
| **integrationRuntimeName** | String | Nombre de la instancia de SSIS IR                                      | `MySSISIR` |
| **level**                  | String | Nivel de los registros de diagnóstico                                  | `Informational` |
| **resultType**             | String | Resultado de la operación de SSIS IR                          | `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` |
| **message**                | String | Mensaje de salida de la operación de SSIS IR                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | El identificador único del recurso de factoría de datos.                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-message-context-log-attributes"></a>Atributos de los registros contextuales de los mensajes de eventos de SSIS

Estos son los atributos de registro de las condiciones relacionadas con los mensajes de eventos generados por las ejecuciones de paquetes SSIS en la instancia de SSIS IR. Transmiten información similar a la de la [tabla o vista del contexto de mensajes de eventos del catálogo de SSIS (SSISDB)](/sql/integration-services/system-views/catalog-event-message-context) que muestra los valores de tiempo de ejecución de muchas propiedades de los paquetes SSIS. Se generan al seleccionar el nivel de registro `Basic/Verbose` y resultan útiles para la depuración o la comprobación de cumplimiento.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Propiedad                   | Tipo   | Descripción                                                          | Ejemplo                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Hora del evento en formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Establézcala en `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | Categoría de los registros de diagnóstico                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | Id. exclusivo para realizar el seguimiento de una operación determinada                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | El nombre de la factoría de datos.                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | Nombre de la instancia de SSIS IR                                             | `MySSISIR` |
| **level**                  | String | Nivel de los registros de diagnóstico                                         | `Informational` |
| **operationId**            | String | Id. exclusivo para realizar el seguimiento de una operación determinada en SSISDB          | `1` (1 significa operaciones relacionadas con paquetes que *no* están almacenados en SSISDB o no se invocan mediante T-SQL). |
| **contextDepth**           | String | Profundidad del contexto del mensaje del evento                              | `0` (0 significa el contexto antes de que comience la ejecución del paquete, 1 significa el contexto cuando se produce un error, y aumenta a medida que el contexto se aleja del error). |
| **packagePath**            | String | Ruta de acceso del objeto de paquete como origen del contexto del mensaje del evento      | `\Package` |
| **contextType**            | String | Tipo de objeto de paquete como origen del contexto del mensaje del evento      | `60` (Consulte [más tipos de contexto](/sql/integration-services/system-views/catalog-event-message-context#remarks)). |
| **contextSourceName**      | String | Nombre del objeto de paquete como origen del contexto del mensaje del evento      | `MyPackage` |
| **contextSourceId**        | String | Id. exclusivo del objeto de paquete como origen del contexto del mensaje del evento | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | Nombre de la propiedad del paquete para el origen del contexto del mensaje del evento   | `DelayValidation` |
| **propertyValue**          | String | Valor de la propiedad del paquete para el origen del contexto del mensaje del evento  | `False` |
| **resourceId**             | String | El identificador único del recurso de factoría de datos.                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-messages-log-attributes"></a>Atributos de los registros de los mensajes de eventos de SSIS

Estos son los atributos de registro de los mensajes de eventos que generan las ejecuciones de paquetes SSIS en la instancia de SSIS IR. Transmiten información similar a la de la [tabla o vista de mensajes de eventos de SSISDB](/sql/integration-services/system-views/catalog-event-messages) que muestra los metadatos o el texto detallado de los mensajes de los eventos. Se generan en cualquier nivel de registro, excepto en `None`.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Propiedad                   | Tipo   | Descripción                                                        | Ejemplo                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | Hora del evento en formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Establézcala en `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | Categoría de los registros de diagnóstico                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | Id. exclusivo para realizar el seguimiento de una operación determinada                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | El nombre de la factoría de datos.                                               | `MyADFv2` |
| **integrationRuntimeName** | String | Nombre de la instancia de SSIS IR                                           | `MySSISIR` |
| **level**                  | String | Nivel de los registros de diagnóstico                                       | `Informational` |
| **operationId**            | String | Id. exclusivo para realizar el seguimiento de una operación determinada en SSISDB        | `1` (1 significa operaciones relacionadas con paquetes que *no* están almacenados en SSISDB o no se invocan mediante T-SQL). |
| **messageTime**            | String | Hora a la que se crea el mensaje del evento en formato UTC          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | Tipo de mensaje de evento                                     | `70` (Consulte [más tipos de mensajes](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)). |
| **messageSourceType**      | String | Tipo de origen del mensaje del evento                              | `20` (Consulte [más tipos de orígenes de mensajes](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)). |
| **message**                | String | Texto del mensaje del evento                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | Nombre del archivo del paquete ejecutado                             | `MyPackage.dtsx` |
| **eventName**              | String | Nombre del evento de tiempo de ejecución relacionado                                 | `OnPreValidate` |
| **messageSourceName**      | String | Nombre del componente del paquete como origen del mensaje del evento         | `Data Flow Task` |
| **messageSourceId**        | String | Id. exclusivo del componente del paquete como origen del mensaje del evento    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | String | Nombre del componente del flujo de datos como el origen del mensaje del evento       | `SSIS.Pipeline` |
| **packagePath**            | String | Ruta de acceso del objeto de paquete como origen del mensaje del evento            | `\Package\Data Flow Task` |
| **executionPath**          | String | Ruta de acceso completa del paquete primario para el componente ejecutado            | `\Transformation\Data Flow Task` (Esta ruta también captura las iteraciones de los componentes). |
| **threadId**               | String | Id. exclusivo del subproceso ejecutado cuando se registra el mensaje del evento | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

### <a name="ssis-executable-statistics-log-attributes"></a>Atributos del registro de estadísticas de archivos ejecutables de SSIS

Estos son los atributos de registro de las estadísticas de los archivos ejecutables que generan las ejecuciones de paquetes SSIS en la instancia de SSIS IR, en la que los archivos ejecutables son contenedores o tareas de los flujos de control de paquetes. Transmiten información similar a la de la [tabla o vista de estadísticas de archivos ejecutables de SSISDB](/sql/integration-services/system-views/catalog-executable-statistics) que muestra una fila para cada archivo ejecutable en ejecución, incluidas sus iteraciones. Se generan en cualquier nivel de registro, excepto `None`, y resultan útiles para identificar los errores o cuellos de botella a nivel de tarea.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Propiedad                   | Tipo   | Descripción                                                      | Ejemplo                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Hora del evento en formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Establézcala en `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | Categoría de los registros de diagnóstico                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | Id. exclusivo para realizar el seguimiento de una operación determinada                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | El nombre de la factoría de datos.                                             | `MyADFv2` |
| **integrationRuntimeName** | String | Nombre de la instancia de SSIS IR                                         | `MySSISIR` |
| **level**                  | String | Nivel de los registros de diagnóstico                                     | `Informational` |
| **executionId**            | String | Id. exclusivo para realizar el seguimiento de una ejecución determinada en SSISDB      | `1` (1 significa ejecuciones relacionadas con paquetes que *no* están almacenados en SSISDB o no se invocan mediante T-SQL). |
| **executionPath**          | String | Ruta de acceso completa del paquete primario para el componente ejecutado          | `\Transformation\Data Flow Task` (Esta ruta también captura las iteraciones de los componentes). |
| **startTime**              | String | Hora en que el archivo ejecutable entra en la fase previa a la ejecución en formato UTC  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | Hora en que el archivo ejecutable entra en la fase posterior a la ejecución en formato UTC | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | Tiempo de ejecución del archivo ejecutable en milisegundos                   | `1,125` |
| **executionResult**        | String | Resultado de la ejecución del archivo ejecutable                                 | `0` (0 significa que se ha realizado correctamente, 1 significa que se ha producido un error, 2 significa finalización y 3 significa cancelación). |
| **executionValue**         | String | Valor definido por el usuario devuelto por la ejecución del archivo ejecutable            | `1` |
| **resourceId**             | String | El identificador único del recurso de factoría de datos.                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-component-phases-log-attributes"></a>Atributos del registro de fases de los componentes de ejecución de SSIS

Estos son los atributos de registro de las estadísticas de tiempo de ejecución de los componentes de flujo de datos que generan las ejecuciones de paquetes SSIS en la instancia de IR de SSIS. Transmiten información similar a la de la [tabla o vista de las fases de los componentes de ejecución de SSISDB](/sql/integration-services/system-views/catalog-execution-component-phases) que muestra el tiempo empleado por los componentes de flujo de datos en todas sus fases de ejecución. Se generan al seleccionar el nivel de registro `Performance/Verbose` y resultan útiles para capturar las estadísticas de ejecución del flujo de datos.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Propiedad                   | Tipo   | Descripción                                                         | Ejemplo                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Hora del evento en formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Establézcala en `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | Categoría de los registros de diagnóstico                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | Id. exclusivo para realizar el seguimiento de una operación determinada                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | El nombre de la factoría de datos.                                                | `MyADFv2` |
| **integrationRuntimeName** | String | Nombre de la instancia de SSIS IR                                            | `MySSISIR` |
| **level**                  | String | Nivel de los registros de diagnóstico                                        | `Informational` |
| **executionId**            | String | Id. exclusivo para realizar el seguimiento de una ejecución determinada en SSISDB         | `1` (1 significa ejecuciones relacionadas con paquetes que *no* están almacenados en SSISDB o no se invocan mediante T-SQL). |
| **packageName**            | String | Nombre del archivo del paquete ejecutado                              | `MyPackage.dtsx` |
| **taskName**               | String | Nombre de la tarea Flujo de datos ejecutada                                 | `Data Flow Task` |
| **subcomponentName**       | String | Nombre del componente de flujo de datos                                     | `Derived Column` |
| **phase**                  | String | Nombre de la fase de ejecución                                         | `AcquireConnections` |
| **startTime**              | String | Hora a la que se inicia la fase de ejecución en formato UTC                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | Hora de finalización de la fase de ejecución en formato UTC                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | Ruta de ejecución de la tarea Flujo de datos                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | El identificador único del recurso de factoría de datos.                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-data-statistics-log-attributes"></a>Atributos del registro de estadísticas de los datos de ejecución de SSIS

Estos son los atributos de registro de los movimientos de datos a través de cada una de las canalizaciones de flujo de datos, desde los componentes ascendentes hasta los descendentes, que generan las ejecuciones de paquetes SSIS en la instancia de SSIS IR. Transmiten información similar a la de la [tabla o vista de estadísticas de datos de ejecución de SSISDB](/sql/integration-services/system-views/catalog-execution-data-statistics) que muestra los recuentos de filas de los datos que se mueven a través de las tareas de flujo de datos. Se generan al seleccionar el nivel de registro `Verbose` y resultan útiles para calcular el rendimiento del flujo de datos.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Propiedad                     | Tipo   | Descripción                                                        | Ejemplo                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | Hora del evento en formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | Establézcala en `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | Categoría de los registros de diagnóstico                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | Id. exclusivo para realizar el seguimiento de una operación determinada                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | El nombre de la factoría de datos.                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | Nombre de la instancia de SSIS IR                                           | `MySSISIR` |
| **level**                    | String | Nivel de los registros de diagnóstico                                       | `Informational` |
| **executionId**              | String | Id. exclusivo para realizar el seguimiento de una ejecución determinada en SSISDB        | `1` (1 significa ejecuciones relacionadas con paquetes que *no* están almacenados en SSISDB o no se invocan mediante T-SQL). |
| **packageName**              | String | Nombre del archivo del paquete ejecutado                             | `MyPackage.dtsx` |
| **taskName**                 | String | Nombre de la tarea Flujo de datos ejecutada                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | Id. exclusivo para el seguimiento de la ruta del flujo de datos                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | Nombre de la ruta del flujo de datos                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | Nombre del componente de flujo de datos que envía los datos                    | `SQLDB Table3` |
| **destinationComponentName** | String | Nombre del componente de flujo de datos que recibe los datos                 | `Derived Column` |
| **rowsSent**                 | String | Número de filas enviadas por el componente de origen                        | `500` |
| **createdTime**              | String | Hora a la que se obtienen los valores de fila en formato UTC                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | Ruta de ejecución de la tarea Flujo de datos                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | El identificador único del recurso de factoría de datos.                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

## <a name="log-analytics-schema"></a>Esquema de Log Analytics

Log Analytics hereda el esquema de Azure Monitor con las excepciones siguientes:

* La primera letra de cada nombre de columna se escribe en mayúsculas. Por ejemplo, el nombre de columna "correlationId" en Monitor es "CorrelationId" en Log Analytics.
* No hay ninguna columna "Level".
* La columna de "propiedades" dinámicas se conservará como el tipo de blob de JSON dinámico siguiente.

    | Columna de Azure Monitor | Columna de Log Analytics | Tipo |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | Dinámica |
    | $.properties.Annotations | Anotaciones | Dinámica |
    | $.properties.Input | Entrada | Dinámica |
    | $.properties.Output | Output | Dinámica |
    | $.properties.Error.errorCode | ErrorCode | int |
    | $.properties.Error.message | ErrorMessage | string |
    | $.properties.Error | Error | Dinámica |
    | $.properties.Predecessors | Predecessors | Dinámica |
    | $.properties.Parameters | Parámetros | Dinámica |
    | $.properties.SystemParameters | SystemParameters | Dinámica |
    | $.properties.Tags | Etiquetas | Dinámica |

## <a name="next-steps"></a>Pasos siguientes

[Instalación de la solución Azure Data Factory Analytics desde Azure Marketplace](monitor-install-analytics.md)
