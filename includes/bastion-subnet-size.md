---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 62df24c61cf50f57e5f59ebca8eb2d11b2d03b12
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640642"
---
* El tamaño más pequeño de la subred de AzureBastionSubnet que puede crear es /27. Sin embargo, se recomienda crear un tamaño /26 o mayor para adaptarse al escalado de host. 
   * Para más información sobre el escalado, consulte [Valores de configuración: Escalado de host](../articles/bastion/configuration-settings.md#instance).
   * Para más información sobre el escalado, consulte [Valores de configuración: AzureBastionSubnet](../articles/bastion/configuration-settings.md#instance).
* Cree **AzureBastionSubnet** sin ninguna delegación ni tabla de rutas. 
* Cuando use grupos de seguridad de red en **AzureBastionSubnet**, consulte el artículo [Trabajo con grupos de seguridad de red](../articles/bastion/bastion-nsg.md).
