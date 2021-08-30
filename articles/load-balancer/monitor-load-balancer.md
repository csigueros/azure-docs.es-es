---
title: Supervisión de Azure Load Balancer
description: Empiece aquí para aprender a supervisar un equilibrador de carga.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 62885e889c64c40c051f838178f9a96d4ac4a8ee
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291188"
---
# <a name="monitoring-load-balancer"></a>Supervisión de un equilibrador de carga

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. 

En este artículo se describen los datos de supervisión que genera Load Balancer. Load Balancer usa [Azure Monitor](../azure-monitor/overview.md). Si no está familiarizado con las características de Azure Monitor comunes a todos los servicios de Azure que lo usan, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="load-balancer-insights"></a>Conclusiones sobre el equilibrador de carga

Algunos servicios en Azure tienen un panel de supervisión pregenerado enfocado especial en Azure Portal que proporciona un punto inicial para supervisar el servicio. Estos paneles especiales se denominan "conclusiones".

Las conclusiones de Load Balancer proporcionan:

* Vista de dependencia funcional
* Panel de métricas
* Pestaña Información general
* Pestaña Disponibilidad de front-end y back-end
* Pestaña Rendimiento de los datos
* Distribución del flujo
* Monitores de conexión
* Definiciones de métricas

Para más información sobre las conclusiones de Load Balancer, consulte [Uso de la información para supervisar y configurar Azure Load Balancer](./load-balancer-insights.md).

## <a name="monitoring-data"></a>Supervisión de datos 

Load Balancer recopila los mismos tipos de datos de supervisión que otros recursos de Azure, que se describen en [Supervisión de datos de recursos de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte el artículo sobre la [supervisión de referencias de datos de Load Balancer](monitor-load-balancer.md) para obtener información detallada sobre métricas y las métricas de registros que crea Load Balancer.

Load Balancer proporciona datos adicionales de supervisión a través de:

* [Sondeos de estado](./load-balancer-custom-probe-overview.md)
* [Estado de mantenimiento de los recursos](./load-balancer-standard-diagnostics.md#resource-health-status)
* [REST API](./load-balancer-query-metrics-rest-api.md)

## <a name="collection-and-routing"></a>Recopilación y enrutamiento

Las métricas de la plataforma y el registro de actividad se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.  

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o varias ubicaciones.

## <a name="creating-a-diagnostic-setting"></a>Creación de una configuración de diagnóstico

Puede crear una configuración de diagnóstico con Azure Portal, PowerShell o la CLI de Azure.


Para obtener instrucciones generales, consulte [Creación de una configuración de diagnóstico para recopilar registros y métricas de la plataforma en Azure](../azure-monitor/essentials/diagnostic-settings.md).

Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar. La categoría para Load Balancer es **AllMetrics**

### <a name="portal"></a>Portal

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**.

3. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

4. Seleccione el equilibrador de carga. En este ejemplo, se usa **myLoadBalancer**.

5. En la sección **Supervisión** de **myLoadBalancer**, seleccione **Configuración de diagnóstico**.

6. En **Configuración de diagnóstico**, seleccione **+ Agregar configuración de diagnóstico**.

7. Escriba o seleccione la información siguiente en **Configuración de diagnóstico**.

    | Configuración | Value |
    | ------- | ----- |
    | Nombre de la configuración de diagnóstico | Escriba un nombre para la configuración de diagnóstico. |
    | **Detalles de categoría** |   |
    | métrica | Seleccione **AllMetrics**. |

8. Seleccione **Detalles del destino**. Estas son algunas de las opciones de destinos:
    * **Enviar a Log Analytics**
        * Seleccione la **Suscripción** y el **Área de trabajo de Log Analytics**.
    * **Archivar en una cuenta de almacenamiento**
        * Seleccione la **Suscripción** y la **Cuenta de almacenamiento**.
    * **Transmisión a un centro de eventos**
        * Seleccione la **Suscripción**, el **Espacio de nombres del centro de eventos**, el **Nombre del centro de eventos (opcional)** y el **Nombre de la directiva del centro de eventos**.

9. Seleccione **Guardar**.

### <a name="powershell"></a>PowerShell

Inicie sesión en Azure PowerShell:

```azurepowershell
Connect-AzAccount 
```

#### <a name="log-analytics-workspace"></a>Área de trabajo de Log Analytics

Para habilitar los registros de diagnóstico en un área de trabajo de Log Analytics, escriba estos comandos. Reemplace los valores entre corchetes por sus valores:

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the workspace in a variable. ##
$wspara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-log-analytics-workspace-name>
}
$ws = Get-AzOperationalInsightsWorkspace @wspara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -Enabled $true `
    -MetricCategory 'AllMetrics' `
    -WorkspaceId $ws.ResourceId
```

#### <a name="storage-account"></a>Cuenta de almacenamiento

Para habilitar los registros de diagnóstico en una cuenta de almacenamiento, escriba estos comandos. Reemplace los valores entre corchetes por sus valores:

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the storage account in a variable. ##
$storpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-storage-account-name>
}
$storage = Get-AzStorageAccount @storpara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -StorageAccountId $storage.id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```

#### <a name="event-hub"></a>Centro de eventos

Para habilitar los registros de diagnóstico en un espacio de nombres del centro de eventos, escriba estos comandos. Reemplace los valores entre corchetes por sus valores:

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the event hub in a variable. ##
$hubpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-event-hub-name>
}
$eventhub = Get-AzEventHubNamespace @hubpara

## Place the event hub authorization rule in a variable. ##    
$hubrule = @{
    ResourceGroupName = 'myResourceGroup'
    Namespace = 'myeventhub8675'
}
$eventhubrule = Get-AzEventHubAuthorizationRule @hubrule

## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.Id `
    -Name 'myDiagSetting-event'`
    -EventHubName $eventhub.Name `
    -EventHubAuthorizationRuleId $eventhubrule.Id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```
### <a name="azure-cli"></a>Azure CLI

Inicie sesión en la CLI de Azure:

```azurecli
az login
```

#### <a name="log-analytics-workspace"></a>Área de trabajo de Log Analytics

Para habilitar los registros de diagnóstico en un área de trabajo de Log Analytics, escriba estos comandos. Reemplace los valores entre corchetes por sus valores:

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

wsid=$(az monitor log-analytics workspace show \
    --resource-group <your-resource-group> \
    --workspace-name <your-log-analytics-workspace-name> \
    --query id \
    --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --workspace $wsid
```

#### <a name="storage-account"></a>Cuenta de almacenamiento

Para habilitar los registros de diagnóstico en una cuenta de almacenamiento, escriba estos comandos. Reemplace los valores entre corchetes por sus valores:

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

storid=$(az storage account show \
        --name <your-storage-account-name> \
        --resource-group <your-resource-group> \
        --query id \
        --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --storage-account $storid
```

#### <a name="event-hub"></a>Centro de eventos

Para habilitar los registros de diagnóstico en un espacio de nombres del centro de eventos, escriba estos comandos. Reemplace los valores entre corchetes por sus valores:

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

az monitor diagnostic-settings create \
    --name myDiagSetting-event \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --event-hub-rule /subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<your-event-hub-namespace>/authorizationrules/RootManageSharedAccessKey
```

En las secciones siguientes se describen las métricas y los registros que se pueden recopilar.

## <a name="analyzing-metrics"></a>Análisis de métricas

Puede analizar las métricas de Load Balancer con las de otros servicios de Azure mediante el explorador de métricas si abre **Métricas** en el menú de **Azure Monitor**. Consulte [Introducción al explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) para más información sobre esta herramienta. 

Para una lista de las métricas de plataforma recopiladas para Load Balancer, consulte el artículo sobre [supervisión de las métricas de referencias de datos de Load Balancer](monitor-load-balancer-reference.md#metrics).  

Como referencia, puede ver una lista de [todas las métricas de recursos que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="analyzing-logs"></a>Análisis de datos

Los datos de los registros de Azure Monitor se almacenan en tablas, cada una con un conjunto propio de propiedades únicas.  

El [Registro de actividad](../azure-monitor/essentials/activity-log.md) es un tipo de registro de plataforma que proporciona información de eventos de nivel de suscripción. Puede verlo de forma independiente o enrutarlo a registros de Azure Monitor, donde puede realizar consultas mucho más complejas mediante Log Analytics.  

Para una lista de las tablas utilizadas por los registros de Azure Monitor y en la que Log Analytics puede realizar consultas, consulte el artículo sobre la [supervisión de referencias de datos de Load Balancer](monitor-load-balancer-reference.md#azure-monitor-logs-tables).  

### <a name="sample-kusto-queries"></a>Ejemplos de consultas de Kusto

> [!NOTE]
> Actualmente hay un problema con las consultas de Kusto que impide que se recuperen datos de los registros del equilibrador de carga.


## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [registros](../azure-monitor/alerts/alerts-unified-log.md) y el [registro de actividad](../azure-monitor/alerts/activity-log-alerts.md). Cada tipo de alerta tiene sus ventajas y desventajas.

Si crea o ejecuta una aplicación que se ejecuta en Load Balancer, [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) puede ofrecer otros tipos de alertas.


En la tabla siguiente se enumeran las reglas de alertas de métricas comunes y recomendadas para Load Balancer.

| Tipo de alerta | Condición | Descripción  |
|:---|:---|:---|
| Regla de equilibrio de carga no disponible debido a máquinas virtuales no disponibles | Si la disponibilidad de la ruta de acceso a los datos dividida por la dirección IP de front-end y el puerto de front-end (todos los valores conocidos y futuros) es igual a cero y el estado del sondeo de estado es igual a cero, se activa la alerta. | Esta alerta determina si la disponibilidad de la ruta de acceso a los datos para las reglas configuradas de equilibrio de carga no está atendiendo el tráfico debido a que el sondeo de estado configurado sondea todas las máquinas virtuales del grupo de back-end asociado. Revise la [guía para la solución de problemas](load-balancer-troubleshoot.md) del equilibrador de carga a fin de investigar la causa principal posible. |
| Disponibilidad de máquina virtual considerablemente baja | Si el estado del sondeo de estado dividido por la dirección IP de back-end y el puerto de back-end es igual al porcentaje sondeado definido por el usuario del tamaño total del grupo (es decir, se sondea un 25 %), se activa la alerta. | Esta alerta determina si hay menos de las VM necesarias disponibles para atender el tráfico. |
| Error en las conexiones de salida al punto de conexión de Internet | Si el número de conexiones SNAT filtrado por Estado de conexión = Error es mayor que cero, se activa la alerta. | Esta alerta se activa cuando se agotan los puertos SNAT y las máquinas virtuales no pueden iniciar conexiones de salida. |
| Aproximación al agotamiento de SNAT | Si los puertos SNAT usados son mayores que el número definido por el usuario, se activa la alerta. | Esta alerta requiere una configuración de salida estática donde siempre se asigne el mismo número de puertos. Luego se activa cuando se utiliza un porcentaje de los puertos asignados. |

## <a name="next-steps"></a>Pasos siguientes

- Consulte el artículo sobre [supervisión de la referencia de datos de Load Balancer](monitor-load-balancer-reference.md) para ver una referencia de las métricas, los registros y otros valores importantes creados por el equilibrador de carga.
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).