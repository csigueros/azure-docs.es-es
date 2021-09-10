---
title: Asignación de un rol de Azure para acceder a datos de cola
titleSuffix: Azure Storage
description: Aprenda a asignar permisos para datos de colas a una entidad de seguridad de Azure Active Directory con el control de acceso basado en rol de Azure (Azure RBAC). Azure Storage admite roles integrados y personalizados de Azure para la autenticación y autorización mediante Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28185918667b35012bd5fd0cc6bb6785d92b29a5
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113761645"
---
# <a name="assign-an-azure-role-for-access-to-queue-data"></a>Asignación de un rol de Azure para acceder a datos de cola

Azure Active Directory (Azure AD) autoriza derechos de acceso a recursos protegidos mediante el [control de acceso basado en rol de Azure (RBAC de Azure)](../../role-based-access-control/overview.md). Azure Storage define un conjunto de roles integrados de Azure que abarcan conjuntos comunes de permisos utilizados para acceder a los datos de colas.

Cuando un rol de Azure se asigna a una entidad de seguridad de Azure AD, Azure concede a esa entidad de seguridad acceso a esos recursos. Una entidad de seguridad de Azure AD puede ser un usuario, un grupo, una entidad de servicio de aplicación o una [identidad de servicio administrada para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para más información sobre el uso de Azure AD para autorizar el acceso a los datos de colas, consulte [Autorización del acceso a colas mediante Azure Active Directory](authorize-access-azure-active-directory.md).

> [!NOTE]
> En este artículo se muestra cómo asignar un rol de Azure para el acceso a los datos de colas en una cuenta de almacenamiento. Para obtener información sobre la asignación de roles para las operaciones de administración en Azure Storage, consulte [Uso del proveedor de recursos de Azure Storage para acceder a los recursos de administración](../common/authorization-resource-provider.md).

## <a name="assign-an-azure-role"></a>Asignación de un rol de Azure

Puede usar Azure Portal, PowerShell, la CLI de Azure o una plantilla de Azure Resource Manager para asignar un rol para el acceso a datos.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para acceder a los datos de colas en Azure Portal con las credenciales de Azure AD, un usuario debe tener las siguientes asignaciones de roles:

- Un rol de acceso a datos, como **Colaborador de datos de Storage Queue**.
- El rol **Lector** de Azure Resource Manager

Para obtener información sobre cómo asignar estos roles a un usuario, siga las instrucciones proporcionadas en [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

El rol [Lector](../../role-based-access-control/built-in-roles.md#reader) es un rol de Azure Resource Manager que permite a los usuarios ver recursos de la cuenta de almacenamiento, pero no modificarlos. No proporciona permisos de lectura en los datos de Azure Storage, sino únicamente en los recursos de administración de la cuenta. El rol **Lector** es necesario para que los usuarios puedan desplazarse a las colas y los mensajes de Azure Portal.

Por ejemplo, si asigna el rol **Colaborador de datos de Storage Queue** al usuario Mary a nivel de una cola llamada **contenedor-ejemplo**, María tendrá acceso de lectura, escritura y eliminación en todos los blobs de esa cola. Sin embargo, si Mary quiere ver una cola en Azure Portal, el rol **Colaborador de datos de Storage Queue** por sí solo no le proporcionará suficientes permisos para desplazarse por el portal hasta la cola para poder verla. Se necesitan más permisos para desplazarse por Portal y ver los otros recursos que estén visibles allí.

A un usuario se le debe asignar el rol **Lector** para usar Azure Portal con las credenciales de Azure AD. Sin embargo, si a un usuario se le ha asignado un rol con permisos **Microsoft.Storage/storageAccounts/listKeys/action**, el usuario puede usar el portal con las claves de cuenta de almacenamiento a través de la autorización de clave compartida. Para usar las claves de cuenta de almacenamiento, se debe permitir el acceso a la clave compartida para la cuenta de almacenamiento. Para obtener más información sobre cómo permitir o no el acceso a la clave compartida, consulte [Impedir la autorización con clave compartida para una cuenta Azure Storage](../common/shared-key-authorization-prevent.md).

También puede asignar un rol de Azure Resource Manager que proporciona permisos adicionales más allá del rol **Lector**. La asignación de los permisos mínimos posibles se recomienda como procedimiento recomendado de seguridad. Para más información, consulte [Procedimientos recomendados para Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Antes de asignarse a sí mismo un rol para el acceso a los datos, puede acceder a los datos de la cuenta de almacenamiento mediante Azure Portal, ya que este también puede usar la clave de cuenta para el acceso a los datos. Para más información, consulte [Elección de la forma de autorizar el acceso a los datos de cola en Azure Portal](../queues/authorize-data-operations-portal.md).
>
> La versión preliminar del Explorador de Storage en Azure Portal no admite el uso de credenciales de Azure AD para ver y modificar datos de cola. Explorador de Storage en Azure Portal usa siempre las claves de cuenta para acceder a los datos. Para usar Explorador de Storage en Azure Portal, debe tener asignado un rol que incluya **Microsoft.Storage/storageAccounts/listkeys/action**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para asignar un rol de Azure a una entidad de seguridad, llame al comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). El formato del comando puede variar en función del ámbito de la asignación. Para ejecutar el comando, debe tener un rol que incluya los permisos **Microsoft.Authorization/roleAssignments/write** asignados en el ámbito correspondiente o superior.

Para asignar un rol cuyo ámbito es una cola, especifique una cadena que contenga el ámbito de la cola en el parámetro `--scope`. El ámbito de una cola tiene este formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

En el siguiente ejemplo se asigna el rol **Colaborador de datos de la cola de Storage Blob** a un usuario y el ámbito se establece una cola denominada *sample-queue*. Asegúrese de reemplazar los valores de ejemplo y los valores de marcador de posición entre corchetes angulares por los suyos propios: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

Para obtener información sobre cómo asignar roles con PowerShell en el ámbito de la suscripción, el grupo de recursos o la cuenta de almacenamiento, consulte [Asignación de roles de Azure mediante Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para asignar un rol de Azure a una entidad de seguridad, use el comando [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create). El formato del comando puede variar en función del ámbito de la asignación. El formato del comando puede variar en función del ámbito de la asignación. Para ejecutar el comando, debe tener un rol que incluya los permisos **Microsoft.Authorization/roleAssignments/write** asignados en el ámbito correspondiente o superior.

Para asignar un rol cuyo ámbito es una cola, especifique una cadena que contenga el ámbito de la cola en el parámetro `--scope`. El ámbito de una cola tiene este formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

En el siguiente ejemplo se asigna el rol **Colaborador de datos de Storage Queue** a un usuario y el ámbito se establece en un nivel de cola. Asegúrese de reemplazar los valores de ejemplo y los valores de marcador de posición entre corchetes angulares por los suyos propios:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

Para obtener información sobre cómo asignar roles con PowerShell en el ámbito de la suscripción, el grupo de recursos o la cuenta de almacenamiento, consulte [Asignación de roles de Azure mediante la CLI de Azure](../../role-based-access-control/role-assignments-cli.md).

# <a name="template"></a>[Plantilla](#tab/template)

Para obtener información sobre cómo usar una plantilla de Azure Resource Manager para asignar un rol de Azure, consulte [Asignación de roles de Azure mediante plantillas de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).

---

Tenga en cuenta los siguientes puntos sobre las asignaciones de roles de Azure en Azure Storage:

- Al crear una cuenta de Azure Storage, no se le asignan automáticamente permisos para tener acceso a datos a través de Azure AD. Tiene que asignarse a sí mismo de forma explícita un rol de Azure para Azure Storage. Puede asignarlo al nivel de su suscripción, grupo de recursos, cuenta de almacenamiento o cola.
- Si la cuenta de almacenamiento se bloquea con un bloqueo de solo lectura de Azure Resource Manager, el bloqueo evita la asignación de roles de Azure que estén limitados a la cuenta de almacenamiento o a una cola.

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es el control de acceso basado en rol de Azure (RBAC)?](../../role-based-access-control/overview.md)
- [Procedimientos recomendados para Azure RBAC](../../role-based-access-control/best-practices.md)