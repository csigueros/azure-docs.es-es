---
title: Creación de una plantilla de solución de problemas
description: Descripción de cómo crear una plantilla para solucionar los problemas de un recurso de Azure implementado con plantillas de Azure Resource Manager (plantillas de ARM) o archivos Bicep.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: 0c9b1a6ebd35a6ebe58b568a1a56e44c0395b630
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478105"
---
# <a name="create-a-troubleshooting-template"></a>Creación de una plantilla de solución de problemas

En algunos casos, la mejor manera de solucionar los problemas de la plantilla consiste en aislar y probar partes específicas de ella. Puede crear una plantilla de solución de problemas que se centre en el recurso que cree que provoca el error.

Por ejemplo, se produce un error cuando la plantilla de implementación hace referencia a un recurso existente. En lugar de evaluar una plantilla de implementación al completo, cree una plantilla de solución de problemas que devuelva datos sobre el recurso. La salida le ayudará a determinar si está pasando los parámetros adecuados, si usa las funciones de plantilla correctamente y si obtiene el recurso esperado.

## <a name="deploy-a-troubleshooting-template"></a>Implementación de una plantilla de solución de problemas

La siguiente plantilla de ARM y archivo Bicep reciben información de una cuenta de almacenamiento existente. Ejecute la implementación con [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) de Azure PowerShell o [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) de la CLI de Azure. Especifique el nombre y el grupo de recursos de la cuenta de almacenamiento. La salida es un objeto con los nombres de propiedad y los valores de la cuenta de almacenamiento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    },
    "storageResourceGroup": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-04-01')]",
      "type": "object"
    }
  }
}
```

En Bicep, use la palabra clave `existing` y ejecute la implementación desde el grupo de recursos donde existe la cuenta de almacenamiento. Use `scope` para acceder a un recurso de un grupo de recursos diferente. Para obtener más información, consulte los [recursos existentes](../bicep/resource-declaration.md#existing-resources).

```bicep
param storageName string

resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: storageName
}

output exampleOutput object = stg.properties
```

## <a name="alternate-troubleshooting-method"></a>Método alternativo de solución de problemas

Si cree que los errores de implementación están provocados por dependencias incorrectas, puede ejecutar pruebas dividiendo la plantilla en plantillas simplificadas. En primer lugar, cree una plantilla que implemente solo un único recurso (por ejemplo, un servidor SQL Server). Cuando esté seguro de que la implementación del recurso es correcta, agregue un recurso que dependa de él (como una base de datos SQL). Cuando esos dos recursos se definan correctamente, agregue otros recursos dependientes (por ejemplo, directivas de auditoría). Entre cada implementación de prueba, elimine el grupo de recursos para asegurarse de que se prueban adecuadamente las dependencias.

## <a name="next-steps"></a>Pasos siguientes

- [Errores de implementación frecuentes](common-deployment-errors.md)
- [Búsqueda de códigos de error](find-error-code.md)
- [Habilitación del registro de depuración](enable-debug-logging.md)
