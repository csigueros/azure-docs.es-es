---
title: Restauración de una instantánea en un nuevo volumen mediante Azure NetApp Files | Microsoft Docs
description: Describe cómo crear un nuevo volumen a partir de una instantánea mediante Azure NetApp Files.
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
ms.topic: how-to
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 4c3f65d61cac7f6bafd6d483383c3f85bb3e2516
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699512"
---
# <a name="restore-a-snapshot-to-a-new-volume-using-azure-netapp-files"></a>Restauración de una instantánea en un nuevo volumen mediante Azure NetApp Files

Las [instantáneas](snapshots-introduction.md) permiten la recuperación a un momento dado de los volúmenes. Actualmente, puede restaurar una instantánea solo a un nuevo volumen. 

## <a name="steps"></a>Pasos

1. Seleccione **Instantáneas** en la hoja Volumen para mostrar la lista de instantáneas. 
2. Haga clic con el botón derecho en la instantánea que desee restaurar y seleccione **Restaurar al nuevo volumen** en la opción de menú.  

    ![Captura de pantalla del menú Restaurar nuevo volumen.](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. En la ventana Crear un volumen, especifique la información del nuevo volumen:  
    * **Nombre**   
        Especifique el nombre para el volumen que va a crear.  
        
        El nombre debe ser único dentro de un grupo de recursos. Debe tener tres caracteres de longitud, como mínimo.  Puede usar cualquier carácter alfanumérico.

    * **Cuota**  
        Especifique la cantidad de almacenamiento lógico que desee asignar al volumen.  

    ![Captura de pantalla que muestra la ventana para crear un volumen.](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Haga clic en **Revisar y crear**.  Haga clic en **Crear**.   
    El nuevo volumen utiliza el mismo protocolo que la instantánea.   
    El nuevo volumen al que se restaura la instantánea aparece en la hoja Volúmenes.

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre las instantáneas](snapshots-introduction.md)
* [Supervisión de métricas de volumen e instantáneas](azure-netapp-files-metrics.md#volumes)
* [Solución de problemas de directivas de instantáneas](troubleshoot-snapshot-policies.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vídeo Instantáneas de Azure NetApp Files 101](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Información general sobre instantáneas de Azure NetApp Files](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)