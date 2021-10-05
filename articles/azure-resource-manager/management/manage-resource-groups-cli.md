---
title: 'Administración de grupos de recursos: CLI de Azure'
description: Use la CLI de Azure para administrar grupos de recursos con Azure Resource Manager. Muestra cómo crear, enumerar y eliminar grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2088f1b4f201f149fc4d51bf608cb5868802d4d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731122"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Administración de grupos de recursos de Azure Resource Manager mediante la CLI de Azure

Obtenga información sobre cómo utilizar la CLI de Azure con [Azure Resource Manager](overview.md) para administrar los grupos de recursos de Azure. Para administrar los recursos de Azure, vea [Administración de recursos de Azure mediante la CLI de Azure](manage-resources-cli.md).

Otros artículos sobre cómo administrar grupos de recursos:

- [Administración de grupos de recursos de Azure con Azure Portal](manage-resources-portal.md)
- [Administración de grupos de recursos de Azure mediante Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>¿Qué es un grupo de recursos?

Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. El grupo de recursos puede incluir todos los recursos de la solución o solo aquellos que se desean administrar como grupo. Para decidir cómo agregar los recursos a los grupos de recursos, tenga en cuenta lo que más conviene a su organización. Por lo general, se recomienda agregar recursos que compartan el mismo ciclo de vida al mismo grupo de recursos para que los pueda implementar, actualizar y eliminar con facilidad como un grupo.

Los grupos de recursos almacenan metadatos acerca de los recursos. Al especificar la ubicación del grupo de recursos, se especifica el lugar en que dichos metadatos se almacenan. Por motivos de compatibilidad, es posible que sea preciso asegurarse de que los datos se almacenan en una región concreta.

## <a name="create-resource-groups"></a>Crear grupos de recursos

Para crear un grupo de recursos, use [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Enumeración de grupos de recursos

Para mostrar los grupos de recursos de su suscripción, use [az group list](/cli/azure/group#az_group_list).

```azurecli-interactive
az group list
```

Para obtener un grupo de recursos, use [az group show](/cli/azure/group#az_group_show).

```azurecli-interactive
az group show --name exampleGroup
```

## <a name="delete-resource-groups"></a>Eliminación de grupos de recursos

Para eliminar un grupo de recursos, use [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name exampleGroup
```

Para obtener más información sobre cómo ordena Azure Resource Manager la eliminación de recursos, consulte [Eliminación del grupo de recursos en Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources"></a>Implementación de recursos

Puede implementar recursos de Azure usando la CLI de Azure o implementando una plantilla de Azure Resource Manager (ARM) o un archivo Bicep.

En el ejemplo siguiente se crea una cuenta de almacenamiento. El nombre que proporcione para la cuenta de almacenamiento debe ser único en Azure.

```azurecli-interactive
az storage account create --resource-group exampleGroup --name examplestore --location westus --sku Standard_LRS --kind StorageV2
```

Para implementar una plantilla de ARM o un archivo Bicep, use [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create).

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-file storage.bicep
```

Para obtener más información sobre la implementación de una plantilla de ARM, vea [Implementación de recursos con plantillas de ARM y la CLI de Azure](../templates/deploy-cli.md).

Para obtener más información sobre la implementación de un archivo Bicep, vea [Implementación de recursos con Bicep y la CLI de Azure](../bicep/deploy-cli.md).

## <a name="lock-resource-groups"></a>Bloqueo de grupos de recursos

Los bloqueos impiden que otros usuarios de la organización eliminen o modifiquen de forma accidental recursos críticos.

Para evitar que se eliminen un grupo de recursos y sus recursos, use [az lock create](/cli/azure/lock#az_lock_create).

```azurecli-interactive
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleGroup
```

Para obtener los bloqueos para un grupo de recursos, use [az lock list](/cli/azure/lock#az_lock_list).

```azurecli-interactive
az lock list --resource-group exampleGroup
```

Para eliminar un bloqueo, use [az lock delete](/cli/azure/lock#az_lock_delete).

```azurecli-interactive
az lock delete --name exampleLock --resource-group exampleGroup
```

Para obtener más información, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](lock-resources.md).

## <a name="tag-resource-groups"></a>Etiquetado de grupos de recursos

Puede aplicar etiquetas a los recursos y grupos de recursos para organizar de manera lógica los recursos. Para obtener información, vea [Uso de etiquetas para organizar los recursos de Azure](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exportación de grupos de recursos a plantillas

Para ayudar con la creación de plantillas de ARM, puede exportar una plantilla desde los recursos existentes. Para más información, consulte [Uso de la CLI de Azure para exportar una plantilla](../templates/export-template-cli.md). 

## <a name="manage-access-to-resource-groups"></a>Administración del acceso a los grupos de recursos

Para administrar el acceso a un grupo de recursos, use el [control de acceso basado en roles de Azure (RBAC de Azure)](../../role-based-access-control/overview.md). Para más información, consulte [Incorporación o eliminación de asignaciones de roles de Azure mediante la CLI de Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre Azure Resource Manager, consulte [Información general de Azure Resource Manager](overview.md).
- Para obtener información sobre la sintaxis de las plantillas de Resource Manager, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](../templates/syntax.md).