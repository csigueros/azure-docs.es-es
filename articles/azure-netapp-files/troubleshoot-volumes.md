---
title: Solución de errores de volúmenes de Azure NetApp Files | Microsoft Docs
description: Aquí se describen los mensajes de error y las resoluciones que pueden ayudar a solucionar los problemas de los volúmenes de Azure NetApp Files.
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
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: a9df7577e1426fb355995cd4486338003a753c86
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129539015"
---
# <a name="troubleshoot-volume-errors-for-azure-netapp-files"></a>Solución de errores de volúmenes de Azure NetApp Files

En este artículo se describen los mensajes de error y las resoluciones que pueden ayudar a solucionar los problemas de los volúmenes de Azure NetApp Files. 

## <a name="errors-for-volume-allocation"></a>Errores de asignación de volúmenes 

Al crear un nuevo volumen o cambiar el tamaño de uno existente en Azure NetApp Files, Microsoft Azure asigna recursos de red y almacenamiento a la suscripción. A veces se pueden experimentar errores de asignación de recursos debido al crecimiento sin precedentes de la demanda de servicios de Azure en determinadas regiones.

En esta sección se explican las causas de algunos de los errores de asignación más comunes y se sugieren posibles soluciones.

|     Condiciones del error    |     Soluciones    |
|-|-|
|Error al crear nuevos volúmenes o al cambiar el tamaño de volúmenes existentes. <br> Mensaje de error: `There was a problem locating [or extending] storage  for the volume. Please retry the operation. If the problem persists, contact Support.` | El error indica que el servicio se ha encontrado con un error al intentar asignar recursos para esta solicitud. <br> Vuelva a intentar la operación más tarde. Póngase en contacto con Soporte técnico si el problema persiste.|
|Sin capacidad de almacenamiento o red en una región para volúmenes normales. <br> Mensaje de error: `There are currently insufficient resources available to create [or extend] a volume in this region. Please retry the operation. If the problem persists, contact Support.` | El error indica que no hay recursos suficientes disponibles en la región para crear volúmenes o cambiar su tamaño. <br> Pruebe alguna de las siguientes soluciones alternativas: <ul><li>Cree el volumen en una nueva red virtual. Con esto evita alcanzar los límites de recursos relacionados con las redes.</li> <li>Vuelva a intentarlo más tarde. Entre tanto se pueden haber liberado recursos en el clúster, la región o la zona.</li></ul> |
|Sin capacidad de almacenamiento al crear un volumen con características de red establecidas en `Standard`. <br> Mensaje de error: `No storage available with Standard network features, for the provided VNet.` | El error indica que no hay recursos suficientes disponibles en la región para crear volúmenes con características de red `Standard`. <br> Pruebe alguna de las siguientes soluciones alternativas: <ul><li>Si no se requieren características de red `Standard`, cree el volumen con características de red `Basic`.</li> <li>Intente crear el volumen en una nueva red virtual. Con esto evita alcanzar los límites de recursos relacionados con las redes.</li><li>Vuelva a intentarlo más tarde.  Entre tanto se pueden haber liberado recursos en el clúster, la región o la zona.</li></ul> |

## <a name="next-steps"></a>Pasos siguientes  

* [Creación de un volumen de NFS para Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Cree un volumen SMB para Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Creación de un volumen de dos protocolos para Azure NetApp Files](create-volumes-dual-protocol.md) 
* [Configuración de las características de red de un volumen](configure-network-features.md)
