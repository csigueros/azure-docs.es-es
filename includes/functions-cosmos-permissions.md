---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: d95d50d6e5f69922856e6a3045dfccdf91ddbf1a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994338"
---
Deberá crear una asignación de roles que proporcione acceso a la cuenta de base de datos en tiempo de ejecución. Los roles de administración, como [propietario](../articles/role-based-access-control/built-in-roles.md#owner) no son suficientes. En la tabla siguiente se muestran los roles integrados que se recomiendan al usar la extensión de Cosmos DB en funcionamiento normal. Puede que la aplicación precise permisos adicionales en función del código que escriba.

| Tipo de enlace   | Roles integrados de ejemplo                |
|----------------|---------------------------------------|
| Desencadenador        | [Lector de datos integrado de Cosmos DB]      |
| Enlace de salida | [Colaborador de datos integrado de Cosmos DB] |


[Lector de datos integrado de Cosmos DB]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions
[Colaborador de datos integrado de Cosmos DB]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions