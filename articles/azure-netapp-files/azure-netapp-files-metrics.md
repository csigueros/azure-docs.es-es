---
title: Métricas de Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files proporciona métricas sobre el almacenamiento asignado, el uso de almacenamiento real, el número de IOPS del volumen y la latencia. Use estas métricas para comprender el uso y el rendimiento.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: cc034689e2c3cd6846986680225ca7ca21ac41c8
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407539"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas de Azure NetApp Files

Azure NetApp Files proporciona métricas sobre el almacenamiento asignado, el uso de almacenamiento real, el número de IOPS del volumen y la latencia. Mediante el análisis de estas métricas, puede comprender mejor el patrón de uso y el rendimiento de volumen de las cuentas de NetApp.  

Puede consultar las métricas para un grupo de capacidad o un volumen si selecciona el **grupo de capacidad** o el **volumen**, respectivamente.  A continuación, haga clic en **Métrica** para ver las métricas disponibles: 

[![Instantánea que muestra cómo navegar a la lista desplegable Métrica.](../media/azure-netapp-files/metrics-navigate-volume.png) ](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Métricas de uso de grupos de capacidad

- *Tamaño asignado del grupo*   
    El tamaño aprovisionado del grupo.

- *Grupo asignado al tamaño del volumen*  
    Cuota de volumen (GiB) total en un grupo de capacidad determinado (es decir, el total de los tamaños aprovisionados de los volúmenes en el grupo de capacidad).  
    Es el tamaño que se selecciona durante la creación del volumen.  

- *Tamaño consumido del grupo*  
    Espacio lógico (GiB) total usado en todos los volúmenes de un grupo de capacidad.  

- *Tamaño de instantánea total del grupo*    
    Suma del tamaño de la instantánea de todos los volúmenes del grupo.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Métricas de uso de volúmenes

- *Porcentaje de tamaño consumido del volumen*    
    Porcentaje del volumen consumido, incluidas las instantáneas.  
- *Tamaño asignado del volumen*   
    Tamaño aprovisionado de un volumen
- *Tamaño de cuota del volumen*    
    Tamaño de la cuota (GiB) con el que se aprovisiona el volumen.   
- *Tamaño consumido del volumen*   
    Tamaño lógico del volumen (bytes usados).  
    Este tamaño incluye el espacio lógico usado por las instantáneas y los sistemas de archivos activos.  
- *Tamaño de instantánea de volumen*   
   Tamaño de todas las instantáneas de un volumen.  

## <a name="performance-metrics-for-volumes"></a>Métricas de rendimiento de los volúmenes

> [!NOTE] 
> La latencia de volumen para *Latencia media de lectura* y *Latencia media de escritura* se mide dentro del servicio de almacenamiento y no incluye la latencia de red.

- *Latencia de lectura media*   
    Tiempo promedio de las lecturas del volumen en milisegundos.
- *Latencia de escritura media*   
    Tiempo promedio de las escrituras del volumen en milisegundos.
- *IOPS de lectura*   
    Número de lecturas en el volumen por segundo.
- *IOPS de escritura*   
    Número de escrituras en el volumen por segundo.

## <a name="volume-replication-metrics"></a><a name="replication"></a>Métricas de replicación de volúmenes

> [!NOTE] 
> * El tamaño de transferencia de red (por ejemplo, las métricas de la *transferencia total de replicación de volúmenes*) pueden diferir de los volúmenes de origen o de destino de una replicación entre regiones. Este comportamiento es el resultado del uso de un motor de replicación eficaz para minimizar el costo de la transferencia de red.
> * Actualmente, las métricas de replicación de volumen se rellenan para los volúmenes de destino de replicación y no para el origen de la relación de replicación.

- *¿Es correcto el estado de replicación del volumen?*    
    La condición de la relación de replicación. `1` indica un estado correcto. `0` indica un estado incorrecto.

- *¿Se está transfiriendo la replicación del volumen?*     
    Indica si el estado de la replicación del volumen es "transfiriéndose". 

- *Duración de la última transferencia de replicación de volumen*   
    Cantidad de tiempo en segundos que tardó en completarse la última transferencia. 

- *Tamaño de última transferencia de replicación de volumen*    
    Número total de bytes transferidos como parte de la última transferencia. 

- *Progreso de la replicación del volumen*    
    La cantidad total de datos transferidos para la operación de transferencia actual. 

- *Transferencia total de la replicación del volumen*   
    Los bytes acumulados transferidos para la relación. 

## <a name="throughput-metrics-for-capacity-pools"></a>Métricas de rendimiento de grupos de capacidad   

* *Rendimiento asignado al grupo*    
    Suma del rendimiento de todos los volúmenes que pertenecen al grupo.
    
* *Rendimiento aprovisionado para el grupo*   
    Rendimiento aprovisionado de este grupo.


## <a name="throughput-metrics-for-volumes"></a>Métricas de rendimiento de los volúmenes   

* *Rendimiento de lectura*   
    Rendimiento de lectura en bytes por segundo.
    
* *Rendimiento total*   
    Suma de todo el rendimiento en bytes por segundo.

* *Rendimiento de escritura*    
    Rendimiento de escritura en bytes por segundo.

* *Otro rendimiento*   
    Otro rendimiento (que no sea de lectura ni escritura) expresado en bytes por segundo.

## <a name="volume-backup-metrics"></a>Métricas de copia de seguridad de los volúmenes  

* *Is Volume Backup Enabled*  (¿Está habilitada la copia de seguridad del volumen?)  
    Muestra si está habilitada la copia de seguridad del volumen. `1` se ha habilitado. `0` indica que se deshabilitan.

* *Is Volume Backup Operation Complete*  (¿Se ha completado la operación de copia de seguridad del volumen?)  
    Muestra si la última operación de copia de seguridad o restauración del volumen se completó correctamente.  `1` es correcta. `0` no es correcta.

* *Is Volume Backup Suspended*  (¿Se ha suspendido la copia de seguridad del volumen?)  
    Muestra si la directiva de copia de seguridad está suspendida para el volumen.  `1` no está suspendida. `0` está suspendida.

* *Volume Backup Bytes*  (Bytes de la copia de seguridad del volumen)  
    Total de bytes en la copia de seguridad de este volumen.

* *Volume Backup Last Transferred Bytes*  (Bytes transferidos en la última copia de seguridad del volumen)  
    El total de bytes transferidos en la última operación de copia de seguridad o restauración.  

## <a name="next-steps"></a>Pasos siguientes

* [Información sobre la jerarquía del almacenamiento de Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Creación de un grupo de capacidad](azure-netapp-files-set-up-capacity-pool.md)
* [Creación de un volumen de Azure NetApp Files](azure-netapp-files-create-volumes.md)
