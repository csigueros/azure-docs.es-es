---
title: Asignación de un rol de Azure para acceder a datos de tablas (versión preliminar)
titleSuffix: Azure Storage
description: Aprenda a asignar permisos para datos de tablas (versión preliminar) a una entidad de seguridad de Azure Active Directory con el control de acceso basado en rol de Azure (Azure RBAC). Azure Storage admite roles integrados y personalizados de Azure para la autenticación y autorización mediante Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19c377c1b6d0b5cad7515ca199133f8d10c8742b
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760610"
---
# <a name="assign-an-azure-role-for-access-to-table-data-preview"></a>Asignación de un rol de Azure para acceder a datos de tablas (versión preliminar)

Azure Active Directory (Azure AD) autoriza derechos de acceso a recursos protegidos mediante el [control de acceso basado en rol de Azure (RBAC de Azure)](../../role-based-access-control/overview.md). Azure Storage define un conjunto de roles integrados de Azure que abarcan conjuntos comunes de permisos utilizados para acceder a los datos de tablas (versión preliminar).

Cuando un rol de Azure se asigna a una entidad de seguridad de Azure AD, Azure concede a esa entidad de seguridad acceso a esos recursos. Una entidad de seguridad de Azure AD puede ser un usuario, un grupo, una entidad de servicio de aplicación o una [identidad de servicio administrada para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para más información sobre el uso de Azure AD para autorizar el acceso a los datos de tablas, consulte [Autorización del acceso a tablas mediante Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> La autorización con Azure AD para tablas se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="assign-an-azure-role"></a>Asignación de un rol de Azure

Puede usar PowerShell, la CLI de Azure o una plantilla de Azure Resource Manager para asignar un rol para el acceso a datos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para asignar un rol de Azure a una entidad de seguridad, llame al comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). El formato del comando puede variar en función del ámbito de la asignación. Para ejecutar el comando, debe tener un rol que incluya los permisos **Microsoft.Authorization/roleAssignments/write** asignados en el ámbito correspondiente o superior.

Para asignar un rol limitado a una tabla, especifique una cadena que contenga el ámbito de la tabla para el parámetro `--scope`. El ámbito de una tabla tiene este formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

En el siguiente ejemplo se asigna el rol **Colaborador de datos de Storage Queue** a un usuario, limitado a una tabla. Asegúrese de reemplazar los valores de ejemplo y los valores de marcador de posición entre corchetes angulares por los suyos propios:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Table Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

Para obtener información sobre cómo asignar roles con PowerShell en el ámbito de la suscripción, el grupo de recursos o la cuenta de almacenamiento, consulte [Asignación de roles de Azure mediante Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para asignar un rol de Azure a una entidad de seguridad, use el comando [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create). El formato del comando puede variar en función del ámbito de la asignación. El formato del comando puede variar en función del ámbito de la asignación. Para ejecutar el comando, debe tener un rol que incluya los permisos **Microsoft.Authorization/roleAssignments/write** asignados en el ámbito correspondiente o superior.

Para asignar un rol limitado a una tabla, especifique una cadena que contenga el ámbito de la tabla para el parámetro `--scope`. El ámbito de una tabla tiene este formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

En el siguiente ejemplo se asigna el rol **Colaborador de datos de Storage Table** a un usuario, limitado al nivel de la tabla. Asegúrese de reemplazar los valores de ejemplo y los valores de marcador de posición entre corchetes angulares por los suyos propios:

```azurecli-interactive
az role assignment create \
    --role "Storage Table Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

Para obtener información sobre cómo asignar roles con PowerShell en el ámbito de la suscripción, el grupo de recursos o la cuenta de almacenamiento, consulte [Asignación de roles de Azure mediante la CLI de Azure](../../role-based-access-control/role-assignments-cli.md).

# <a name="template"></a>[Plantilla](#tab/template)

Para obtener información sobre cómo usar una plantilla de Azure Resource Manager para asignar un rol de Azure, consulte [Asignación de roles de Azure mediante plantillas de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).

---

Tenga en cuenta los siguientes puntos sobre las asignaciones de roles de Azure en Azure Storage:

- Al crear una cuenta de Azure Storage, no se le asignan automáticamente permisos para tener acceso a datos a través de Azure AD. Tiene que asignarse a sí mismo de forma explícita un rol de Azure para Azure Storage. Puede asignarlo a nivel de su suscripción, grupo de recursos, cuenta de almacenamiento o tabla.
- Si la cuenta de almacenamiento se bloquea con un bloqueo de solo lectura de Azure Resource Manager, el bloqueo evita la asignación de roles de Azure que estén limitados a la cuenta de almacenamiento o a una tabla.
- La versión preliminar del Explorador de Storage en Azure Portal no admite el uso de credenciales de Azure AD para ver y modificar datos de tablas. Explorador de Storage en Azure Portal usa siempre las claves de cuenta para acceder a los datos. Para usar Explorador de Storage en Azure Portal, debe tener asignado un rol que incluya **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es el control de acceso basado en rol de Azure (RBAC)?](../../role-based-access-control/overview.md)
- [Procedimientos recomendados para Azure RBAC](../../role-based-access-control/best-practices.md)
