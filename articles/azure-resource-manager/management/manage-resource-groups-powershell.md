---
title: 'Administración de grupos de recursos: Azure PowerShell'
description: Use Azure PowerShell para administrar grupos de recursos con Azure Resource Manager. Muestra cómo crear, enumerar y eliminar grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c3bb028186155cc3af47f8efb293b7dbe61e13c9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731044"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Administración de grupos de recursos de Azure Resource Manager mediante Azure PowerShell

Obtenga información sobre cómo utilizar Azure PowerShell con [Azure Resource Manager](overview.md) para administrar los grupos de recursos de Azure. Para administrar recursos de Azure, vea [Administrar recursos de Azure mediante Azure PowerShell](manage-resources-powershell.md).

Otros artículos sobre cómo administrar grupos de recursos:

- [Administración de grupos de recursos de Azure con Azure Portal](manage-resources-portal.md)
- [Administrar grupos de recursos de Azure Resource Manager mediante la CLI de Azure](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>¿Qué es un grupo de recursos?

Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. El grupo de recursos puede incluir todos los recursos de la solución o solo aquellos que se desean administrar como grupo. Para decidir cómo agregar los recursos a los grupos de recursos, tenga en cuenta lo que más conviene a su organización. Por lo general, se recomienda agregar recursos que compartan el mismo ciclo de vida al mismo grupo de recursos para que los pueda implementar, actualizar y eliminar con facilidad como un grupo.

Los grupos de recursos almacenan metadatos acerca de los recursos. Al especificar la ubicación del grupo de recursos, se especifica el lugar en que dichos metadatos se almacenan. Por motivos de compatibilidad, es posible que sea preciso asegurarse de que los datos se almacenan en una región concreta.

## <a name="create-resource-groups"></a>Crear grupos de recursos

Para crear un grupo de recursos, use [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name exampleGroup -Location westus
```

## <a name="list-resource-groups"></a>Enumeración de grupos de recursos

Para obtener una lista de grupos de recursos de la suscripción, use [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell-interactive
Get-AzResourceGroup
```

Para obtener un grupo de recursos, proporcione el nombre del grupo de recursos.

```azurepowershell-interactive
Get-AzResourceGroup -Name exampleGroup
```

## <a name="delete-resource-groups"></a>Eliminación de grupos de recursos

Para quitar un grupo de recursos, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name exampleGroup
```

Para obtener más información sobre cómo ordena Azure Resource Manager la eliminación de recursos, consulte [Eliminación del grupo de recursos en Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources"></a>Implementación de recursos

Puede implementar recursos de Azure usando Azure PowerShell o implementando una plantilla de Azure Resource Manager (ARM) o un archivo Bicep.

En el ejemplo siguiente se crea una cuenta de almacenamiento. El nombre que proporcione para la cuenta de almacenamiento debe ser único en Azure.

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName exampleGroup -Name examplestore -Location westus -SkuName "Standard_LRS"
```

Para implementar una plantilla de ARM o un archivo Bicep, use [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile storage.bicep
```

Para obtener más información sobre la implementación de una plantilla de ARM, consulte [Implementación de recursos con las plantillas de ARM y Azure PowerShell](../templates/deploy-powershell.md).

Para obtener más información sobre la implementación de un archivo Bicep, vea [Implementación de recursos con Bicep y Azure PowerShell](../bicep/deploy-powershell.md).

## <a name="lock-resource-groups"></a>Bloqueo de grupos de recursos

Los bloqueos impiden que otros usuarios de la organización eliminen o modifiquen de forma accidental recursos críticos. 

Para evitar que se eliminen un grupo de recursos y sus recursos, use [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleGroup
```

Para obtener los bloqueos para un grupo de recursos, use [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock).

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleGroup
```

Para obtener más información, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](lock-resources.md).

## <a name="tag-resource-groups"></a>Etiquetado de grupos de recursos

Puede aplicar etiquetas a los recursos y grupos de recursos para organizar de manera lógica los recursos. Para obtener información, vea [Uso de etiquetas para organizar los recursos de Azure](tag-resources.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exportación de grupos de recursos a plantillas

Para ayudar con la creación de plantillas de ARM, puede exportar una plantilla desde los recursos existentes. Para más información, consulte [Uso de Azure PowerShell para exportar una plantilla](../templates/export-template-powershell.md). 

## <a name="manage-access-to-resource-groups"></a>Administración del acceso a los grupos de recursos

El [control de acceso basado en rol (RBAC) de Azure](../../role-based-access-control/overview.md) es la forma en la que se administra el acceso a los recursos de Azure. Para más información, consulte [Incorporación o eliminación de asignaciones de roles de Azure mediante Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre Azure Resource Manager, consulte [Información general de Azure Resource Manager](overview.md).
- Para obtener información sobre la sintaxis de las plantillas de Resource Manager, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](../templates/syntax.md).
