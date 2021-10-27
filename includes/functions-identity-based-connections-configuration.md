---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/11/2021
ms.author: mahender
ms.openlocfilehash: 8cef6aa5daa8bbbdda6971724343e65ea7e47aef
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992770"
---
Cuando se hospeda en el servicio de Azure Functions, las conexiones basadas en identidades usan una [identidad administrada](../articles/app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json). La identidad asignada por el sistema se usa de manera predeterminada, aunque se puede especificar una identidad asignada por el usuario con las propiedades `credential` y `clientID`. Cuando se ejecuta en otros contextos, como el de desarrollo local, se usa en su lugar la identidad del desarrollador, aunque se puede personalizar. Consulte [Desarrollo local con conexiones basadas en identidades](../articles/azure-functions/functions-reference.md#local-development-with-identity-based-connections).

#### <a name="grant-permission-to-the-identity"></a>Concesión de permiso a la identidad

Cualquier identidad que se utilice debe tener permisos para realizar las acciones previstas. Deberá [asignar un rol en Azure RBAC](../articles/role-based-access-control/role-assignments-steps.md) mediante roles integrados o personalizados que proporcionen esos permisos.

> [!IMPORTANT]
> Es posible que el servicio de destino muestre algunos permisos que no son necesarios para todos los contextos. Siempre que sea posible, respete el **principio de privilegios mínimos** y conceda solo los privilegios necesarios a la identidad. Por ejemplo, si la aplicación solo necesita poder leer desde un origen de datos, use un rol que solo tenga permiso de lectura. Sería inadecuado asignar un rol que también permita escribir en ese servicio, ya que sería un permiso excesivo para una operación de lectura. De forma similar, le interesa asegurarse de que la asignación de roles esté limitada solo a los recursos que se deben leer.
