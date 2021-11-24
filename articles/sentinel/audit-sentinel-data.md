---
title: Auditoría de consultas y actividades de Microsoft Sentinel | Microsoft Docs
description: En este artículo se describe cómo auditar las consultas y actividades realizadas en Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 26eac13eaee01d4f418abe31b69d0730d28f6afe
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707227"
---
# <a name="audit-microsoft-sentinel-queries-and-activities"></a>Auditoría de consultas y actividades de Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este artículo se describe cómo puede ver los datos de auditoría para las consultas ejecutadas y las actividades realizadas en el área de trabajo de Microsoft Sentinel, como los requisitos de cumplimiento internos y externos en el área de trabajo de las operaciones de seguridad (SOC).

Microsoft Sentinel proporciona acceso a:

- La tabla **AzureActivity**, en la que se proporcionan detalles sobre todas las acciones realizadas en Microsoft Sentinel, como la edición de reglas de alertas. En la tabla **AzureActivity** no se registran datos específicos de la consulta. Para más información, consulte [Auditoría con registros de actividad de Azure](#auditing-with-azure-activity-logs).

- La tabla **LAQueryLogs**, en la que se proporcionan detalles sobre las consultas que se ejecutan en log Analytics, incluidas las consultas que se ejecutan desde Microsoft Sentinel. Para más información, consulte [Auditoría con LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> Además de las consultas manuales que se describen en este artículo, Microsoft Sentinel proporciona un libro integrado para ayudarle a auditar las actividades en el entorno de SOC.
>
> En el área **Libros** de Microsoft Sentinel, busque el libro **Workspace audit** (Auditoría de área de trabajo).

## <a name="auditing-with-azure-activity-logs"></a>Auditoría con registros de actividad de Azure

Los registros de auditoría de Microsoft Sentinel se mantienen en los [registros de actividad de Azure](../azure-monitor/essentials/platform-logs-overview.md), donde la tabla **AzureActivity** incluye todas las acciones realizadas en el área de trabajo de Microsoft Sentinel.

Puede usar la tabla **AzureActivity** al auditar la actividad en el entorno de SOC con Microsoft Sentinel.

**Para consultar la tabla AzureActivity**:

1. Conecte el origen de datos [Actividad de Azure](./data-connectors-reference.md#azure-activity) para iniciar la transmisión de eventos de auditoría a una nueva tabla en la pantalla **Registros**, denominada AzureActivity.

1. A continuación, consulte los datos mediante KQL, tal como lo haría con cualquier otra tabla.

    En la tabla **AzureActivity** se incluyen datos de muchos servicios, incluido Microsoft Sentinel. Para filtrar solo los datos de Microsoft Sentinel, inicie la consulta con el código siguiente:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Por ejemplo, para averiguar quién fue el último usuario en editar una regla de análisis determinada, use la consulta siguiente (reemplazando `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` por el identificador de la regla que desea comprobar):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Agregue más parámetros a la consulta para explorar más la tabla **AzureActivities** en función de lo que necesite informar. En las secciones siguientes se proporcionan otras consultas de ejemplo que se pueden usar al realizar la auditoría con datos de la tabla **AzureActivity**.

Para más información, consulte [Datos de Microsoft Sentinel incluidos en los registros de actividad de Azure](#microsoft-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Búsqueda de todas las acciones realizadas por un usuario específico en las últimas 24 horas

La siguiente consulta de la tabla **AzureActivity** enumera todas las acciones realizadas por un usuario específico de Azure AD en las últimas 24 horas.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Búsqueda de todas las operaciones de eliminación

La siguiente consulta de la tabla **AzureActivity** enumera todas las operaciones de eliminación realizadas en el área de trabajo de Microsoft Sentinel.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
```

### <a name="microsoft-sentinel-data-included-in-azure-activity-logs"></a>Datos de Microsoft Sentinel incluidos en los registros de actividad de Azure

Los registros de auditoría de Microsoft Sentinel se mantienen en los [registros de actividad de Azure](../azure-monitor/essentials/platform-logs-overview.md) e incluyen los siguientes tipos de información:

|Operación  |Tipos de información  |
|---------|---------|
|**Creado**     |Las reglas de alertas <br> Comentarios de casos <br>Comentarios sobre el incidente <br>Búsquedas guardadas<br>Listas de reproducción    <br>Workbooks     |
|**Eliminado**     |Las reglas de alertas <br>Marcadores <br>Conectores de datos <br>Incidentes <br>Búsquedas guardadas <br>Configuración <br>Informes de inteligencia sobre amenazas <br>Listas de reproducción      <br>Workbooks <br>Flujo de trabajo  |
|**Updated**     |  Las reglas de alertas<br>Marcadores <br> Casos <br> Conectores de datos <br>Incidentes <br>Comentarios sobre el incidente <br>Informes de inteligencia sobre amenazas <br> Workbooks <br>Flujo de trabajo       |
|     |         |

También puede usar los registros de actividad de Azure para comprobar las autorizaciones y licencias de usuario.

Por ejemplo, en la tabla siguiente se enumeran las operaciones seleccionadas que se encuentran en los registros de actividad de Azure con el recurso específico del que se extraen los datos de registro.

|Nombre de la operación| Tipo de recurso|
|----|----|
|Crear o actualizar libro |Microsoft.Insights/workbooks|
|Eliminar libro |Microsoft.Insights/workbooks|
|Establecer flujo de trabajo |Microsoft.Logic/workflows|
|Eliminar flujo de trabajo |Microsoft.Logic/workflows|
|Crear búsqueda guardada |Microsoft.OperationalInsights/workspaces/savedSearches|
|Eliminar búsqueda guardada |Microsoft.OperationalInsights/workspaces/savedSearches|
|Actualizar reglas de alerta |Microsoft.SecurityInsights/alertRules|
|Eliminar reglas de alerta |Microsoft.SecurityInsights/alertRules|
|Actualizar acciones de respuesta de reglas de alerta |Microsoft.SecurityInsights/alertRules/actions|
|Eliminar acciones de respuesta de reglas de alerta |Microsoft.SecurityInsights/alertRules/actions|
|Actualizar marcadores |Microsoft.SecurityInsights/bookmarks|
|Eliminar marcadores |Microsoft.SecurityInsights/bookmarks|
|Actualizar casos |Microsoft.SecurityInsights/Cases|
|Actualizar investigación de caso |Microsoft.SecurityInsights/Cases/investigations|
|Crear comentarios de casos |Microsoft.SecurityInsights/Cases/comments|
|Actualizar conectores de datos |Microsoft.SecurityInsights/dataConnectors|
|Eliminar conectores de datos |Microsoft.SecurityInsights/dataConnectors|
|Actualización de la configuración |Microsoft.SecurityInsights/settings|
| | |

Para más información, consulte [Esquema de eventos del registro de actividad de Azure](../azure-monitor/essentials/activity-log-schema.md).

## <a name="auditing-with-laquerylogs"></a>Auditoría con LAQueryLogs

En la tabla **LAQueryLogs** se proporcionan detalles sobre las consultas de registro que se ejecutan en log Analytics. Como Log Analytics se usa como almacén de datos subyacente de Microsoft Sentinel, puede configurar el sistema para recopilar datos de LAQueryLogs en el área de trabajo de Microsoft Sentinel.

Los datos de LAQueryLogs incluyen información como:

- Cuándo se ejecutaron las consultas
- Quién ejecutó las consultas en Log Analytics
- Qué herramienta se usó para ejecutar consultas en Log Analytics, como Microsoft Sentinel
- Los propios textos de las consultas
- Datos de rendimiento en cada ejecución de consulta

> [!NOTE]
> - En la tabla **LAQueryLogs** solo se incluyen las consultas que se han ejecutado en la hoja Registros de Microsoft Sentinel. No se incluyen las consultas ejecutadas por las reglas de análisis programado, mediante el **Gráfico de investigación** o en la página **Búsqueda** de Microsoft Sentinel.
> - Puede haber un breve retraso entre el momento en que se ejecuta una consulta y en el que se rellenan los datos en la tabla **LAQueryLogs**. Se recomienda esperar unos 5 minutos para consultar los datos de auditoría de la tabla **LAQueryLogs**.

**Para consultar la tabla LAQueryLogs**:

1. La tabla **LAQueryLogs** no está habilitada de manera predeterminada en el área de trabajo Log Analytics. Para usar los datos de **LAQueryLogs** al auditar en Microsoft Sentinel, primero habilite **LAQueryLogs** en el área **Configuración de diagnóstico** del área de trabajo de Log Analytics.

    Para más información, vea [Auditoría de las consultas en los registros de Azure Monitor](../azure-monitor/logs/query-audit.md).

1. A continuación, consulte los datos mediante KQL, tal como lo haría con cualquier otra tabla.

    Por ejemplo, la consulta siguiente muestra el número de consultas que se ejecutaron en la última semana, cada día:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

En las secciones siguientes se muestran más consultas de ejemplo que se ejecutan en la tabla **LAQueryLogs** al auditar actividades en el entorno de SOC con Microsoft Sentinel.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>El número de consultas ejecutadas en las que la respuesta no era "OK"

La siguiente consulta de la tabla **LAQueryLogs** muestra el número de consultas ejecutadas, donde la respuesta recibida no es la respuesta de HTTP **200 OK**. Por ejemplo, este número incluirá las consultas que no se han podido ejecutar.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Visualización de los usuarios de consultas con uso intensivo de CPU

La siguiente consulta de la tabla **LAQueryLogs** indica los usuarios que ejecutaron la mayoría de las consultas de uso intensivo de CPU, en función de la CPU usada y la duración del tiempo de la consulta.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Visualización de los usuarios que ejecutaron la mayoría de las consultas de la semana pasada

La siguiente consulta de la tabla **LAQueryLogs** muestra los usuarios que ejecutaron la mayoría de las consultas durante la semana pasada.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-microsoft-sentinel-activities"></a>Configuración de alertas para las actividades de Microsoft Sentinel

Puede que quiera usar recursos de auditoría de Microsoft Sentinel para crear alertas proactivas.

Por ejemplo, si tiene tablas confidenciales en el área de trabajo de Microsoft Sentinel, use la siguiente consulta para que se le notifique cada vez que se consulten las tablas:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```

## <a name="monitor-microsoft-sentinel-with-workbooks-rules-and-playbooks"></a>Supervisión de Microsoft Sentinel con libros, reglas y cuadernos de estrategias

Use las propias características de Microsoft Sentinel para supervisar los eventos y las acciones que se producen en Microsoft Sentinel.

- **Supervise con libros**. Los siguientes libros se crearon para supervisar la actividad de las áreas de trabajo:

  - **Auditoría de áreas de trabajo**. Incluye información sobre qué usuarios del entorno realizan acciones, qué acciones han realizado, etc.
  - **Eficacia de los análisis**. Proporciona información sobre qué reglas de análisis se usan, qué tácticas de MITRE están más cubiertas y qué incidentes se generaron a partir de las reglas.
  - **Eficiencia de las operaciones de seguridad**. Presenta métricas sobre el rendimiento del equipo de SOC, los incidentes abiertos, los incidentes cerrados y mucho más. Este libro se puede usar para mostrar el rendimiento del equipo y destacar las áreas faltantes a las que haya que prestar atención.
  - **Seguimiento del estado de la recopilación de datos**. Ayuda a inspeccionar las ingestas paradas o detenidas.

  Para obtener más información, consulte [Libros de Microsoft Sentinel que se usan comúnmente](top-workbooks.md).

- **Observe el retraso de ingesta**.  Si le preocupa el retraso de ingesta, [establezca una variable en una regla de análisis](ingestion-delay.md) para representar el retraso.

  Por ejemplo, la siguiente regla de análisis puede ayudar a garantizar que los resultados no incluyan duplicados y que los registros no se pierden al ejecutar las reglas:

  ```kusto
  let ingestion_delay= 2min;let rule_look_back = 5min;CommonSecurityLog| where TimeGenerated >= ago(ingestion_delay + rule_look_back)| where ingestion_time() > (rule_look_back)
  - Calculating ingestion delay
    CommonSecurityLog| extend delay = ingestion_time() - TimeGenerated| summarize percentiles(delay,95,99) by DeviceVendor, DeviceProduct
  ```

  Para obtener más información, consulte [Automatización del control de incidentes en Microsoft Sentinel con las reglas de automatización](automate-incident-handling-with-automation-rules.md).

- **Supervise el estado del conector de datos** mediante el cuaderno de la [solución de notificaciones push del estado de los conectores](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Send-ConnectorHealthStatus) para ver si la ingesta está parada o detenida, y envíe notificaciones cuando un conector haya dejado de recopilar datos o las máquinas hayan dejado de informar.

## <a name="next-steps"></a>Pasos siguientes

En Microsoft Sentinel, use el libro **Workspace audit** (Auditoría de área de trabajo) para auditar las actividades en el entorno de SOC.

Para más información, consulte [Visualización y supervisión de los datos](monitor-your-data.md).
