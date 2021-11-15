---
title: Cuota de capacidad regional para Azure NetApp Files | Microsoft Docs
description: Se explica la cuota de capacidad regional de Azure NetApp Files.
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
ms.date: 10/11/2021
ms.author: b-juche
ms.openlocfilehash: 1ae89ff8b8593d96740b0140a1570ac19deacea3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270181"
---
# <a name="regional-capacity-quota-for-azure-netapp-files"></a>Cuota de capacidad regional para Azure NetApp Files

En este artículo se explica la cuota de capacidad regional de Azure NetApp Files.

## <a name="display-regional-capacity-quota"></a>Visualización de la cuota de capacidad regional

Puede hacer clic en **Cuota** en Configuración de Azure NetApp Files para mostrar los tamaños de cuota actuales y predeterminados de la región. 

Por ejemplo: 

![Captura de pantalla que muestra cómo mostrar la información de cuota.](../media/azure-netapp-files/quota-display.png) 

## <a name="request-regional-capacity-quota-increase"></a>Solicitud de aumento de la cuota de capacidad regional

Puede [enviar una solicitud de soporte técnico](azure-netapp-files-resource-limits.md#request-limit-increase) para aumentar una cuota de capacidad regional sin incurrir en costos adicionales. La solicitud de soporte técnico se enviará al equipo de administración de capacidad de Azure para su procesamiento. Normalmente, recibirá una respuesta en un plazo de dos días laborables. El equipo de administración de la capacidad de Azure podría ponerse en contacto con usted si la solicitud es de tamaño considerable.  

Un aumento de la cuota de capacidad regional no incurre en un aumento de facturación. La facturación se seguirá basando en los grupos de capacidad aprovisionados.
Por ejemplo, si actualmente tiene 25 TiB de capacidad aprovisionada, puede solicitar un aumento de la cuota a 35 TiB.  En un plazo de dos días laborables, el aumento de la cuota se aplicará a la región solicitada. Cuando se aplique el aumento de cuota, solo se pagará por la capacidad aprovisionada actual (25 TiB). Pero cuando aprovisione realmente los 10 TiB adicionales, se le facturarán 35 TiB.

Los [límites de los recursos](azure-netapp-files-resource-limits.md#resource-limits) actuales de Azure NetApp Files no cambian. Todavía podrá aprovisionar un grupo de capacidad de 500 TiB. Pero antes de hacerlo, la cuota de capacidad regional debe aumentarse a 500 TiB.

## <a name="next-steps"></a>Pasos siguientes  

- [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Información sobre la jerarquía del almacenamiento de Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de costo de Azure NetApp Files](azure-netapp-files-cost-model.md)
