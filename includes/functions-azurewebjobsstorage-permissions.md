---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e8b274cd3d9398694661b06a5a1c9e13e42c6c89
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137380"
---
Deberá crear una asignación de roles que proporcione acceso a la cuenta de almacenamiento para "AzureWebJobsStorage" en entorno de ejecución. Los roles de administración, como [Propietario](../articles/role-based-access-control/built-in-roles.md#owner), no son suficientes. El rol de [Propietario de datos de blobs de almacenamiento] cubre las necesidades básicas del almacenamiento de host de Functions: el entorno de ejecución necesita acceso de lectura y escritura a blobs y la capacidad de crear contenedores. Existen algunas situaciones, como con el uso del desencadenador de blobs, en las que el [Colaborador de datos de la cola de Storage] también puede ser necesario. Puede que necesite permisos adicionales si usa "AzureWebJobsStorage" para cualquier otro fin.

[Propietario de datos de blobs de almacenamiento]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
[Colaborador de datos de la cola de Storage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor