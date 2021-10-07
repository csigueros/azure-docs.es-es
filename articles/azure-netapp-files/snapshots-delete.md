---
title: Eliminación de instantáneas mediante Azure NetApp Files | Microsoft Docs
description: Describe cómo eliminar instantáneas mediante Azure NetApp Files.
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
ms.openlocfilehash: 19ae00a47dfee9fc86a3c5c42b81821934ad5999
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699519"
---
# <a name="delete-snapshots-using-azure-netapp-files"></a>Eliminación de instantáneas mediante Azure NetApp Files 

Puede eliminar las instantáneas que ya no necesita conservar. 

> [!IMPORTANT]
> La operación de eliminación de instantáneas no se puede deshacer. No se puede recuperar una instantánea eliminada. 

## <a name="steps"></a>Pasos

1. Vaya al menú **Instantáneas** de un volumen. Haga clic con el botón derecho en la instantánea que desea eliminar. Seleccione **Eliminar**.

    ![Captura de pantalla que describe el menú contextual de una instantánea](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. En la ventana Eliminar instantánea, haga clic en **Sí** para confirmar que quiere eliminar la instantánea. 

    ![Captura de pantalla que confirma la eliminación de la instantánea](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre las instantáneas](snapshots-introduction.md)
* [Información general sobre instantáneas de Azure NetApp Files](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)