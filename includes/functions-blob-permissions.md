---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 32184e2b836aa881dc3175bb0790fc9cbbca1df7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996447"
---
Deberá crear una asignación de roles que proporcione acceso al contenedor de blobs en tiempo de ejecución. Los roles de administración, como [propietario](../articles/role-based-access-control/built-in-roles.md#owner) no son suficientes. En la tabla siguiente se muestran los roles integrados que se recomiendan al usar la extensión Blob Storage en funcionamiento normal. Puede que la aplicación precise permisos adicionales en función del código que escriba.

| Tipo de enlace   | Roles integrados de ejemplo                |
|----------------|---------------------------------------|
| Desencadenador        | [Propietario de datos de Storage Blob]<sup>1</sup> |
| Enlace de entrada  | [Lector de datos de blobs de almacenamiento]            |
| Enlace de salida | [Propietario de datos de blobs de almacenamiento]             |

<sup>1</sup> En algunas configuraciones, un desencadenador de blobs puede requerir además el rol de [colaborador de datos de la cola de Storage Blob](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor).

[Lector de datos de blobs de almacenamiento]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader
[Propietario de datos de blobs de almacenamiento]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
