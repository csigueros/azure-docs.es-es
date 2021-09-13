---
title: Consulta entre recursos en Azure Data Explorer mediante Azure Monitor
description: Use Azure Monitor para realizar consultas entre productos entre Azure Data Explorer, áreas de trabajo de Log Analytics y aplicaciones clásicas de Application Insights en Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 498fc101f257b05d24826cead8906b513ec34ccd
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324533"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Consulta entre recursos en Azure Data Explorer mediante Azure Monitor
Azure Monitor admite las consultas entre los servicios Azure Data Explorer, [Application Insights](../app/app-insights-overview.md) y [Log Analytics](../logs/data-platform-logs.md). Después puede consultar el clúster de Azure Data Explorer mediante las herramientas de Log Analytics o Application Insights y hacer referencia al mismo en una consulta entre servicios. En el artículo se muestra cómo realizar una consulta entre servicios.

En el diagrama siguiente se muestra el flujo entre servicios de Azure Monitor:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagrama que muestra el flujo de consultas entre un usuario, Azure Monitor, un proxy y Azure Data Explorer.":::

>[!NOTE]
> La consulta entre servicios de Azure Monitor está en versión preliminar pública. Póngase en contacto con el [equipo de servicio](mailto:ADXProxy@microsoft.com) si tiene alguna duda.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Consulta entre los recursos de Log Analytics o Application Insights y Azure Data Explorer

Puede ejecutar las consultas entre recursos usando herramientas de cliente que admitan consultas de Kusto. Entre los ejemplos de estas herramientas se incluyen la interfaz de usuario web de Log Analytics, Workbooks, PowerShell y la API REST.

Escriba el identificador de un clúster de Azure Data Explorer en una consulta dentro del patrón `adx`, seguido del nombre y la tabla de la base de datos.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Captura de pantalla que muestra un ejemplo de una consulta entre servicios.":::

> [!NOTE]
>* Los nombres de las bases de datos distinguen mayúsculas de minúsculas.
>* No se admite la consulta entre recursos como una alerta.
>* No se admite la identificación de la columna Marca de tiempo en el clúster. La API de consulta de Log Analytics no se pasará con el filtro de tiempo.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Combinación de tablas de clúster de Azure Data Explorer con un área de trabajo de Log Analytics

Use el comando `union` para combinar las tablas de clúster con un área de trabajo de Log Analytics.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Captura de pantalla que muestra un ejemplo de consulta entre servicios con el comando &quot;union&quot;.":::

> [!Tip]
> Se permite el formato abreviado: *nombreDeClúster*/*catálogoInicial*. Por ejemplo, `adx('help/Samples')` se convierte en `adx('help.kusto.windows.net/Samples')`.

>[!Note]
> 
>* Con el operador [`join` ](/azure/data-explorer/kusto/query/joinoperator), en lugar de union, debe usar una [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) para combinar los datos del clúster de Azure Data Explorer con el área de trabajo de Log Analytics.
>* Use Hint.remote={dirección del área de trabajo de Log Analytics}, por ejemplo:
>```kusto
>AzureDiagnostics
>| join hint.remote=left adx("cluster=ClusterURI").AzureDiagnostics on (ColumnName)
>```

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Combinación de datos desde un clúster de Azure Data Explorer de un inquilino con un recurso de Azure Monitor de otro inquilino

No se admiten las consultas entre inquilinos en los servicios. Debe iniciar sesión en un solo inquilino para ejecutar la consulta que abarca ambos recursos.

Si el recurso de Azure Data Explorer está en el inquilino A y el área de trabajo de Log Analytics está en el inquilino B, use uno de los métodos siguientes:

*  Azure Data Explorer permite agregar roles para entidades de seguridad de distintos inquilinos. Agregue su identificador de usuario en el inquilino B como usuario autorizado en el clúster de Azure Data Explorer. Compruebe que la propiedad [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) del clúster de Azure Data Explorer contiene al inquilino B. Ejecute la consulta cruzada completamente en el inquilino B.
*  Use [Lighthouse](../../lighthouse/index.yml) para proyectar el recurso de Azure Monitor en el inquilino A.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Conexión a clústeres de Azure Data Explorer desde otros inquilinos

Kusto Explorer inicia sesión automáticamente en el inquilino al que pertenece originalmente la cuenta de usuario. Para acceder a los recursos de otros inquilinos con la misma cuenta de usuario, tiene que especificar explícitamente el valor de `TenantId` en la cadena de conexión:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Pasos siguientes
* [Escritura de consultas](/azure/data-explorer/write-queries)
* [Consulta de datos en Azure Monitor mediante Azure Data Explorer](/azure/data-explorer/query-monitor-data)
* [Consulta de registros entre recursos en Azure Monitor](../logs/cross-workspace-query.md)