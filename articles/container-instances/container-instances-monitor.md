---
title: Supervisión de instancias de contenedor
description: Aprenda a supervisar el consumo de los recursos de computación, como la CPU y la memoria, en los contenedores de Azure Container Instances.
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 7f485efc5bdc29760f0b4278b746940c947777e3
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219235"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Supervisión de los recursos de los contenedores en Azure Container Instances

[Azure Monitor][azure-monitoring] proporciona información acerca de los recursos de proceso que utilizan las instancias de contenedor. Esta información sobre el uso de los recursos le ayuda a determinar cuál es la mejor configuración de recursos para los grupos de contenedores. Azure Monitor también proporciona métricas que realizan un seguimiento de la actividad en la red en sus instancias de contenedor.

En este documento, se explica cómo se recopilan las métricas de Azure Monitor para instancias de contenedor mediante Azure Portal y la CLI de Azure.

> [!IMPORTANT]
> Las métricas de Azure Monitor de Azure Container Instances están actualmente en versión preliminar y [se aplican algunas limitaciones](#preview-limitations). Las versiones preliminares están a su disposición con la condición de que acepte los [términos de uso adicionales][terms-of-use]. Es posible que algunos de los aspectos de esta característica cambien antes de ofrecer disponibilidad general.

## <a name="preview-limitations"></a>Limitaciones de vista previa

En este momento, las métricas de Azure Monitor solamente están disponibles para los contenedores de Linux.

## <a name="available-metrics"></a>Métricas disponibles

Azure Monitor proporciona las siguientes [métricas para Azure Container Instances][supported-metrics]. Estas métricas están disponibles para grupos de contenedores y contenedores individuales. De forma predeterminada, las métricas se agregan como promedios.

- **Uso de CPU**: medido en **milinúcleos**. 
  - 1 millar de núcleos corresponde a 1/1000 de un núcleo de CPU, por lo que 500 millares de núcleos representan un uso de 0,5 de un núcleo de CPU.
- **Uso de memoria** en bytes.
- **Bytes de red recibidos** por segundo
- **Bytes de red transmitidos** por segundo 

## <a name="get-metrics---azure-portal"></a>Obtención de métricas mediante Azure Portal

Cuando se crea un grupo de contenedores, los datos de Azure Monitor están disponibles en Azure Portal. Para ver las métricas de un grupo de contenedores, vaya a la página **Información general** del grupo de contenedores. Aquí puede ver los gráficos creados previamente para cada una de las métricas disponibles.

![gráfico dual][dual-chart]

En un grupo de contenedores que contenga varios contenedores, utilice una [dimensión][monitor-dimension] para mostrar las métricas por contenedor. Para crear un gráfico utilizando las métricas de cada contenedor, siga estos pasos:

1. En la página **Información general** página, seleccione uno de los gráficos de métricas, como **CPU**. 
1. Seleccione el botón **Apply splitting** (Aplicar división) y, después, seleccione **Nombre del contenedor**.

![Captura de pantalla en la que se muestran las métricas de una instancia de contenedor con las opciones Aplicar división y Nombre de contenedor seleccionadas.][dimension]

## <a name="get-metrics---azure-cli"></a>Obtención de métricas mediante la CLI de Azure

Las métricas de las instancias de contenedor también pueden obtenerse mediante la CLI de Azure. En primer lugar, debe utilizar el siguiente comando para obtener el identificador del grupo de contenedores. Sustituya `<resource-group>` por el nombre del grupo de recursos y `<container-group>` por el nombre del grupo de contenedores.


```azurecli
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Utilice el comando siguiente para obtener las métricas de uso de la **CPU**.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
Timestamp            Name       Average
-------------------  ---------  ---------
2020-12-17 23:34:00  CPU Usage
. . .
2020-12-18 00:25:00  CPU Usage
2020-12-18 00:26:00  CPU Usage  0.4
2020-12-18 00:27:00  CPU Usage  0.0
```

Cambie el valor del parámetro `--metric` en el comando para obtener otras [métricas admitidas][supported-metrics]. Por ejemplo, utilice el comando siguiente para obtener las métricas de uso de la **memoria**. 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

En un grupo de varios contenedores, la dimensión `containerName` se puede agregar para devolver métricas de cada contenedor.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la supervisión en Azure, consulte esta [introducción a la supervisión de Azure][azure-monitoring].

Aprenda a crear [alertas de métricas][metric-alert] para recibir notificaciones cuando una métrica de Azure Container Instances traspase un umbral.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/alerts/alerts-metric.md
[monitor-dimension]: ../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups
