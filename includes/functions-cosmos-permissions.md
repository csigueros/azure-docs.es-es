---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2021
ms.author: mahender
ms.openlocfilehash: 15f68c6339135139f6f6fcbf335306f6edf2a3c6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257142"
---
Deberá crear una asignación de roles que proporcione acceso a la cuenta de base de datos en tiempo de ejecución. Los roles de administración, como [propietario](../articles/role-based-access-control/built-in-roles.md#owner) no son suficientes. En la tabla siguiente se muestran los roles integrados que se recomiendan al usar la extensión de Cosmos DB en funcionamiento normal. Puede que la aplicación precise permisos adicionales en función del código que escriba.

| Tipo de enlace   | Roles integrados de ejemplo                |
|----------------|---------------------------------------|
| Desencadenador        | [Colaborador de datos integrado de Cosmos DB] |
| Enlace de entrada  | [Lector de datos integrado de Cosmos DB]      |
| Enlace de salida | [Colaborador de datos integrado de Cosmos DB] |


[Lector de datos integrado de Cosmos DB]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions
[Colaborador de datos integrado de Cosmos DB]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions