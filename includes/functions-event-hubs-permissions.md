---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b0451d7a3f14002ec53cdb410144c6e69f1337a9
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137395"
---
Deberá crear una asignación de roles que proporcione acceso al centro de eventos en tiempo de ejecución. El ámbito de la asignación de roles debe ser para un espacio de nombres de Event Hubs, no para el propio centro de eventos. Los roles de administración, como [Propietario](../articles/role-based-access-control/built-in-roles.md#owner), no son suficientes. En la tabla siguiente se muestran los roles integrados que se recomiendan al usar la extensión de Event Hubs con un funcionamiento normal. Puede que la aplicación precise permisos adicionales en función del código que escriba.

| Tipo de enlace   | Roles integrados de ejemplo                                          |
|----------------|-----------------------------------------------------------------|
| Desencadenador        | [Receptor de los datos de Azure Event Hubs], [Propietario de los datos de Azure Event Hubs] |
| Enlace de salida | [Emisor de datos de Azure Event Hubs]                                  |

[Receptor de datos de Azure Event Hubs]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver
[Emisor de datos de Azure Event Hubs]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender
[Propietario de los datos de Azure Event Hubs]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner