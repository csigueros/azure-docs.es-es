---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e9897a9fb28f3ea9489e80b0dd4bb814c836b23b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992743"
---
Deberá crear una asignación de roles que proporcione acceso a la cola en tiempo de ejecución. Los roles de administración, como [propietario](../articles/role-based-access-control/built-in-roles.md#owner) no son suficientes. En la tabla siguiente se muestran los roles integrados que se recomiendan al usar la extensión Queue Storage en funcionamiento normal. Puede que la aplicación precise permisos adicionales en función del código que escriba.

| Tipo de enlace   | Roles integrados de ejemplo                                                |
|----------------|-----------------------------------------------------------------------|
| Desencadenador        | [Lector de datos de la cola de Storage Blob], [Procesador de mensajes de datos de Queue Storage]   |
| Enlace de salida | [Colaborador de datos de la cola de Storage Blob], [Remitente de mensajes de datos de Queue Storage] |

[Lector de datos de la cola de Storage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader
[Procesador de mensajes de datos de la cola de Storage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor
[Emisor de mensajes de datos de la cola de Storage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender
[Colaborador de datos de la cola de Storage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor
