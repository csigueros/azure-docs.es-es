---
title: Supervisión de redes virtuales de Azure
description: Comience aquí para aprender a supervisar las redes virtuales de Azure
services: virtual-network
author: duongau
ms.author: duau
ms.service: virtual-network
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: bec4d18ebc762ecf573281341e907db4376f26b2
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297643"
---
# <a name="monitoring-azure-virtual-network"></a>Supervisión de Azure Virtual Network

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. 

En este artículo se describen los datos de supervisión generados por Azure Virtual Network. Azure Virtual Network usa [Azure Monitor](../azure-monitor/overview.md). Si no está familiarizado con las características de Azure Monitor comunes a todos los servicios de Azure que lo usan, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="monitoring-data"></a>Supervisión de datos 

Azure Virtual Network recopila los mismos tipos de datos de supervisión que otros recursos de Azure, que se describen en [Supervisión de datos de recursos de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte el artículo sobre la [supervisión de la referencia de datos de Azure Virtual Network](monitor-virtual-network-reference.md) para obtener información detallada sobre las métricas y las métricas de registros creadas por Azure Virtual Network.

## <a name="collection-and-routing"></a>Recopilación y enrutamiento

Las métricas de la plataforma y el registro de actividad se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.  

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o varias ubicaciones.

Consulte [Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure](../azure-monitor/essentials/diagnostic-settings.md) para ver el proceso detallado para crear una configuración de diagnóstico mediante el Azure Portal, la CLI o PowerShell. Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar. Las categorías de *Azure Virtual Network* se muestran en la [referencia de datos de supervisión de Azure Virtual Network](monitor-virtual-network-reference.md#resource-logs).

> [!IMPORTANT]
> La habilitación de esta configuración requiere servicios adicionales de Azure (cuenta de almacenamiento, centro de eventos o Log Analytics), lo que puede aumentar el costo. Para calcular un costo estimado, visite la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator).

En las secciones siguientes se describen las métricas y los registros que se pueden recopilar.

## <a name="analyzing-metrics"></a>Análisis de métricas

Azure Monitor actualmente no admite el análisis de métricas de *Azure Virtual Network* desde el explorador de métricas. Para ver las métricas de *Azure Virtual Network*, seleccione **Métricas** en **Supervisión** de la red virtual que desea analizar.

:::image type="content" source="./media/monitor-virtual-network/metrics.png" alt-text="Captura de pantalla de los paneles de métricas para Virtual Network." lightbox="./media/monitor-virtual-network/metrics-expanded.png":::

Para una lista de las métricas de plataforma recopiladas para Azure Virtual Network, consulte el artículo sobre la [supervisión de métricas de referencias de datos de Azure Virtual Network](monitor-virtual-network-reference.md#metrics).

Como referencia, puede ver una lista de [todas las métricas de recursos que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="analyzing-logs"></a>Análisis de datos

Azure Virtual Network no admite registros de recursos.

Para una lista de los tipos de registros de recursos recopilados para los recursos de una red virtual, consulte el artículo sobre la [supervisión de referencia de datos de Virtual Network](monitor-virtual-network-reference.md#resource-logs).   

El [registro de actividad](../azure-monitor/essentials/activity-log.md) es un tipo de registro de plataforma de Azure que proporciona conclusiones sobre los eventos del nivel de suscripción. Puede verlo de forma independiente o enrutarlo a registros de Azure Monitor, donde puede realizar consultas mucho más complejas mediante Log Analytics.  

## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [registros](../azure-monitor/alerts/alerts-unified-log.md) y el [registro de actividad](../azure-monitor/alerts/activity-log-alerts.md). Cada tipo de alerta tiene sus ventajas y desventajas.

En la tabla siguiente se enumeran las reglas de alertas de actividades comunes y recomendadas para Azure Virtual Network.

| Tipo de alerta | Condición | Descripción  |
|:---|:---|:---|
| Crear o actualizar una red virtual | Nivel de evento: Todos los seleccionados, Estado: Todos los seleccionados, Evento iniciado por: Todos los servicios y usuarios | Cuando un usuario crea la red virtual o modifica su configuración. |
| Eliminar red virtual | Nivel de evento: Todos los seleccionados, Estado: Iniciado | Cuando un usuario elimina una red virtual. |

## <a name="next-steps"></a>Pasos siguientes

* Consulte el artículo sobre la [supervisión de referencia de datos de Virtual Network](monitor-virtual-network-reference.md) para una referencia de las métricas, los registros y otros valores importantes creados por Azure Virtual Network.
* Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/overview.md).