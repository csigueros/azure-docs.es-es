---
title: Supervisión de un dispositivo Azure Stack Edge | Microsoft Docs
description: Describe cómo usar Azure Portal y la interfaz de usuario web local para supervisar Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: 13a11bb23479653ccc88b6e1a2312ff8e6f33609
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110099666"
---
# <a name="monitor-your-azure-stack-edge"></a>Supervisión de Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

En este artículo se describe cómo supervisar Azure Stack Edge. Para supervisar el dispositivo, puede usar Azure Portal o la interfaz de usuario web local. Use Azure Portal para ver los eventos de dispositivo, configurar y administrar las alertas, y ver las métricas. Use la interfaz de usuario web local en el dispositivo físico para ver el estado del hardware de los distintos componentes del dispositivo.

En este artículo aprenderá a:

> [!div class="checklist"]
>
> * Ver los eventos de dispositivo y las alertas correspondientes
> * Ver el estado del hardware de los componentes del dispositivo
> * Ver las métricas de transacción y de capacidad del dispositivo

## <a name="view-device-events"></a>Ver los eventos de dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Visualización del estado del hardware

Realice los pasos siguientes en la interfaz de usuario web local para ver el estado del hardware de los componentes del dispositivo.

1. Conéctese a la interfaz de usuario web local del dispositivo.
2. Vaya a **Mantenimiento > Estado de hardware**. Puede ver el estado de los distintos componentes del dispositivo.

    ![Visualización del estado del hardware](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Visualización de métricas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Métricas sobre el dispositivo

En esta sección se describen las métricas de supervisión sobre el dispositivo. Las métricas pueden ser:

* Métricas de capacidad. Las métricas de capacidad están relacionadas con la capacidad del dispositivo.

* Métricas de transacción. Las métricas de transacción están relacionadas con las operaciones de lectura y de escritura en Azure Storage.

* Métricas de proceso perimetral. Las métricas de proceso perimetral se relacionan con el uso del proceso perimetral en el dispositivo.

En la tabla siguiente se muestra una lista completa de las métricas:

|Métricas de capacidad                     |Descripción  |
|-------------------------------------|-------------|
|**Capacidad disponible**               | Se refiere al tamaño de los datos que se pueden escribir en el dispositivo. En otras palabras, esta métrica es la capacidad que puede estar disponible en el dispositivo. <br></br>Para liberar capacidad del dispositivo, elimine la copia local de los archivos que tengan una copia tanto en el dispositivo como en la nube.        |
|**Capacidad total**                   | Hace referencia al total de bytes del dispositivo en los que se puede escribir datos. También se conoce como el tamaño total de la caché local. <br></br> Ahora puede aumentar la capacidad de un dispositivo virtual existente mediante la adición de un disco de datos. Agregue un disco de datos a través de la administración del hipervisor para la VM y, después, reinicie la VM. El bloque de almacenamiento local del dispositivo de puerta de enlace se expandirá para adaptarse al disco de datos recién agregado. <br></br>Para obtener más información, vaya a [Add a hard drive for Hyper-V virtual machine](https://www.youtube.com/watch?v=EWdqUw9tTe4) (Adición de un disco duro para la máquina virtual de Hyper-V). |

|Métricas de transacciones              | Descripción         |
|-------------------------------------|---------|
|**Bytes cargados en la nube (dispositivo)**    | Suma de todos los bytes cargados en todos los recursos compartidos en el dispositivo        |
|**Bytes cargados en la nube (recurso compartido)**     | Bytes cargados por recurso compartido. Esta métrica puede ser: <br></br> Valor medio: suma de todos los bytes cargados por uno o varios recursos compartidos  <br></br>Valor máx.: número máximo de bytes cargados desde un recurso compartido <br></br>Valor mín.: número mínimo de bytes cargados desde un recurso compartido      |
|**Rendimiento de descarga en la nube (recurso compartido)**| Bytes descargados por recurso compartido. Esta métrica puede ser: <br></br> Valor medio: suma de todos los bytes leídos o descargados en uno o varios recursos compartidos <br></br> Valor máx.: número máximo de bytes descargados desde un recurso compartido<br></br> Valor mín.: número mínimo de bytes descargados desde un recurso compartido  |
|**Rendimiento de lectura en la nube**            | Suma de todos los bytes leídos desde la nube en todos los recursos compartidos en el dispositivo     |
|**Rendimiento de carga en la nube**          | Suma de todos los bytes escritos en la nube en todos los recursos compartidos en el dispositivo     |
|**Rendimiento de carga en la nube (recurso compartido)**  | Suma de todos los bytes escritos en la nube desde uno o varios recursos compartidos. Se trata de un valor medio, máx. y mín. por recurso compartido      |
|**Rendimiento de lectura (red)**           | Incluye el rendimiento de la red del sistema para todos los bytes leídos desde la nube. Esta vista puede incluir datos no restringidos a los recursos compartidos. <br></br>La división mostrará el tráfico que pasa por todos los adaptadores de red del dispositivo, incluidos los que no están conectados o habilitados.      |
|**Rendimiento de escritura (red)**       | Incluye el rendimiento de la red del sistema para todos los bytes escritos en la nube. Esta vista puede incluir datos no restringidos a los recursos compartidos. <br></br>La división mostrará el tráfico que pasa por todos los adaptadores de red del dispositivo, incluidos los que no están conectados o habilitados.          |

| Métricas de proceso perimetral              | Descripción         |
|-------------------------------------|---------|
|**Proceso perimetral: uso de memoria**      |           |
|**Proceso perimetral: porcentaje de CPU**    |         |

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [administrar el ancho de banda](azure-stack-edge-manage-bandwidth-schedules.md).
Aprenda a [administrar las notificaciones de alertas de eventos de dispositivos](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
