---
title: Supervisión y métricas de Azure HPC Cache
description: Cómo ver estadísticas sobre su instancia de Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2021
ms.author: v-erkel
ms.openlocfilehash: 0963a412ac3939ad9421c2f7c1de7d72c24995ac
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113589793"
---
# <a name="cache-metrics-and-monitoring"></a>Supervisión y métricas de caché

Azure Portal tiene varias visualizaciones integradas que puede usar para supervisar el rendimiento de la instancia de Azure HPC Cache.

En este artículo se explica dónde encontrar las visualizaciones y lo que muestran.

En el portal, hay cuatro páginas de HPC Cache en las que se muestran los datos de rendimiento de la caché:

* **Información general**
* **Métricas**
* **Informe de caché**
* **Estado de cliente**

Abra las páginas **Métricas**, **Informe de caché** y **Estado de cliente** en el menú **Supervisión** de la barra lateral. La página **Información general** de la memoria caché es el elemento superior del menú lateral.

![captura de pantalla del menú Supervisión a la izquierda de la interfaz de Azure Portal para HPC Cache](media/monitoring-menu.png)

## <a name="overview-page"></a>Página de información general

La página **Información general** de la caché muestra gráficos para algunas estadísticas básicas de caché: rendimiento de caché, operaciones por segundo y latencia. Los gráficos se encuentran en la parte inferior de la página, debajo de la sección **Información esencial**.

![Captura de pantalla de tres gráficos de líneas que muestran las estadísticas mencionadas anteriormente para una memoria caché de ejemplo](media/hpc-cache-overview-stats.png)

Estos gráficos forman parte de las herramientas de supervisión y análisis integradas de Azure. Obtenga más información sobre estas funcionalidades en la [documentación de supervisión de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="metrics-page"></a>Página Métricas

La página **Métricas** de HPC Cache es otra característica estándar de Azure. Siga las sugerencias de la página para crear sus propios gráficos y obtener más información sobre las características.

## <a name="cache-report"></a>Informe de caché

El **Informe caché** es un conjunto personalizado de gráficos que muestran el espacio usado, el espacio libre y la tasa de reciclaje de la memoria caché.

De forma predeterminada, la página muestra datos agregados de todos los destinos de almacenamiento de la memoria caché, pero puede usar el control **Destino de almacenamiento** para mostrar los datos de consumo de caché para un destino de almacenamiento determinado en su lugar. También puede personalizar el intervalo de tiempo que se va a mostrar.

![captura de pantalla de la página Informe de caché](media/cache-report.png)

* El **Espacio usado en la memoria caché** es la cantidad de espacio del almacenamiento de caché que se usa en ese momento para almacenar los archivos que los equipos cliente han solicitado. Si selecciona un único destino de almacenamiento, solo se muestra el espacio usado para los archivos de ese sistema de almacenamiento de back-end.

* El **Espacio libre de caché** es la capacidad disponible restante en el almacenamiento en caché.

* La **Tasa de reciclaje de caché** representa la velocidad a la que se quitan los archivos antiguos de la memoria caché para dejar espacio para los archivos recién solicitados. Para un único destino de almacenamiento, este cálculo solo incluye los archivos almacenados en ese destino.

## <a name="client-status"></a>Estado de cliente

En la página **Estado de cliente** se enumeran las máquinas cliente que están conectadas a la instancia de Azure HPC Cache.

![captura de pantalla de la página Estado de cliente](media/client-status.png)

Puede personalizar la tabla con filtros para mostrar una dirección o un intervalo de direcciones de cliente específicas, un destino de almacenamiento concreto o una dirección de montaje de caché individual.

Los tipos de conexión que se muestran en la tabla están relacionados con las conexiones de cliente al destino de almacenamiento:

* `azure_https_backend` es una conexión de cliente segura a un sistema de Azure Blob Storage estándar.
* `mount_backend` es una conexión NFS a un sistema NFS de hardware o a un contenedor de blobs habilitado para NFS.
* `nfs_backend` es similar a la conexión `mount_backend`, pero representa una conexión independiente utilizada por una llamada a procedimiento remoto interno de NFS en lugar de una iniciada por la operación de montaje del cliente.

Estos valores pueden ser útiles al depurar problemas de conexión entre los clientes y HPC Cache.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre las [herramientas de métricas y estadísticas de Azure](../azure-monitor/index.yml)
* [Administración de la caché](hpc-cache-manage.md)
