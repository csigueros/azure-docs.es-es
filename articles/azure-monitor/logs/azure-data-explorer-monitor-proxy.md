---
title: Consulta de datos en Azure Monitor mediante Azure Data Explorer
description: Use Azure Data Explorer para realizar consultas entre productos entre Azure Data Explorer, áreas de trabajo de Log Analytics y aplicaciones clásicas de Application Insights en Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 9faa9ff9c0635c84ebc5c56a343db0426873f945
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735836"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer"></a>Consulta de datos en Azure Monitor mediante Azure Data Explorer

Azure Data Explorer admite las consultas entre los servicios Azure Data Explorer, [Application Insights (AI)](../app/app-insights-overview.md) y [Log Analytics (LA)](./data-platform-logs.md). Asimismo, puede consultar el área de trabajo de Log Analytics/Application Insights mediante herramientas de Azure Data Explorer y hacer referencia a la misma en una consulta entre servicios. En este artículo se muestra cómo crear una consulta entre servicios y cómo agregar el área de trabajo de Log Analytics/Application Insights a la interfaz de usuario web de Azure Data Explorer.

Flujo de consultas entre servicios de Azure Data Explorer: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Flujo del proxy de Azure Data Explorer.":::

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Incorporación de un área de trabajo de Log Analytics/Application Insights a herramientas de cliente de Azure Data Explorer

1. Compruebe que el clúster nativo de Azure Data Explorer (como el clúster *help*) aparece en el menú izquierdo antes de conectarse al clúster de Log Analytics o de Application Insights.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Clúster nativo de Azure Data Explorer.":::

 En la interfaz de usuario de Azure Data Explorer (https://dataexplorer.azure.com/clusters), seleccione **Agregar clúster**.

2. En la ventana **Agregar clúster**, agregue la dirección URL al clúster de LA o AI.

    * Para LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Para AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Seleccione **Agregar**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Agregue el clúster.":::
 
>[!NOTE]
>Si agrega una conexión a más de un área de trabajo de Log Analytics/Application Insights, asigne a cada una un nombre diferente. En caso contrario, todos tendrán el mismo nombre en el panel izquierdo.

 Una vez que se establece la conexión, el área de trabajo de Log Analytics o de Application Insights aparecerá en el panel de la izquierda con el clúster nativo de Azure Data Explorer.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Clústeres de Log Analytics y de Azure Data Explorer.":::
 
> [!NOTE]
> El número de áreas de trabajo de Azure Monitor que se pueden asignar está limitado a 100.

## <a name="create-queries-using-azure-monitor-data"></a>Creación de consultas con datos de Azure Monitor

Puede ejecutar las consultas usando herramientas de cliente que admitan consultas de Kusto, como: Kusto Explorer, interfaz de usuario web de Azure Data Explorer, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens, API de REST.

> [!NOTE]
> La capacidad de consulta entre servicios se usa solo para la recuperación de datos. Para más información, consulte el apartado [Compatibilidad con funciones](#function-supportability).

> [!TIP]
> * El nombre de la base de datos debe tener el mismo nombre que el recurso especificado en la consulta entre servicios. Los nombres distinguen mayúsculas de minúsculas.
> * En consultas entre clústeres, asegúrese de que la nomenclatura de las aplicaciones de Application Insights y las áreas de trabajo de Log Analytics sea correcta.
> * Si los nombres contienen caracteres especiales, se sustituyen por la codificación URL de la consulta entre servicios.
> * Si los nombres incluyen caracteres que no cumplen las [reglas del nombre del identificador KQL](/azure/data-explorer/kusto/query/schema-entities/entity-names), se sustituyen por el carácter de guion **-** .

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Consulta directa en las áreas de trabajo de Log Analytics o Application Insights desde las herramientas de cliente de Azure Data Explorer

Ejecute consultas en las áreas de trabajo de Log Analytics o de Application Insights. Compruebe que el área de trabajo está seleccionada en el panel izquierdo.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Consulte el área de trabajo de Log Analytics.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Consulta cruzada de las instancias de Log Analytics o de Application Insights y el clúster nativo de Azure Data Explorer

Al ejecutar consultas de servicios entre clústeres, compruebe que el clúster nativo de Azure Data Explorer está seleccionado en el panel izquierdo. En los ejemplos siguientes se muestra cómo combinar tablas de clúster de Azure Data Explorer mediante el operador [union](/azure/data-explorer/kusto/query/unionoperator) con un área de trabajo de Log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Consulta entre servicios desde Azure Data Explorer":::.

>[!TIP]
>* Usar el [`join` operador](/azure/data-explorer/kusto/query/joinoperator), en lugar de union, puede requerir una [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) para ejecutarla en un clúster nativo de Azure Data Explorer.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Combinación de datos desde un clúster de Azure Data Explorer de un inquilino con un recurso de Azure Monitor de otro inquilino

No se admiten las consultas entre inquilinos en los servicios. Ha iniciado sesión en un solo inquilino para ejecutar la consulta que abarca ambos recursos.

Si el recurso de Azure Data Explorer está en el inquilino "A" y el área de trabajo de Log Analytics está en el inquilino "B", use uno de los dos métodos siguientes:

1. Azure Data Explorer permite agregar roles para entidades de seguridad de distintos inquilinos. Agregue su identificador de usuario en el inquilino "B" como usuario autorizado en el clúster de Azure Data Explorer. Compruebe que la propiedad *["TrustedExternalTenant"](/powershell/module/az.kusto/update-azkustocluster)* del clúster de Azure Data Explorer contiene al inquilino "B". Ejecute la consulta cruzada completamente en el inquilino "B".

2. Use [Lighthouse](../../lighthouse/index.yml) para proyectar el recurso de Azure Monitor en el inquilino "A".
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Conexión a clústeres de Azure Data Explorer desde otros inquilinos

Kusto Explorer inicia sesión automáticamente en el inquilino al que pertenece originalmente la cuenta de usuario. Para acceder a los recursos de otros inquilinos con la misma cuenta de usuario, se tiene que especificar explícitamente el valor de `tenantId` en la cadena de conexión: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Compatibilidad con funciones

Las consultas entre servicios de Azure Data Explorer admiten funciones tanto para Application Insights como para Log Analytics.
Esta funcionalidad permite que las consultas entre clústeres hagan referencia directamente a una función tabular de Azure Monitor.
Los siguientes comandos se admiten en la consulta entre servicios:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

En la imagen siguiente se muestra un ejemplo de cómo consultar una función tabular desde la interfaz de usuario web de Azure Data Explorer.
Para usar la función, ejecute el nombre en la ventana Consulta.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Consulta de una función tabular desde la interfaz de usuario web de Azure Data Explorer.":::

## <a name="additional-syntax-examples"></a>Ejemplos de sintaxis adicionales

Las opciones de sintaxis siguientes están disponibles cuando se llama a los clústeres de Log Analytics o de Application Insights:

|Descripción de la sintaxis  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de datos dentro de un clúster que contiene solo el recurso definido en esta suscripción (**recomendado para las consultas entre clústeres**) |   clúster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | clúster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Clúster que contiene todas las aplicaciones o áreas de trabajo en esta suscripción    |     clúster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    clúster (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Clúster que contiene todas las aplicaciones o áreas de trabajo de la suscripción y que son miembros de este grupo de recursos    |   clúster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    clúster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Clúster que contiene solo el recurso definido en esta suscripción      |    clúster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  clúster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Pasos siguientes

- Lea más sobre la [estructura de datos de las áreas de trabajo de Log Analytics y Application Insights](data-platform-logs.md).
- Aprenda a [escribir consultas en Azure Data Explorer](/azure/data-explorer/write-queries).