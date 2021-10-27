---
title: Configuración de identidades administradas con Azure AD para su cuenta de Azure Cosmos DB
description: Aprenda a configurar las identidades administradas con Azure Active Directory para la cuenta de Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2021
ms.author: thweiss
ms.openlocfilehash: 826afef5d637278628146af8a35f78232e5b3531
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132928"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Configuración de identidades administradas con Azure Active Directory para la cuenta de Azure Cosmos DB.
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Las identidades administradas para los recursos de Azure proporcionan a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. En este artículo se muestra cómo crear una identidad administrada para cuentas de Azure Cosmos DB.

## <a name="prerequisites"></a>Prerrequisitos

- Si no está familiarizado con las identidades administradas para los recursos de Azure, vea [¿Qué son las identidades administradas para recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md). Para obtener información sobre los tipos de identidades administradas, vea [Tipos de identidad administrada](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Para configurar identidades administradas, la cuenta debe tener el [rol Colaborador de la cuenta de DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor).

## <a name="add-a-system-assigned-identity"></a>Adición de una identidad asignada por el sistema

### <a name="using-the-azure-portal"></a>Uso de Azure Portal

Para habilitar una identidad administrada asignada por el sistema en una cuenta de Azure Cosmos DB existente, vaya a la cuenta en Azure Portal y seleccione **Identidad** en el menú de la izquierda.

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="Entrada del menú Identidad" border="true":::

En la sección **Asignado por el sistema**, cambie el **Estado** a Activado y seleccione **Guardar**. Se le pedirá que confirme la creación de la identidad administrada asignada por el sistema.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-system-assigned.png" alt-text="Habilitación de una identidad asignada por el sistema" border="true":::

Una vez creada y asignada la identidad, puede recuperar su id. de objeto (entidad de seguridad).

:::image type="content" source="./media/how-to-setup-managed-identity/system-identity-enabled.png" alt-text="Recuperación del id. de objeto de una identidad asignada por el sistema" border="true":::

### <a name="using-an-azure-resource-manager-arm-template"></a>Uso de una plantilla de Azure Resource Manager (ARM)

> [!IMPORTANT]
> Asegúrese de usar una `apiVersion` de `2021-03-15` o una versión superior al trabajar con identidades administradas.

Para habilitar una identidad asignada por el sistema en una cuenta de Azure Cosmos DB nueva o existente, incluya la siguiente propiedad en la definición de recursos:

```json
"identity": {
    "type": "SystemAssigned"
}
```

La sección `resources` de la plantilla de ARM debería tener un aspecto similar al siguiente:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
]
```

Una vez creada o actualizada la cuenta de Azure Cosmos DB, se mostrará la siguiente propiedad:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

Para habilitar una identidad asignada por el sistema al crear una nueva cuenta de Azure Cosmos DB, agregue la opción `--assign-identity`:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity
```

También puede agregar una identidad asignada por el sistema en una cuenta existente mediante el comando `az cosmosdb identity assign`:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
```

Después de crear o actualizar su cuenta de Azure Cosmos DB, puede obtener la identidad asignada con el comando `az cosmosdb identity show`:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity show \
    -n $accountName \
    -g $resourceGroupName
```

```json
{
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="add-a-user-assigned-identity"></a>Adición de una identidad asignada por el usuario

### <a name="using-the-azure-portal"></a>Uso de Azure Portal

Para habilitar una identidad administrada asignada por el usuario en una cuenta de Azure Cosmos DB existente, vaya a la cuenta en Azure Portal y seleccione **Identidad** en el menú de la izquierda.

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="Entrada del menú Identidad" border="true":::

En la sección **Asignado por el usuario**, seleccione **+ Agregar**.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-1.png" alt-text="Habilitación de una identidad asignada por el usuario" border="true":::

Busque y seleccione todas las identidades que desea asignar a la cuenta de Azure Cosmos DB y, a continuación, seleccione **Agregar**.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-2.png" alt-text="Selección de todas las identidades que se asignarán" border="true":::

### <a name="using-an-azure-resource-manager-arm-template"></a>Uso de una plantilla de Azure Resource Manager (ARM)

> [!IMPORTANT]
> Asegúrese de usar una `apiVersion` de `2021-03-15` o una versión superior al trabajar con identidades administradas.

Para habilitar una identidad asignada por el usuario en una cuenta de Azure Cosmos DB nueva o existente, incluya la siguiente propiedad en la definición de recursos:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<identity-resource-id>": {}
    }
}
```

La sección `resources` de la plantilla de ARM debería tener un aspecto similar al siguiente:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "<identity-resource-id>": {}
            }
        },
        // ...
    },
    // ...
]
```

Una vez creada o actualizada la cuenta de Azure Cosmos DB, se mostrará la siguiente propiedad:

```json
"identity": {
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

Para habilitar una identidad asignada por el usuario al crear una nueva cuenta de Azure Cosmos DB, agregue la opción `--assign-identity` y pase el id. de recurso de la identidad que desea asignar:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity <identity-resource-id>
```

También puede agregar una identidad asignada por el usuario en una cuenta existente mediante el comando `az cosmosdb identity assign`:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
    --identities <identity-resource-id>
```

Después de crear o actualizar su cuenta de Azure Cosmos DB, puede obtener la identidad asignada con el comando `az cosmosdb identity show`:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity show \
    -n $accountName \
    -g $resourceGroupName
```

```json
{
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="remove-a-system-assigned-or-user-assigned-identity"></a>Eliminación de una identidad asignada por el sistema o el usuario

### <a name="using-an-azure-resource-manager-arm-template"></a>Uso de una plantilla de Azure Resource Manager (ARM)

> [!IMPORTANT]
> Asegúrese de usar una `apiVersion` de `2021-03-15` o una versión superior al trabajar con identidades administradas.

Para quitar una identidad asignada por el sistema de la cuenta de Azure Cosmos DB, establezca `type` de la propiedad `identity` en `None`:

```json
"identity": {
    "type": "None"
}
```

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

Para quitar todas las identidades administradas de la cuenta de Azure Cosmos DB, use el comando `az cosmosdb identity remove`:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity remove \
    -n $accountName \
    -g $resourceGroupName
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre las [identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md)
- Obtenga más información sobre las [claves administradas por el cliente en Azure Cosmos DB](how-to-setup-cmk.md).
