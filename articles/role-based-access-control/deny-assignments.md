---
title: 'Descripción de las asignaciones de denegación de Azure: RBAC de Azure'
description: Aprenda sobre as asignaciones de denegación en el control de acceso basado en rol (RBAC de Azure)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 38427b0686e830939025075536ac79c95ebde4cd
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362959"
---
# <a name="understand-azure-deny-assignments"></a>Descripción de las asignaciones de denegación de Azure

De forma similar a una asignación de roles, una *asignación de denegación* asocia un conjunto de acciones de denegación a un usuario, grupo o entidad de servicio en un ámbito determinado con el fin de denegar el acceso. Las asignaciones de denegación impiden que los usuarios realicen acciones concretas en recursos de Azure, aunque una asignación de roles les conceda acceso.

En este artículo se describe cómo se definen las asignaciones de denegación.

## <a name="how-deny-assignments-are-created"></a>Cómo se crean las asignaciones de denegación

Azure crea y administra las asignaciones de denegación para proteger los recursos. Azure Blueprints y las aplicaciones administradas de Azure usan las asignaciones de denegación para proteger los recursos administrados por el sistema. Azure Blueprints y las aplicaciones administradas de Azure son la única manera de crear las asignaciones de denegación. No se pueden crear directamente asignaciones de denegación propias. Para más información sobre cómo Blueprints usa las asignaciones de denegación para bloquear recursos, vea [Comprensión del bloqueo de recursos en planos técnicos de Azure Blueprint](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> No se pueden crear directamente asignaciones de denegación propias.

## <a name="compare-role-assignments-and-deny-assignments"></a>Asignaciones de roles de comparación y asignaciones de denegación

Las asignaciones de denegación siguen un patrón similar que las asignaciones de roles, pero también presentan algunas diferencias.

| Capacidad | Asignación de roles | Asignación de denegación |
| --- | --- | --- |
| Conceder acceso | :heavy_check_mark: |  |
| Denegación del acceso |  | :heavy_check_mark: |
| Se pueden crear directamente | :heavy_check_mark: |  |
| Se aplican en un ámbito | :heavy_check_mark: | :heavy_check_mark: |
| Excluyen entidades de seguridad |  | :heavy_check_mark: |
| Impiden la herencia de los ámbitos secundarios |  | :heavy_check_mark: |
| Se aplican a las asignaciones de [administrador de suscripciones clásicas](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Propiedades de la asignación de denegación

 Una asignación de denegación tiene las siguientes propiedades:

> [!div class="mx-tableFixed"]
> | Propiedad | Obligatorio | Tipo | Descripción |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sí | String | El nombre para mostrar de la asignación de denegación. Los nombres deben ser únicos para un ámbito determinado. |
> | `Description` | No | String | La descripción de la asignación de denegación. |
> | `Permissions.Actions` | Al menos una Actions o una DataActions | String[] | Una matriz de cadenas que especifican las acciones del plano de control a las que la asignación de denegación bloquea el acceso. |
> | `Permissions.NotActions` | No | String[] | Una matriz de cadenas que especifican la acción del plano de control que se debe excluir de la asignación de denegación. |
> | `Permissions.DataActions` | Al menos una Actions o una DataActions | String[] | Una matriz de cadenas que especifican las acciones del plano de datos a las que la asignación de denegación bloquea el acceso. |
> | `Permissions.NotDataActions` | No | String[] | Una matriz de cadenas que especifican las acciones del plano de datos que deben excluirse de la asignación de denegación. |
> | `Scope` | No | String | Una cadena que especifica el ámbito al que se aplica la asignación de denegación. |
> | `DoNotApplyToChildScopes` | No | Boolean | Especifica si la asignación de denegación se aplica a los ámbitos secundarios. El valor predeterminado es False. |
> | `Principals[i].Id` | Sí | String[] | Matriz de identificadores de objetos de entidad de seguridad de Azure AD (usuario, grupo o entidad de servicio) a los que se aplica la asignación de denegación. Establézcala en un GUID `00000000-0000-0000-0000-000000000000` vacío para representar a todas las entidades de seguridad. |
> | `Principals[i].Type` | No | String[] | Una matriz de tipos de objetos representados por Principals[i].Id. Establézcala en `SystemDefined` para representar a todas las entidades de seguridad. |
> | `ExcludePrincipals[i].Id` | No | String[] | Matriz de identificadores de objetos de entidades de seguridad de Azure AD (usuario, grupo o entidad de servicio) a los que no se aplica la asignación de denegación. |
> | `ExcludePrincipals[i].Type` | No | String[] | Una matriz de tipos de objetos representados por ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | No | Boolean | Especifica si Azure ha creado esta asignación de denegación y no se puede editar ni eliminar. Actualmente, el sistema protege todas las asignaciones de denegación. |

## <a name="the-all-principals-principal"></a>La entidad de seguridad Todas las entidades de seguridad

Con objeto de admitir las asignaciones de denegación, se ha introducido una entidad de seguridad definida por el sistema llamada *Todas las entidades de seguridad*. Esta entidad de seguridad representa a todos los usuarios, grupos, entidades de servicio y identidades administradas de un directorio de Azure AD. Si el identificador de la entidad de seguridad es un GUID cero `00000000-0000-0000-0000-000000000000` y el tipo de entidad de seguridad es `SystemDefined`, la entidad de seguridad representa a todas las entidades de seguridad. En la salida de Azure PowerShell, la entidad Todas las entidades muestra un aspecto como el siguiente:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Todas las entidades se puede combinar con `ExcludePrincipals` para denegar todas las entidades de seguridad, salvo algunos usuarios. Todas las entidades tiene las siguientes restricciones:

- Solo se puede utilizar en `Principals` y no se puede usar en `ExcludePrincipals`.
- `Principals[i].Type` se debe establecer en `SystemDefined`.

## <a name="next-steps"></a>Pasos siguientes

* [Tutorial: Protección de los nuevos recursos con bloqueos de recursos de Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md)
* [Enumeración de las asignaciones de denegación de Azure mediante Azure Portal](deny-assignments-portal.md)
