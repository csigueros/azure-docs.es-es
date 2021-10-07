---
title: Reversión de un volumen mediante la reversión de instantáneas con Azure NetApp Files | Microsoft Docs
description: Se describe cómo revertir un volumen a un estado anterior mediante Azure NetApp Files.
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
ms.openlocfilehash: 7bf50ee981052424cf0a57f366b3e24b483eb5ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699708"
---
# <a name="revert-a-volume-using-snapshot-revert-with-azure-netapp-files"></a>Reversión de un volumen mediante la reversión de instantáneas con Azure NetApp Files

La funcionalidad de reversión de [instantáneas](snapshots-introduction.md) le permite revertir rápidamente un volumen al estado en que se encontraba cuando se tomó una instantánea determinada. En la mayoría de los casos, la reversión de un volumen es mucho más rápida que la restauración de archivos individuales a partir de una instantánea en el sistema de archivos activo. También requiere menos espacio que la restauración de una instantánea en un nuevo volumen. 

Puede encontrar la opción Revertir volumen en el menú Instantáneas de un volumen. Después de seleccionar una instantánea para la reversión, Azure NetApp Files revierte el volumen a los datos y las marcas de tiempo que contenía cuando se tomó la instantánea seleccionada. 

> [!IMPORTANT]
> Los datos del sistema de archivos activo y las instantáneas que se tomaron después de la instantánea seleccionada se perderán. La operación de reversión de instantáneas reemplazará *todos* los datos del volumen de destino por los datos de la instantánea seleccionada. Debe prestar atención al contenido y a la fecha de creación de la instantánea al seleccionar una instantánea. No se puede deshacer la operación de reversión de instantáneas.

## <a name="steps"></a>Pasos

1. Vaya al menú **Instantáneas** de un volumen.  Haga clic con el botón derecho en la instantánea que quiere utilizar para la operación de reversión. Seleccione **Revertir volumen**. 

    ![Captura de pantalla que describe el menú contextual de una instantánea.](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. En la ventana Revertir el volumen a la instantánea, escriba el nombre del volumen y haga clic en **Revertir**.   

    El volumen ahora se restaura al momento en que se tomó la instantánea seleccionada.

![Captura de pantalla que muestra la ventana "Revertir el volumen a la instantánea".](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre las instantáneas](snapshots-introduction.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vídeo Instantáneas de Azure NetApp Files 101](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Información general sobre instantáneas de Azure NetApp Files](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)