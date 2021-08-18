---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: b37e8f32720cbfcc217a14440257410c02296c5a
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136403"
---
Key Vault admite hasta 1024 entradas de directiva de acceso, donde cada entrada concede un conjunto de permisos distinto a una entidad de servicio particular: Debido a esta limitación, se recomienda asignar directivas de acceso a grupos de usuarios, siempre que sea posible, en lugar de a usuarios individuales. El uso de grupos facilita la administración de permisos para varias personas de la organización. Para más información, consulte [Administración del acceso a recursos y aplicaciones con grupos en Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md).
