---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 8a56b564d9126197b76d6e96cc56bb51eca980eb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520625"
---
* El tamaño más pequeño de la subred de AzureBastionSubnet que puede crear es /26. Se recomienda crear un tamaño /26 o mayor para adaptarse al escalado de host. 
   * Para más información sobre el escalado, consulte [Valores de configuración: Escalado de host](../articles/bastion/configuration-settings.md#instance).
   * Para más información sobre el escalado, consulte [Valores de configuración: AzureBastionSubnet](../articles/bastion/configuration-settings.md#instance).
* Cree **AzureBastionSubnet** sin ninguna delegación ni tabla de rutas. 
* Cuando use grupos de seguridad de red en **AzureBastionSubnet**, consulte el artículo [Trabajo con grupos de seguridad de red](../articles/bastion/bastion-nsg.md).
