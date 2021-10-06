---
title: Solución de problemas de grupos de capacidad para Azure NetApp Files | Microsoft Docs
description: Describe las posibles incidencias que podría tener al administrar grupos de capacidad y proporciona soluciones.
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
ms.topic: troubleshooting
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 7ca7f042aaa97967d47dd9f35afaa647e0d37361
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407273"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Solución de problemas de grupos de capacidad

En este artículo se describen las resoluciones a las incidencias que puede tener al administrar grupos de capacidad, incluida la operación de cambio de grupo. 

## <a name="issues-managing-a-capacity-pool"></a>Incidencias al administrar un grupo de capacidad 

|     Condición de error    |     Resolución    |
|-|-|
| Incidencias al crear un grupo de capacidad. |  Asegúrese de que el número de grupos de capacidad no supera el límite. Vea [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md).  Si el número es menor que el límite y todavía experimenta incidencias, escriba una incidencia de soporte técnico y especifique el nombre del grupo de capacidad. |
| Incidencias al eliminar un grupo de capacidad.  |  Asegúrese de quitar todos los volúmenes e instantáneas de Azure NetApp Files de la suscripción en la que está intentando eliminar el grupo de capacidad. <br> Si ya ha quitado todos los volúmenes e instantáneas y todavía no puede eliminar el grupo de capacidad, es posible que las referencias a los recursos todavía existan sin que se muestren en el portal. En este caso, escriba una incidencia de soporte técnico y especifique que ha realizado los pasos recomendados anteriores. |
| La creación o modificación del volumen ha fallado con el error `Requested throughput not available`. | El rendimiento disponible para un volumen lo determina el tamaño del grupo de capacidad y el nivel de servicio. Si no tiene suficiente rendimiento, debería aumentar el tamaño del grupo o ajustar el rendimiento del volumen existente. | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>Problemas al cambiar el grupo de capacidad de un volumen 

|     Condición de error    |     Resolución    |
|-|-|
| No se permite cambiar el grupo de capacidad para un volumen. | Es posible que no esté autorizado todavía para usar esta característica. <br> La característica para trasladar un volumen a otro grupo de capacidad está actualmente en versión preliminar. Si usa esta característica por primera vez, debe registrarla primero y establecer `-FeatureName ANFTierChange`. Vea los pasos de registro en [Cambio dinámico del nivel de servicio de un volumen](dynamic-change-volume-service-level.md). |
| El tamaño del grupo de capacidad es demasiado pequeño para el tamaño total del volumen. |  El error se debe a que el grupo de capacidad de destino no tiene la capacidad disponible para el volumen que se está moviendo.  <br> Aumente el tamaño del grupo de destino o elija otro grupo más grande.  Vea [Cambiar el tamaño de un grupo de capacidad o de un volumen](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
|  No se puede completar el cambio de grupo porque ya existe un volumen llamado `'{source pool name}'` en el grupo de destino `'{target pool name}'`. | Este error se produce porque el volumen con el mismo nombre ya existe en el grupo de capacidad de destino.  Seleccione otro grupo de capacidad que no tenga un volumen con el mismo nombre.   | 

## <a name="next-steps"></a>Pasos siguientes  

* [Creación de un grupo de capacidad](azure-netapp-files-set-up-capacity-pool.md)
* [Administración de un grupo de capacidad de QoS manual](manage-manual-qos-capacity-pool.md)
* [Cambio dinámico del nivel de servicio de un volumen](dynamic-change-volume-service-level.md)
* [Cambiar el tamaño de un grupo de capacidad o de un volumen](azure-netapp-files-resize-capacity-pools-or-volumes.md)
