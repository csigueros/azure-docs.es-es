---
title: Procedimiento de registro de recursos
titleSuffix: Azure Network Function Manager
description: Obtener información sobre cómo registrar recursos y crear identidades administradas asignadas por el usuario
author: prmitt
ms.service: network-function-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: e42527e5fdca01b58c08458bc360268b5d36d7d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093268"
---
# <a name="how-to-register-resources-and-create-user-assigned-managed-identities"></a>Procedimiento de registro de recursos y creación de identidades administradas asignadas por el usuario

Este artículo le ayuda a comprender los requisitos para crear recursos de dispositivo, crear recursos de aplicación administrada e identidad administrada asignada por el usuario para implementar funciones de red.

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>Permisos y registro del proveedor de recursos

Los recursos de Azure Network Function Manager están en el proveedor de recursos Microsoft.HybridNetwork. Registre el identificador de suscripción con el proveedor de recursos Microsoft.HybridNetwork. Para obtener más información sobre cómo registrarse, vea [Tipos y proveedores de recursos de Azure](../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="device-resource-accounts"></a>Cuentas de recursos de dispositivo

Las cuentas que se usan para crear el recurso de dispositivo de Network Function Manager deben asignarse a un rol personalizado al que se asignen las acciones necesarias de la tabla siguiente. Para obtener más información, vea [Roles personalizados](../role-based-access-control/custom-roles.md).

| Nombre | Acción|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|Necesario para leer el recurso de Azure Stack Edge en el que se van a implementar las funciones de red. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |Necesario para leer la sección de propiedades del recurso de Azure Stack Edge. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |Necesario para crear el recurso de dispositivo de Network Function Manager en el recurso de Azure Stack Edge.|
| Microsoft.HybridNetwork/devices/* | Necesario para crear, actualizar y eliminar el recurso de dispositivo de Network Function Manager. |

### <a name="managed-applications-resource-accounts"></a>Cuentas de recursos de aplicaciones administradas

Las cuentas que se usan para crear el recurso de aplicaciones administradas de Azure deben asignarse a un [rol personalizado](../role-based-access-control/custom-roles.md) que tenga asignadas las acciones necesarias de la tabla siguiente: 

|Nombre |Acción |
|---|---|
|[Rol Colaborador de la aplicación administrada](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|Necesario para crear recursos de aplicación administrada.|

## <a name="managed-identity"></a><a name="managed-identity"></a>Identidad administrada

Los asociados de funciones de red que ofrecen sus aplicaciones administradas de Azure con Network Function Manager proporcionan una experiencia que permite implementar una aplicación administrada que está asociada a un recurso de dispositivo de Network Function Manager existente. Al implementar la aplicación administrada del asociado en Azure Portal, debe proporcionar un recurso de identidad administrada asignada por el usuario de Azure que tenga acceso al recurso de dispositivo de Network Function Manager. La identidad administrada asignada por el usuario permite al proveedor de recursos de aplicación administrada y al editor de la función de red los permisos adecuados para el recurso de dispositivo de Network Function Manager que se implementa fuera del grupo de recursos administrado. Para obtener más información, vea [Administración de una identidad administrada asignada por el usuario en Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Para crear una identidad administrada asignada por el usuario para implementar funciones de red:

1. Cree una identidad administrada asignada por el usuario y asígnela a un rol personalizado con permisos para Microsoft.HybridNetwork/devices/join/action. Para obtener más información, vea [Administración de una identidad administrada asignada por el usuario en Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Proporcione esta identidad administrada al crear la aplicación administrada de un asociado en Azure Portal. Para obtener más información, vea [Asignación de acceso de una identidad administrada a un recurso mediante Azure Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte las [Preguntas más frecuentes de Network Function Manager](faq.md).
