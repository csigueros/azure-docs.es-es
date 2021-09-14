---
title: Exportación de plantilla en Azure PowerShell
description: Use Azure PowerShell para exportar una plantilla de Azure Resource Manager desde los recursos de la suscripción.
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: 15a81b845811f870be783807cf680c724413bf6c
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479172"
---
# <a name="use-azure-powershell-to-export-a-template"></a>Uso de Azure PowerShell para exportar una plantilla

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

Este artículo muestra cómo exportar plantillas mediante **Azure PowerShell**. Para conocer otras opciones, consulte:

* [Exportación de plantilla con la CLI de Azure](export-template-cli.md)
* [Exportación de plantilla con Azure Portal](export-template-portal.md)
* [Exportación de API REST a partir de un grupo de recursos](/rest/api/resources/resourcegroups/exporttemplate) y [Exportación de API REST a partir del historial de implementación](/rest/api/resources/deployments/export-template).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>Exportación de la plantilla desde un grupo de recursos

Después de configurar el grupo de recursos, puede exportar una plantilla de Resource Manager para el grupo de recursos. 

Para exportar todos los recursos de un grupo de recursos, use el cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) y proporcione el nombre del grupo de recursos.

```azurepowershell-interactive
Export-AzResourceGroup -ResourceGroupName demoGroup
```

La plantilla se guarda como un archivo local.

En lugar de exportar todos los recursos del grupo de recursos, puede seleccionar los recursos que quiere exportar.

Para exportar un recurso, pase el identificador de ese recurso.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Para exportar más de un recurso, pase los identificadores de los recursos en una matriz.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Al exportar la plantilla, puede especificar si se usan parámetros en ella. De forma predeterminada, se incluyen parámetros para los nombres de recursos, pero no tienen un valor predeterminado.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

Si usa el parámetro `-SkipResourceNameParameterization` al exportar la plantilla, los parámetros de los nombres de recursos no se incluyen en la plantilla. En cambio, el nombre del recurso se establece directamente en el recurso en su valor actual. No se puede personalizar el nombre durante la implementación.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Si usa el parámetro `-IncludeParameterDefaultValue` al exportar la plantilla, el parámetro de la plantilla incluye un valor predeterminado que se establece en el valor actual. Puede usar ese valor predeterminado o pasar un valor diferente para sobrescribir el predeterminado.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

## <a name="save-template-from-deployment-history"></a>Guardado de una plantilla desde el historial de implementación

Puede guardar una plantilla de una implementación en el historial de implementación. La plantilla que se obtiene es exactamente la que se ha usado para la implementación. 

Para obtener una plantilla de una implementación de grupo de recursos, use el cmdlet [Save-AzResourceGroupDeploymentTemplate](/powershell/module/az.resources/save-azresourcegroupdeploymenttemplate). Especifique el nombre de la implementación que desea recuperar. Para obtener ayuda con el nombre de una implementación, consulte [Visualización del historial de implementación con Azure Resource Manager](deployment-history.md).

```azurepowershell-interactive
Save-AzResourceGroupDeploymentTemplate -ResourceGroupName demoGroup -DeploymentName demoDeployment
```

La plantilla se guarda como un archivo local con el nombre de la implementación.

Para implementar plantillas en otros niveles, utilice:

* [Save-AzDeploymentTemplate](/powershell/module/az.resources/save-azdeploymenttemplate) para implementaciones en suscripciones
* [Save-AzManagementGroupDeploymentTemplate](/powershell/module/az.resources/save-azmanagementgroupdeploymenttemplate) para implementaciones en grupos de administración
* [Save-AzTenantDeploymentTemplate](/powershell/module/az.resources/save-aztenantdeploymenttemplate) para implementaciones en inquilinos

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a exportar plantillas con la [CLI de Azure](export-template-cli.md), [Azure Portal](export-template-portal.md) o la [API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Para obtener información sobre la sintaxis de las plantillas de Resource Manager, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](./syntax.md).
- Para obtener información sobre cómo desarrollar plantillas, consulte los [tutoriales paso a paso](../index.yml).