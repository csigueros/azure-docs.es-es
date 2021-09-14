---
title: Exportación de plantilla en la CLI de Azure
description: Use la CLI de Azure para exportar una plantilla de Azure Resource Manager a partir de los recursos de su suscripción.
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: b60c7ae3787b2b22b09eb844a9206b9530fb68df
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480883"
---
# <a name="use-azure-cli-to-export-a-template"></a>Uso de la CLI de Azure para exportar una plantilla

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

Este artículo muestra cómo exportar plantillas a través de la **CLI de Azure**. Para otras alternativas, consulte los siguientes recursos:

* [Exportación de plantilla en Azure Portal](export-template-portal.md)
* [Exportación de plantilla en Azure PowerShell](export-template-powershell.md)
* [Exportación de API REST a partir de un grupo de recursos](/rest/api/resources/resourcegroups/exporttemplate) y [Exportación de API REST a partir del historial de implementación](/rest/api/resources/deployments/export-template).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>Exportación de la plantilla desde un grupo de recursos

Después de configurar el grupo de recursos correctamente, es posible exportar una plantilla de Resource Manager para el grupo de recursos.

Para exportar todos los recursos de un grupo de recursos, use [az group export](/cli/azure/group#az_group_export) y proporcione el nombre del grupo de recursos.

```azurecli-interactive
az group export --name demoGroup
```

El script muestra la plantilla en la consola. Para guardar la plantilla en un archivo, utilice:

```azurecli-interactive
az group export --name demoGroup > exportedtemplate.json
```

En lugar de exportar todos los recursos del grupo de recursos, puede seleccionar los recursos que quiere exportar.

Para exportar un recurso, pase el identificador de ese recurso.

```azurecli-interactive
storageAccountID=$(az resource show --resource-group demoGroup --name demostg --resource-type Microsoft.Storage/storageAccounts --query id --output tsv)
az group export --resource-group demoGroup --resource-ids $storageAccountID
```

Para exportar más de un recurso, pase los identificadores de recursos separados por espacios. Para exportar todos los recursos, no especifique este argumento ni especifique "*".

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccountID1 $storageAccountID2
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

Si usa el parámetro `--skip-resource-name-params` al exportar la plantilla, los parámetros de los nombres de recursos no se incluyen en la plantilla. En cambio, el nombre del recurso se establece directamente en el recurso en su valor actual. No se puede personalizar el nombre durante la implementación.

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

Si usa el parámetro `--include-parameter-default-value` al exportar la plantilla, el parámetro de la plantilla incluye un valor predeterminado que se establece en el valor actual. Puede usar ese valor predeterminado o pasar un valor diferente para sobrescribir el predeterminado.

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

Para obtener una plantilla a partir de la implementación de un grupo de recursos, utilice el comando [az deployment group export](/cli/azure/deployment/group#az_deployment_group_export). Especifique el nombre de la implementación que desea recuperar. Para obtener ayuda con el nombre de una implementación, consulte [Visualización del historial de implementación Azure Resource Manager](deployment-history.md).

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment 
```

La plantilla se muestra en la consola. Para guardar el archivo, utilice:

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment > demoDeployment.json
```

Para implementar plantillas en otros niveles, utilice:

* [az deployment sub export](/cli/azure/deployment/sub#az_deployment_sub_export) para implementaciones en suscripciones
* [az deployment mg export](/cli/azure/deployment/mg#az_deployment_mg_export) para implementaciones en grupo de administración
* [az deployment tenant export](/cli/azure/deployment/tenant#az_deployment_tenant_export) para implementaciones en inquilinos


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a exportar plantillas con [Azure Portal](export-template-portal.md), [Azure PowerShell](export-template-powershell.md) o [API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Para obtener información sobre la sintaxis de las plantillas de Resource Manager, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](./syntax.md).
- Para obtener información sobre cómo desarrollar plantillas, consulte los [tutoriales paso a paso](../index.yml).