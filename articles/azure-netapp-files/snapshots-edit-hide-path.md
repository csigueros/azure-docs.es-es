---
title: Edición de la opción Ocultar la ruta de acceso de la instantánea de Azure NetApp Files | Microsoft Docs
description: Describe cómo controlar la visibilidad de un volumen de instantáneas con Azure NetApp Files.
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
ms.openlocfilehash: 5b165bd2e4ca3ee6ac2d16bac7c4d5ff1117fdc9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699518"
---
# <a name="edit-the-hide-snapshot-path-option-of-azure-netapp-files"></a>Edición de la opción Ocultar la ruta de acceso de la instantánea de Azure NetApp Files
La opción Ocultar la ruta de acceso de la instantánea controla si está visible la ruta de acceso de la instantánea de un volumen. Durante la creación de un volumen de [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) o [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume), puede especificar si se debe ocultar la ruta de acceso de la instantánea. Posteriormente, puede editar esta opción según proceda.  

> [!NOTE]
> En el caso de un [volumen de destino](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) en la replicación entre regiones, la opción Ocultar la ruta de acceso de la instantánea está habilitada de forma predeterminada y no se puede modificar. 

## <a name="steps"></a>Pasos

1. Para ver la opción Ocultar la ruta de acceso de la instantánea de un volumen, seleccione el volumen. En el campo **Ocultar la ruta de acceso de la instantánea** se muestra si la opción está habilitada.   
    ![Captura de pantalla que describe el campo Ocultar la ruta de acceso de la instantánea.](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. Para editar la opción Ocultar la ruta de acceso de la instantánea, haga clic en **Editar** en la página del volumen y modifique la opción **Ocultar la ruta de acceso de la instantánea** como proceda.   
    ![Captura de pantalla que describe la opción de edición de la instantánea de volumen.](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre las instantáneas](snapshots-introduction.md)