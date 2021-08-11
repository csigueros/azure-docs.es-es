---
title: 'Administración de roles del plano de datos del HSM administrado: Azure Key Vault | Microsoft Docs'
description: Use este artículo para administrar las asignaciones de roles para el HSM administrado.
services: key-vault
author: mbaldwin
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 7a4179d35faffbf04a70a63aafead120259802f3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471302"
---
# <a name="managed-hsm-role-management"></a>Administración de roles de Managed HSM

> [!NOTE]
> Key Vault admite dos tipos de recursos: almacenes y HSM administrados. Este artículo trata sobre los **HSM administrados**. Si desea obtener información sobre cómo administrar un almacén, consulte [Administración de Key Vault mediante la CLI de Azure](../general/manage-with-cli2.md).

Para obtener información general sobre los HSM administrados, consulte [¿Qué es HSM administrado?](overview.md). Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

En este artículo se muestra cómo administrar roles para un plano de datos de HSM administrado. Consulte [Control de acceso de HSM administrado](access-control.md) para obtener información sobre el modelo de control de acceso de HSM administrado.

Para permitir que una entidad de seguridad (como un usuario, una entidad de servicio, un grupo o una identidad administrada) realice operaciones del plano de datos del HSM administrado, se le debe asignar un rol que permita realizar esas operaciones. Por ejemplo, si desea permitir que una aplicación realice una operación de firma mediante una clave, se le debe asignar un rol que contenga "Microsoft.KeyVault/managedHSM/keys/sign/action" como una de las acciones de datos. Un rol se puede asignar a un ámbito específico. El RBAC local de Managed HSM admite dos ámbitos: todo HSM (`/` o `/keys`) y por clave (`/keys/<keyname>`).

Para obtener una lista de todos los roles integrados de HSM administrado y las operaciones que permiten, consulte [Roles integrados de HSM administrado](built-in-roles.md).

## <a name="prerequisites"></a>Prerrequisitos

Para usar los comandos de la CLI de Azure, debe tener los siguientes elementos:

* Una suscripción a Microsoft Azure. Si no tiene una, puede registrarse para una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).
* La CLI de Azure, versión 2.25.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).
* Un HSM administrado en la suscripción. Consulte [Quickstart: Aprovisionamiento y activación de un HSM administrado mediante la CLI de Azure](quick-create-cli.md) para aprovisionar y activar un HSM administrado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Para iniciar sesión en Azure mediante la CLI puede escribir:

```azurecli
az login
```

Para más información sobre las opciones de inicio de sesión mediante la CLI, consulte [Inicio de sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli).

## <a name="create-a-new-role-assignment"></a>Creación de una nueva asignación de roles

### <a name="assign-roles-for-all-keys"></a>Asignación de roles para todas las claves

Use el comando `az keyvault role assignment create` para asignar un rol de **Managed HSM Crypto Officer** al usuario identificado por el nombre principal de usuario **user2\@contoso.com** para todas las **claves** (ámbito `/keys`) de ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Asignación de roles para una clave específica

Use el comando `az keyvault role assignment create` para asignar un rol de **Managed HSM Crypto Officer** al usuario identificado por el nombre principal de usuario **user2\@contoso.com** para una clave específica llamada **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Enumeración de las asignaciones de roles existentes

Use `az keyvault role assignment list` para enumerar las asignaciones de roles.

Todas las asignaciones de roles del ámbito / (opción predeterminada cuando no se especifica --scope) para todos los usuarios (opción predeterminada cuando no se especifica --assignee).

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Todas las asignaciones de roles en el nivel de HSM para el usuario específico **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> Cuando el ámbito es / (o /keys), el comando list enumera únicamente todas las asignaciones de roles en el nivel superior y no muestra las asignaciones de roles en el nivel de clave individual.

Todas las asignaciones de roles para el usuario específico **user2@contoso.com** para la clave específica **myrsakey**.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Una asignación de roles específica para el rol **Managed HSM Crypto Officer** para el usuario específico **user2@contoso.com** para la clave específica **myrsakey**.


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Eliminación de una asignación de roles

Use el comando `az keyvault role assignment delete` para eliminar un rol de **Managed HSM Crypto Officer** asignado al usuario **user2\@contoso.com** para la clave **myrsakey2**.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Enumeración de todas las definiciones de roles disponibles

Use el comando `az keyvault role definition list` para enumerar todas las definiciones de roles.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="create-a-new-role-definition"></a>Creación de una nueva definición de roles

HSM administrado tiene varios roles integrados (predefinidos) que son útiles para los escenarios de uso más comunes. Puede definir su propio rol con una lista de acciones específicas que el rol puede realizar. A continuación, puede asignar este rol a las entidades de seguridad a fin de concederles el permiso para las acciones especificadas. 

Use el comando `az keyvault role definition create` para un rol denominado **My Custom Role** (Mi rol personalizado) mediante una cadena JSON.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
    "roleName": "My Custom Role",
    "description": "The description of the custom rule.",
    "actions": [],
    "notActions": [],
    "dataActions": [
        "Microsoft.KeyVault/managedHsm/keys/read/action"
    ],
    "notDataActions": []
}'
```

Use el comando `az keyvault role definition create` para un rol de un archivo denominado **my-custom-role-definition.jsen** que contenga la cadena JSON para una definición de rol. Vea el ejemplo anterior.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition @my-custom-role-definition.json
```

## <a name="show-details-of-a-role-definition"></a>Mostrar detalles de una definición de rol

Use el comando `az keyvault role definition show` para ver los detalles de una definición de rol específica mediante el nombre (un GUID).

```azurecli-interactive
az keyvault role definition show --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="update-a-custom-role-definition"></a>Actualización de una definición de rol personalizado

Use el comando `az keyvault role definition update` para actualizar un rol denominado **My Custom Role** (Mi rol personalizado) mediante una cadena JSON.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
            "roleName": "My Custom Role",
            "name": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "id": "Microsoft.KeyVault/providers/Microsoft.Authorization/roleDefinitions/xxxxxxxx-
        xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "description": "The description of the custom rule.",
            "actions": [],
            "notActions": [],
            "dataActions": [
                "Microsoft.KeyVault/managedHsm/keys/read/action",
                "Microsoft.KeyVault/managedHsm/keys/write/action",
                "Microsoft.KeyVault/managedHsm/keys/backup/action",
                "Microsoft.KeyVault/managedHsm/keys/create"
            ],
            "notDataActions": []
        }'
```

## <a name="delete-custom-role-definition"></a>Eliminación de definiciones de roles personalizadas

Use el comando `az keyvault role definition delete` para ver los detalles de una definición de rol específica mediante el nombre (un GUID). 
```azurecli-interactive
az keyvault role definition delete --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

> [!NOTE]
> Los roles integrados no se pueden eliminar. Cuando se eliminan los roles personalizados, todas las asignaciones de roles que utilizan ese rol personalizado quedan inactivas.


## <a name="next-steps"></a>Pasos siguientes

- Consulte la información general sobre el [control de acceso basado en rol de Azure (Azure RBAC)](../../role-based-access-control/overview.md).
- Consulte un tutorial sobre la [Administración de roles de HSM administrado](role-management.md).
- Obtenga más información sobre el [modelo de control de acceso de HSM administrado](access-control.md).
- Consulte todos los [roles integrados de RBAC local de HSM administrado](built-in-roles.md).
