---
title: Supervisión de Azure Key Vault
description: Comience aquí para obtener información sobre cómo supervisar Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/21/2021
ms.author: mbaldwin
ms.custom: subject-monitoring
ms.openlocfilehash: 753dbf0bdaa1e97a04422b567dcbc199717bbd71
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207330"
---
# <a name="monitoring-azure-key-vault"></a>Supervisión de Azure Key Vault

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. En el caso de Azure Key Vault, es importante supervisar el servicio a medida que empiece a escalarlo, ya que aumentará el número de solicitudes enviadas al almacén de claves. Es probable que aumente la latencia de las solicitudes y, en casos extremos, puede hacer que las solicitudes se limiten, lo cual afectará al rendimiento del servicio.

En este artículo se describen los datos de supervisión que genera Key Vault. Key Vault usa [Azure Monitor](/azure/azure-monitor/overview). Si no está familiarizado con las características de Azure Monitor comunes a todos los servicios de Azure que lo usan, consulte [Supervisión de recursos de Azure con Azure Monitor](/azure/azure-monitor/essentials/monitor-azure-resource).

## <a name="monitoring-overview-page-in-azure-portal"></a>Página de información general de supervisión en Azure Portal

La página **Información general** de Azure Portal para cada almacén de claves incluye las métricas siguientes en la pestaña "Supervisión":

- Total de solicitudes
- Latencia media
- Índice de éxito

Puede seleccionar "Métricas adicionales" (o la pestaña "Métricas" en la barra lateral izquierda, en "Supervisión") para ver también estas métricas:

- Latencia general de la API de servicio
- Disponibilidad general del almacén
- Saturación general del almacén
- Número total de visitas de la API de servicio
- Número total de resultados de la API de servicio

## <a name="key-vault-insights"></a>Información detallada de Key Vault

Algunos servicios en Azure tienen un panel de supervisión pregenerado enfocado especial en Azure Portal que proporciona un punto inicial para supervisar el servicio. Estos paneles especiales se llaman "conclusiones".

La información de Key Vault proporciona una supervisión completa de los almacenes de claves y ofrece una vista unificada del rendimiento, los errores, la latencia y las solicitudes de Key Vault. Para obtener todos los detalles, consulte [Supervisión de un servicio de Key Vault con información de Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="monitoring-data"></a>Supervisión de datos

Key Vault recopila los mismos tipos de datos de supervisión que otros recursos de Azure que se describen en [Supervisión de datos de recursos de Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data-from-Azure-resources).

Consulte el artículo [Supervisión de la referencia de los datos de *Key Vault*](monitor-key-vault-reference.md) para obtener información detallada sobre las métricas y las métricas de registros creadas por Key Vault.

## <a name="collection-and-routing"></a>Recopilación y enrutamiento

Las métricas de la plataforma y el registro de actividad se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.  

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o varias ubicaciones.

Consulte [Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure](../../azure-monitor/platform/diagnostic-settings.md) para ver el proceso detallado para crear una configuración de diagnóstico mediante el Azure Portal, la CLI o PowerShell. Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar. Las categorías de *Key Vault* se enumeran en la [referencia de datos de supervisión de Key Vault](monitor-key-vault-reference.md#resource-logs).

A fin de crear una configuración de diagnóstico para el almacén de claves, consulte [Habilitación del registro de Key Vault](howto-logging.md).  En las secciones siguientes se describen las métricas y los registros que se pueden recopilar.

## <a name="analyzing-metrics"></a>Análisis de métricas

Las métricas de Key Vault se pueden analizar con métricas de otros servicios de Azure mediante el Explorador de métricas. Para ello, hay que abrir **Métricas** en el menú **Azure Monitor**. Consulte [Introducción al explorador de métricas de Azure](/azure/azure-monitor/platform/metrics-getting-started) para más información sobre esta herramienta.

En [Supervisión de la referencia de los datos de Key Vault](monitor-key-vault-reference.md#metrics) puede consultar una lista de las métricas de la plataforma que se recopilan para Key Vault.  

## <a name="analyzing-logs"></a>Análisis de datos

Los datos de los registros de Azure Monitor se almacenan en tablas, cada una con un conjunto propio de propiedades únicas.  

Todos los registros de recursos de Azure Monitor tienen los mismos campos seguidos de campos específicos del servicio. El esquema común se describe en [Esquemas del registro de recursos de Azure Monitor](../../azure-monitor/platform/diagnostic-logs-schema.md#top-level-resource-logs-schema). 

El [registro de actividad](../../azure-monitor/platform/activity-log.md) es un tipo de registro de plataforma de Azure que proporciona conclusiones sobre los eventos del nivel de suscripción. Puede verlo de forma independiente o enrutarlo a registros de Azure Monitor, donde puede realizar consultas mucho más complejas mediante Log Analytics.  

En [Supervisión de la referencia de los datos de Key Vault](monitor-key-vault-reference.md#resource-logs) puede consultar una lista de los tipos de registros de recursos que se recopilan para Key Vault.  

En [Supervisión de la referencia de los datos de Key Vault](monitor-key-vault-reference.md#azure-monitor-logs-tables) puede consultar una lista de las tablas utilizadas por los registros de Azure Monitor y en las que Log Analytics puede realizar consultas.  

### <a name="sample-kusto-queries"></a>Ejemplos de consultas de Kusto

> [!IMPORTANT]
> Al seleccionar **Registros** en el menú de Key Vault, Log Analytics se abre con el ámbito de la consulta establecido en el almacén de claves actual. Esto significa que las consultas de registro solo incluirán datos de ese recurso. Si quiere ejecutar una consulta que incluya datos de otros almacenes de claves o servicios de Azure, seleccione **Registros** en el menú **Azure Monitor**. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](/azure/azure-monitor/log-query/scope/) para obtener más información.

Estas son algunas consultas que puede escribir en la barra de búsqueda **Búsqueda de registros** para ayudarle a supervisar los recursos de Key Vault. Estas consultas funcionan con el [nuevo lenguaje](/azure-monitor/logs/log-query-overview).

* ¿Hay alguna solicitud lenta?

    ```Kusto
    // List of KeyVault requests that took longer than 1sec. 
    // To create an alert for this query, click '+ New alert rule'
    let threshold=1000; // let operator defines a constant that can be further used in the query

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where DurationMs > threshold
    | summarize count() by OperationName, _ResourceId
    ```

* ¿Hay algún error?

    ```Kusto
    // Count of failed KeyVault requests by status code. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where httpStatusCode_d >= 300 and not(OperationName == "Authentication" and httpStatusCode_d == 401)
    | summarize count() by requestUri_s, ResultSignature, _ResourceId
    // ResultSignature contains HTTP status, e.g. "OK" or "Forbidden"
    // httpStatusCode_d contains HTTP status code returned
    ```

* Errores de deserialización de entrada

    ```Kusto
    // Shows errors caused due to malformed events that could not be deserialized by the job. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.KEYVAULT" and parse_json(properties_s).DataErrorType in ("InputDeserializerError.InvalidData", "InputDeserializerError.TypeConversionError", "InputDeserializerError.MissingColumns", "InputDeserializerError.InvalidHeader", "InputDeserializerError.InvalidCompressionType")
    | project TimeGenerated, Resource, Region_s, OperationName, properties_s, Level, _ResourceId
    ```

* ¿Qué grado de actividad ha tenido este KeyVault?

    ```Kusto
    //  
    // Line chart showing trend of KeyVault requests volume, per operation over time. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize count() by bin(TimeGenerated, 1h), OperationName // Aggregate by hour
    | render timechart

    ```

* ¿Quién está llamando a este KeyVault? 

    ```Kusto
    // List of callers identified by their IP address with their request count.  
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT"
    | summarize count() by CallerIPAddress
    ```

* ¿Con qué rapidez está atendiendo este KeyVault las solicitudes? 

    ```Kusto
    // Line chart showing trend of request duration over time using different aggregations. 
 
    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize avg(DurationMs) by requestUri_s, bin(TimeGenerated, 1h) // requestUri_s contains the URI of the request
    | render timechart
    ```

* ¿Qué cambios se produjeron el mes pasado? 

    ```Kusto
    // Lists all update and patch requests from the last 30 days. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where TimeGenerated > ago(30d) // Time range specified in the query. Overrides time picker in portal.
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where OperationName == "VaultPut" or OperationName = "VaultPatch"
    | sort by TimeGenerated desc
    ```


## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../../azure-monitor/platform/alerts-metric-overview.md), [registros](../../azure-monitor/platform/alerts-unified-log.md) y el [registro de actividad](../../azure-monitor/platform/activity-log-alerts.md). Cada tipo de alerta tiene sus ventajas y desventajas.

Si crea o ejecuta una aplicación que se ejecuta en Azure Key Vault, [Azure Monitor Application Insights](/azure-monitor/overview#application-insights.md) puede ofrecer otros tipos de alertas.

Estas son algunas reglas de alertas comunes y recomendadas de Azure Key Vault.

- La disponibilidad de Key Vault cae por debajo del 100 % (umbral estático)
- La latencia de Key Vault es superior a 500 ms (umbral estático)
- La saturación total del almacén es superior al 75 % (umbral estático)
- La saturación total del almacén supera el promedio (umbral dinámico)
- Códigos de error totales superiores al promedio (umbral dinámico)

Si desea obtener más detalles, consulte [Supervisión y alertas de Azure Key Vault](alert.md).

## <a name="next-steps"></a>Pasos siguientes

Enhorabuena, ahora está supervisando Azure Key Vault. Si también desea configurar alertas, consulte [Supervisión y alertas de Azure Key Vault](alert.md).

- Consulte [Supervisión de la referencia de los datos de Azure Key Vault](monitor-key-vault-reference.md) para obtener una referencia de las métricas, los registros y otros valores importantes creados por Key Vault.
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).
