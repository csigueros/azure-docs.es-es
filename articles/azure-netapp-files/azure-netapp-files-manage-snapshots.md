---
title: Creación de una instantánea a petición mediante Azure NetApp Files | Microsoft Docs
description: En este artículo se describe cómo crear y administrar instantáneas mediante Azure NetApp Files.
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
ms.date: 08/12/2021
ms.author: b-juche
ms.openlocfilehash: 2a17ed67830dfcfc3f1c5c3cbd6cc06ceedc8028
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614189"
---
# <a name="create-an-on-demand-snapshot-for-a-volume"></a>Creación de una instantánea a petición para un volumen

Azure NetApp Files admite la creación de [instantáneas](snapshots-introduction.md) a petición y el uso de directivas de instantáneas para programar la creación automática de instantáneas. También puede restaurar una [instantánea en un nuevo volumen](snapshots-restore-new-volume.md), [restaurar un único archivo mediante un cliente](snapshots-restore-file-client.md) o [revertir un volumen existente mediante una instantánea](snapshots-revert-volume.md). En este artículo se explica cómo crear una instantánea a petición para un volumen. 

> [!NOTE] 
> Para conocer las consideraciones sobre la administración de instantáneas en la replicación entre regiones, consulte [Requisitos y consideraciones del uso de la replicación entre regiones](cross-region-replication-requirements-considerations.md).
 
## <a name="steps"></a>Pasos

1.  Vaya al volumen para el que desee crear una instantánea. Haga clic en **Instantáneas**.

    ![Captura de pantalla que muestra cómo navegar a la hoja de instantáneas.](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Haga clic en **+ Add snapshot** (+Agregar instantánea) para crear una instantánea a petición para un volumen.

    ![Captura de pantalla que muestra cómo agregar una etiqueta.](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  En la ventana Nueva instantánea, proporcione un nombre para la nueva instantánea que va a crear.   

    ![Captura de pantalla que muestra la ventana Nuevo laboratorio.](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Haga clic en **OK**. 

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre las instantáneas](snapshots-introduction.md)
* [Administración de directivas de instantánea con Azure NetApp Files](snapshots-manage-policy.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vídeo Instantáneas de Azure NetApp Files 101](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [¿Qué es la herramienta Azure Application Consistent Snapshot?](azacsnap-introduction.md)
