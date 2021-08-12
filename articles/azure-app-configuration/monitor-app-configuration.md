---
title: Supervisión de Azure App Configuration
description: Comience aquí para obtener información sobre cómo supervisar App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: 7edddaac52c569bf29c1be7e173ce966adb0c17b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969748"
---
# <a name="monitoring-app-configuration"></a>Supervisión de App Configuration
Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. 

En este artículo se describen los datos de supervisión generados por App Configuration. App Configuration usa [Azure Monitor](../azure-monitor/overview.md). Si no está familiarizado con las características de Azure Monitor comunes a todos los servicios de Azure que lo usan, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="monitoring-overview-page-in-azure-portal"></a>Página de información general de supervisión en Azure Portal
La página **Información general** de Azure Portal incluye una breve vista del uso de los recursos, como el número total de solicitudes, el número de solicitudes limitadas y la duración de la solicitud por almacén de configuración. Esta información es útil, pero solo muestra una pequeña cantidad de datos de supervisión. Algunos de estos datos de supervisión se recopilan automáticamente y están disponibles para su análisis en cuanto se crea el recurso. Puede habilitar tipos adicionales de recopilación de datos con cierta configuración adicional.

> [!div class="mx-imgBorder"]
> ![Supervisión en la página Información general](./media/monitoring-overview-page.png)

## <a name="monitoring-data"></a>Supervisión de datos 
App Configuration recopila los mismos tipos de datos de supervisión que otros recursos de Azure que se describen en [Supervisión de datos de recursos de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). Consulte [Referencia de supervisión de datos de App Configuration](./monitor-app-configuration-reference.md) para obtener información detallada sobre las métricas y las métricas de registros creadas por App Configuration.

## <a name="collection-and-routing"></a>Recopilación y enrutamiento
Tanto las métricas de la plataforma como el registro de actividad se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o varias ubicaciones. Por ejemplo, para ver los registros y las métricas de un almacén de configuración casi en tiempo real en Azure Monitor, recopile los registros de recursos en un área de trabajo de Log Analytics. Si aún no tiene una, cree un [área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md) y siga estos pasos para crear y habilitar una configuración de diagnóstico. 

 #### <a name="portal"></a>[Portal](#tab/portal)

1. Inicie sesión en Azure Portal.

1. Vaya al almacén de App Configuration.

1. En la sección **Supervisión**, seleccione **Configuración de diagnóstico** y seleccione **+Agregar configuración de diagnóstico**. 
    > [!div class="mx-imgBorder"]
    > ![Adición de una configuración de diagnóstico](./media/diagnostic-settings-add.png)

1. En la página **Configuración de diagnóstico**, escriba un nombre para la configuración y, a continuación, seleccione **HttpRequest** y elija el destino al que enviar los registros. Para enviarlos a un área de trabajo de Log Analytics, elija **Enviar al área de trabajo de Log Analytics**.
 
    > [!div class="mx-imgBorder"]
    > ![Detalles de la configuración de diagnóstico](./media/monitoring-diagnostic-settings-details.png)

1. Escriba el nombre de la **suscripción** y del **área de trabajo de Log Analytics**. 
1. Seleccione **Guardar** y compruebe que la página Configuración de diagnóstico ahora muestra la nueva configuración de diagnóstico. 
    

 ### <a name="azure-cli"></a>[CLI de Azure](#tab/cli)
    
1. Abra Azure Cloud Shell o, si ha instalado la CLI de Azure localmente, abra una aplicación de consola de comandos como Windows PowerShell.

1. Si su identidad se asocia a más de una suscripción, establezca su suscripción activa como la suscripción de la cuenta de almacenamiento en la que quiera habilitar los registros.

    ```Azure CLI
    az account set --subscription <your-subscription-id>
    ```

1. Habilite los registros mediante el comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create&preserve-view=true).

    ```Azure CLI
    az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <app-configuration-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
    ```

 ### <a name="powershell"></a>[PowerShell](#tab/PowerShell)
    
1. Abra una ventana de comandos de Windows PowerShell e inicie sesión en su suscripción a Azure con el comando Connect-AzAccount. A continuación, siga las instrucciones que aparecen en pantalla.

    ```PowerShell
    Connect-AzAccount
    ```

1. Establezca la suscripción activa como la suscripción de la cuenta de App Configuration en la que quiere habilitar el registro.

    ```PowerShell
    Set-AzContext -SubscriptionId <subscription-id>
    ```
    
1. Para habilitar los registros de un área de trabajo de Log Analytics, use el cmdlet [Set-AzDiagnosticSetting de PowerShell.](/previous-versions/azure/mt631625(v=azure.100)?redirectedfrom=MSDN) 

    ```PowerShell
    Set-AzDiagnosticSetting -ResourceId <app-configuration-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true
    ```
1. Compruebe que la configuración de diagnóstico está establecida correctamente y que las categorías de registro están habilitadas. 

    ```PowerShell
    Get-AzureRmDiagnosticSetting -ResourceId <app-configuration-resource-id> 
    ```
