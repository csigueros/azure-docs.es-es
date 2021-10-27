---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b4ce7e128304aa1befd7029ed2a692b1d7f391f3
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137401"
---
Deberá crear una asignación de roles que proporcione acceso a los temas y las colas en tiempo de ejecución. El ámbito de la asignación de roles debe ser para un espacio de nombres de Service Bus y no estar limitada a una cola o un tema específicos. Los roles de administración, como [Propietario](../articles/role-based-access-control/built-in-roles.md#owner), no son suficientes. En la tabla siguiente, se muestran los roles integrados que se recomiendan al usar la extensión de Service Bus en las operaciones normales. Puede que la aplicación precise permisos adicionales en función del código que escriba.

| Tipo de enlace   | Roles integrados de ejemplo                                            |
|----------------|-------------------------------------------------------------------|
| Desencadenador        | [Receptor de los datos de Azure Service Bus], [Propietario de los datos de Azure Service Bus] |
| Enlace de salida | [Emisor de datos de Azure Service Bus]                                   |

[Receptor de datos de Azure Service Bus]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver
[Emisor de datos de Azure Service Bus]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-sender
[Propietario de los datos de Azure Service Bus]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-owner