---
Para más información sobre la creación de una configuración de diagnóstico mediante Azure Portal, la CLI o PowerShell, consulte [Creación de una configuración de diagnóstico para recopilar registros y métricas de una plataforma en Azure](../azure-monitor/essentials/diagnostic-settings.md). 

Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar. Para más información sobre las categorías de registros de App Configuration, consulte [Referencia de datos de supervisión de App Configuration](./monitor-app-configuration-reference.md#resourcelogs).

## <a name="analyzing-metrics"></a>Análisis de métricas

Puede analizar las métricas de App Configuration con las de otros servicios de Azure mediante el explorador de métricas si abre **Métricas** en el menú de **Azure Monitor**. Consulte [Introducción al explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) para más información sobre esta herramienta. Para App Configuration, se recopilan las métricas siguientes: 

* Número de solicitudes entrantes HTTP 
* Duración de las solicitudes entrantes HTTP 
* Recuento de solicitudes HTTP limitadas (respuestas del código de estado HTTP 429)

En el portal, vaya a la sección **Métricas** y seleccione los **espacios de nombres de métricas** y las **métricas** que desea analizar. En esta captura de pantalla se muestra la vista de métricas al seleccionar **Número de solicitudes entrantes HTTP** para el almacén de configuración.

> [!div class="mx-imgBorder"]
> ![Uso de métricas de App Configuration](./media/monitoring-analyze-metrics.png)

Para obtener una lista de las métricas de plataforma recopiladas para App Configuration, consulte [Supervisión de métricas de referencia de datos de App Configuration](./monitor-app-configuration-reference.md#metrics). Como referencia, puede ver también una lista de [todas las métricas de recursos que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="analyzing-logs"></a>Análisis de datos
Los datos de los registros de Azure Monitor se almacenan en tablas, cada una con un conjunto propio de propiedades únicas. El esquema común se describe en [Esquema de registros de recursos de Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). 

El [registro de actividad](../azure-monitor/essentials/activity-log.md) es un registro de plataforma de Azure que proporciona conclusiones sobre los eventos del nivel de suscripción. Puede verlo de forma independiente o enrutarlo a registros de Azure Monitor, donde puede realizar consultas mucho más complejas mediante Log Analytics.  
Para obtener una lista de los tipos de registros de recursos recopilados para App Configuration, consulte [Referencia de supervisión de datos de App Configuration](./monitor-app-configuration-reference.md#resourcelogs). Para obtener una lista de las tablas utilizadas por los registros de Azure Monitor y en la que Log Analytics puede realizar consultas, consulte [Referencia de supervisión de datos de App Configuration](./monitor-app-configuration-reference.md#azuremonitorlogstables).  

>[!IMPORTANT]
> Al seleccionar **Registros** en el menú de App Configuration, Log Analytics se abre con el ámbito de consulta establecido en el recurso de configuración de la aplicación actual. Esto significa que las consultas de registro solo incluirán datos de ese recurso. 


Si quiere ejecutar una consulta que incluya datos de otra configuración o de otros servicios de Azure, seleccione **Registros** en el menú **Azure Monitor**. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](/azure/azure-monitor/log-query/scope/) para obtener más información.

En el portal, vaya a la sección **Registros** y, a continuación, al editor de consultas. A la izquierda, en la pestaña **Tablas**, seleccione **AACHttpRequest** para ver los registros del almacén de configuración. Escriba una consulta de Kusto en el editor y los resultados aparecerán a continuación.  

> [!div class="mx-imgBorder"]
> ![Escritura de consultas de Kusto en nuestros registros](./media/monitoring-writing-queries.png)

A continuación se muestran las consultas de ejemplo que se pueden usar para supervisar los recursos de App Configuration: 



* Enumeración de todas las solicitudes HTTP de los últimos tres días 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
    ```

* Enumeración de todas las solicitudes limitadas (código de estado HTTP 429 devuelto para demasiadas solicitudes) de los últimos tres días 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | where StatusCode == "429"
    ```

* Enumeración del número de solicitudes enviadas en los últimos tres días por dirección IP 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount= count() by ClientIPAddress
        | order by requestCount desc 
    ```

* Creación de un gráfico circular de los tipos de códigos de estado recibidos en los últimos tres días
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount=count() by StatusCode
        | order by requestCount desc 
        | render piechart 
    ```

* Enumeración del número de solicitudes enviadas por día durante los últimos 14 días
    ```Kusto
    AACHttpRequest
        | where TimeGenerated > ago(14d)
        | extend Day = startofday(TimeGenerated)
        | summarize requestcount=count() by Day
        | order by Day desc  
    ```

## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [registros](../azure-monitor/alerts/alerts-unified-log.md) y el [registro de actividad](../azure-monitor/alerts/activity-log-alerts.md). Cada tipo de alerta tiene sus ventajas y desventajas.
En la tabla siguiente se enumeran las reglas de alertas de métricas comunes y recomendadas para App Configuration:

| Tipo de alerta | Condición | Descripción  |
|:---|:---|:---|
|Límite de velocidad en solicitudes HTTP | Código de estado = 429  | El almacén de configuración ha superado la [cuota de solicitudes por hora](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration). Actualice a un almacén estándar o siga los [procedimientos recomendados](./howto-best-practices.md#reduce-requests-made-to-app-configuration) para optimizar el uso. |



## <a name="next-steps"></a>Pasos siguientes

* Consulte [Referencia de supervisión de datos de App Configuration](./monitor-app-configuration-reference.md) para obtener una referencia de las métricas, los registros y otros valores importantes creados por App Configuration.

* Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).