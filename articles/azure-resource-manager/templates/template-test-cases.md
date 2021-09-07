---
title: Casos de prueba del kit de herramientas para pruebas de plantillas
description: Describe las pruebas de plantillas que ejecuta el kit de herramientas para pruebas de plantillas de Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 961a62ae45b423e2e4c2abf8a4e7d771c2775591
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744902"
---
# <a name="test-cases-for-arm-templates"></a>Casos de prueba para plantillas de ARM

En este artículo se describen las pruebas que se ejecutan con el [kit de herramientas de pruebas de plantillas](test-toolkit.md) para las plantillas de Azure Resource Manager (plantillas de ARM). Ofrece ejemplos que ilustran la **superación** y **no superación** de las pruebas, e incluye el nombre de cada prueba. Para más información sobre cómo ejecutar las pruebas o sobre la ejecución de una prueba específica, consulte [Parámetros de prueba](test-toolkit.md#test-parameters).

## <a name="use-correct-schema"></a>Uso del esquema correcto

Nombre de la prueba: **Esquema DeploymentTemplate correcto**

En la plantilla, debe especificar un valor de esquema válido.

En el ejemplo siguiente **no se supera** la prueba porque el esquema no es válido.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-01-01/deploymentTemplate.json#",
}
```

En el ejemplo siguiente se muestra una **advertencia** porque la versión del esquema `2015-01-01` está en desuso y no se mantiene.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
}
```

En el ejemplo siguiente **se supera** mediante un esquema válido.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
}
```

La propiedad `schema` de la plantilla debe establecerse en uno de los esquemas siguientes:

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="declared-parameters-must-be-used"></a>Los parámetros declarados deben usarse

Nombre de la prueba: **Debe hacerse referencia a los parámetros**

Esta prueba busca parámetros que no se usan en la plantilla o parámetros que no se usan en una expresión válida.

Para reducir la confusión en la plantilla, elimine los parámetros definidos, pero que no se usan. La eliminación de parámetros sin usar simplifica las implementaciones de plantillas, ya que no hay que proporcionar valores innecesarios.

En el ejemplo siguiente **no se supera** la prueba porque a la expresión que hace referencia a un parámetro le falta el corchete inicial (`[`).

```json
"resources": [
  {
    "location": " parameters('location')]"
  }
]
```

En el ejemplo siguiente **se supera** la prueba porque la expresión es válida.

```json
"resources": [
  {
    "location": "[parameters('location')]"
  }
]
```

## <a name="secure-parameters-cant-have-hard-coded-default"></a>Los parámetros protegidos no pueden tener un valor predeterminado codificado de forma rígida

Nombre de la prueba: **Los parámetros de cadena segura no pueden tener valores predeterminados**

No proporcione un valor predeterminado codificado de forma rígida para un parámetro seguro en la plantilla. Un parámetro protegido puede tener una cadena vacía como valor predeterminado o usar la función [newGuid](template-functions-string.md#newguid) en una expresión.

Use los tipos `secureString` o `secureObject` en los parámetros que contienen valores confidenciales, como las contraseñas. Cuando un parámetro usa un tipo seguro, el valor del parámetro no se registra ni se almacena en el historial de implementación. Esta acción evita que un usuario malintencionado detecte el valor confidencial.

Cuando se proporciona un valor predeterminado, cualquiera que pueda acceder a la plantilla o al historial de implementación puede detectarlo.

El ejemplo siguiente **no supera** la prueba.

```json
"parameters": {
  "adminPassword": {
    "defaultValue": "HardcodedPassword",
    "type": "secureString"
  }
}
```

En el ejemplo siguiente **se supera** la prueba.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
```

En el ejemplo siguiente **se supera** la prueba porque se usa la función `newGuid`.

```json
"parameters": {
  "secureParameter": {
    "type": "secureString",
    "defaultValue": "[newGuid()]"
  }
}
```

## <a name="environment-urls-cant-be-hard-coded"></a>Las direcciones URL de entorno no pueden estar codificadas de forma rígida

Nombre de la prueba: **DeploymentTemplate no debe contener un URI codificado de forma rígida**

No codifique de forma rígida las direcciones URL de entorno en la plantilla. En su lugar, use la función [environment](template-functions-deployment.md#environment) para obtener estas direcciones URL de forma dinámica durante la implementación. Para obtener una lista de los hosts de URL que están bloqueados, consulte el [caso de prueba](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

En el siguiente ejemplo **no se supera** la prueba porque la URL está codificada de forma rígida.

```json
"variables":{
  "AzureURL":"https://management.azure.com"
}
```

La prueba también **genera un error** cuando se usa con [concat](template-functions-string.md#concat) o [uri](template-functions-string.md#uri).

```json
"variables":{
  "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
  "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

El ejemplo siguiente **supera** la prueba.

```json
"variables": {
  "AzureSchemaURL": "[environment().gallery]"
}
```

## <a name="location-uses-parameter"></a>La ubicación usa el parámetro

Nombre de la prueba: **La ubicación no debe estar codificada de forma rígida**

Para establecer la ubicación de un recurso, las plantillas deben tener un parámetro denominado `location` con el tipo establecido en `string`. En la plantilla principal, _azuredeploy.json_ o _mainTemplate.json_, este parámetro puede tener como valor predeterminado la ubicación del grupo de recursos. En las plantillas vinculadas o anidadas, el parámetro location no debe tener una ubicación predeterminada.

Los usuarios de plantilla pueden tener acceso limitado a las regiones donde pueden crear recursos. Una ubicación de recursos codificada de forma segura podría impedir que los usuarios creen un recurso. La expresión `"[resourceGroup().location]"` podría bloquear a los usuarios si el grupo de recursos se creó en una región a la que el usuario no puede acceder. Los usuarios bloqueados no pueden usar la plantilla.

Al proporcionar un parámetro `location` cuyo valor predeterminado es la ubicación del grupo de recursos, los usuarios pueden usar el valor predeterminado cuando sea conveniente, pero también especificar otra ubicación.

En el siguiente ejemplo **no se supera** la prueba porque el parámetro `location` del recurso está establecido en `resourceGroup().location`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

En el ejemplo siguiente se usa un parámetro `location`, pero **no se supera** la prueba porque el parámetro tiene como valor predeterminado una ubicación codificada de forma rígida.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "westus"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

En el ejemplo siguiente, **se supera** la prueba cuando se usa la plantilla como principal. Cree un parámetro que tenga como valor predeterminado la ubicación del grupo de recursos, pero que permita a los usuarios proporcionar otro valor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

> [!NOTE]
> Si el ejemplo anterior se usa como una plantilla vinculada, la prueba **no se supera**. Cuando se usa como plantilla vinculada, quite el valor predeterminado.

## <a name="resources-should-have-location"></a>Los recursos deben tener ubicación

Nombre de la prueba: **Los recursos deben tener ubicación**

La ubicación de un recurso debe establecerse en una [expresión de plantilla](template-expressions.md) o en `global`. La expresión de plantilla usaría normalmente el parámetro `location` descrito en [La ubicación usa el parámetro](#location-uses-parameter).

En el siguiente ejemplo **no se supera** la prueba porque `global` no es una expresión ni `location`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "westus",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

En el siguiente ejemplo **se supera** la prueba porque `global` está establecido en `location`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "global",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}

```

En el ejemplo siguiente también **se supera** la prueba porque el parámetro `location` usa una expresión. El recurso `location` usa el valor de la expresión.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>El tamaño de la máquina virtual usa un parámetro

Nombre de la prueba: **El tamaño de la máquina virtual debe ser un parámetro**

No codifique de forma rígida `vmSize` del objeto `hardwareProfile`. La prueba no se supera cuando `hardwareProfile` se omite o contiene un valor codificado de forma rígida. Proporcione un parámetro para que los usuarios de la plantilla puedan modificar el tamaño de la máquina virtual que implementan. Para más información, consulte [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines).

En el siguiente ejemplo **no se supera** la prueba porque `vmSize` del objeto `hardwareProfile` está codificado de forma rígida.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_D2_v3"
      }
    }
  }
]
```

En el ejemplo **se supera** la prueba cuando un parámetro especifica un valor para `vmSize`:

```json
"parameters": {
  "vmSizeParameter": {
    "type": "string",
    "defaultValue": "Standard_D2_v3",
    "metadata": {
      "description": "Size for the virtual machine."
    }
  }
}
```

Luego, `hardwareProfile` usa una expresión para `vmSize` para hacer referencia al valor del parámetro:

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "[parameters('vmSizeParameter')]"
      }
    }
  }
]
```

## <a name="min-and-max-values-are-numbers"></a>Los valores mínimo y máximo son números

Nombre de la prueba: **Los valores mínimo y máximo son números**

Cuando define un parámetro con `minValue` y `maxValue`, especifíquelos como números. Debe usar `minValue` y `maxValue` como par o no se superará la prueba.

En el ejemplo siguiente **no se supera** la prueba porque `minValue` y `maxValue` son cadenas.

```json
"exampleParameter": {
  "type": "int",
  "minValue": "0",
  "maxValue": "10"
}
```

En el ejemplo siguiente **no se supera** la prueba porque solo se usa `minValue`.

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0
}
```

En el ejemplo siguiente **se supera** la prueba porque `minValue` y `maxValue` son números.

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0,
  "maxValue": 10
}
```

## <a name="artifacts-parameter-defined-correctly"></a>Parámetro de artefactos definido correctamente

Nombre de la prueba : **parámetro de artefactos**

Cuando incluya parámetros para `_artifactsLocation` y `_artifactsLocationSasToken`, utilice los valores predeterminados y los tipos correctos. Deben cumplirse las condiciones siguientes para superar la prueba:

- Si indica uno de los parámetros, tiene que indicar el otro.
- `_artifactsLocation` debe ser `string`.
- `_artifactsLocation` debe tener un valor predeterminado en la plantilla principal.
- `_artifactsLocation` no puede tener un valor predeterminado en una plantilla anidada.
- `_artifactsLocation` debe tener `"[deployment().properties.templateLink.uri]"` o la dirección URL del repositorio sin formato como valor predeterminado.
- `_artifactsLocationSasToken` debe ser `secureString`.
- `_artifactsLocationSasToken` solo puede tener una cadena vacía como valor predeterminado.
- `_artifactsLocationSasToken` no puede tener un valor predeterminado en una plantilla anidada.

## <a name="declared-variables-must-be-used"></a>Se deben usar variables declaradas

Nombre de la prueba: **Debe hacerse referencia a las variables**

Esta prueba busca variables que no se usan en la plantilla o que no se usan en una expresión válida. Para reducir la confusión en la plantilla, elimine las variables definidas, pero que no se utilicen.

Se debe hacer referencia a las variables que usan el elemento `copy` para iterar valores. Para más información, consulte [Iteración de variables en las plantillas de ARM](copy-variables.md).

En el ejemplo siguiente **no se supera** la prueba porque no se hace referencia a la variable que usa el elemento `copy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {}
}
```

En el ejemplo siguiente **no se supera** la prueba porque a la expresión que hace referencia a una variable le falta el corchete inicial (`[`).

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": " variables('varExample')]"
  }
}
```

En el ejemplo siguiente **se supera** la prueba porque se hace referencia a la variable en `outputs`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

En el ejemplo siguiente **se supera** la prueba porque la expresión es válida.

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": "[variables('varExample')]"
  }
}
```

## <a name="dynamic-variable-should-not-use-concat"></a>La variable dinámica no debe utilizar concat

Nombre de la prueba: **Las referencias de variables dinámicas no deben usar concat**

A veces es necesario crear dinámicamente una variable que se base en el valor de otra variable u otro parámetro. No utilice la función [concat](template-functions-string.md#concat) al establecer el valor. En su lugar, use un objeto que incluya las opciones disponibles y obtenga dinámicamente una de las propiedades del objeto durante la implementación.

El ejemplo siguiente **supera** la prueba. La variable `currentImage` se establece dinámicamente durante la implementación.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "osType": {
      "type": "string",
      "allowedValues": [
        "Windows",
        "Linux"
      ]
    }
  },
  "variables": {
    "imageOS": {
      "Windows": {
        "image": "Windows Image"
      },
      "Linux": {
        "image": "Linux Image"
      }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[variables('currentImage')]"
    }
  }
}
```

## <a name="use-recent-api-version"></a>Uso de la versión de API reciente

Nombre de la prueba: **apiVersions debe ser reciente**

La versión de API de cada recurso debe usar una versión reciente que esté codificada de forma rígida como cadena. La prueba evalúa la versión de API de la plantilla con respecto a las versiones del proveedor de recursos en la caché del kit de herramientas. Una versión de API con una antigüedad de menos de dos a partir de la fecha de ejecución de la prueba se considera reciente. No use una versión preliminar cuando haya disponible una versión más reciente.

Una advertencia de que no se ha encontrado una versión de API solo indica que la versión no está incluida en la caché del kit de herramientas. El uso de la versión más reciente de una API, lo que se recomienda, puede generar la advertencia.

Obtenga más información sobre la [caché del kit de herramientas](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache).

En el ejemplo siguiente **no se supera** la prueba porque la versión de API tiene más de dos años.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

En el ejemplo siguiente **no se supera** la prueba porque se usa una versión preliminar cuando hay disponible una versión más reciente.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2020-08-01-preview",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

En el ejemplo siguiente **se supera** la prueba porque es una versión reciente que no es preliminar.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-02-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

## <a name="use-hard-coded-api-version"></a>Uso de la versión de API codificada de forma rígida

Nombre de la prueba: **No está permitido el uso de apiVersions de proveedores**

La versión de API para un tipo de recurso determina las propiedades que están disponibles. Proporcione una versión de API codificada de forma rígida en la plantilla. No recupere una versión de API que se determine durante la implementación porque no sabrá qué propiedades están disponibles.

El ejemplo siguiente **no supera** la prueba.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
    ...
  }
]
```

El ejemplo siguiente **supera** la prueba.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    ...
  }
]
```

## <a name="properties-cant-be-empty"></a>Las propiedades no pueden estar vacías

Nombre de la prueba: **La plantilla no debe contener espacios en blanco**

No codifique propiedades de forma rígida en un valor vacío. Los valores vacíos incluyen cadenas, objetos o matrices NULL y vacíos. Si una propiedad está establecida en un valor vacío, quite esa propiedad de la plantilla. Puede establecer una propiedad en un valor vacío durante la implementación, por ejemplo, a través de un parámetro.

La propiedad `template` de una [plantilla anidada](linked-templates.md#nested-template) puede incluir propiedades vacías. Para más información sobre las plantillas anidadas, consulte [Implementaciones de Microsoft.Resources](/azure/templates/microsoft.resources/deployments).

En el ejemplo siguiente **no se supera** la prueba porque hay propiedades vacías.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {},
    "kind": ""
  }
]
```

En el ejemplo siguiente **se supera** la prueba porque las propiedades incluyen valores.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {
      "name": "Standard_LRS",
      "tier": "Standard"
    },
    "kind": "Storage"
  }
]
```

## <a name="use-resource-id-functions"></a>Uso de funciones de identificador de recurso

Nombre de la prueba: **Los identificadores se deben derivar de ResourceId**

Al especificar un identificador de recurso, utilice una de las funciones de identificador de recurso. Las funciones permitidas son las siguientes:

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)

No utilice la función concat para crear un identificador de recurso. El ejemplo siguiente **no supera** la prueba.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

En el ejemplo siguiente **se supera** la prueba.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>La función ResourceId tiene parámetros correctos

Nombre de la prueba: **ResourceId no debe contener**

Al generar identificadores de recursos, no use funciones innecesarias para parámetros opcionales. De forma predeterminada, la función [ResourceId](template-functions-resource.md#resourceid) usa la suscripción y el grupo de recursos actuales. No es necesario proporcionar esos valores.

En el siguiente ejemplo **no se supera** la prueba, porque no es necesario proporcionar el identificador de suscripción y el nombre del grupo de recursos actuales.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

En el ejemplo siguiente **se supera** la prueba.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Esta prueba se aplica a:

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)
- [reference](template-functions-resource.md#reference)
- [list*](template-functions-resource.md#list)

En `reference` y `list*`, la prueba **no se supera** cuando se usa `concat` para crear el identificador de recurso.

## <a name="dependson-best-practices"></a>Procedimientos recomendados de dependsOn

Nombre de la prueba: **Procedimientos recomendados de dependsOn**

Al establecer las dependencias de implementación, no use la función [si](template-functions-logical.md#if) para probar una condición. Si un recurso depende de un recurso que se [implementó condicionalmente](conditional-resource-deployment.md), establezca la dependencia como lo haría con cualquier recurso. Cuando un recurso condicional no está implementado, Azure Resource Manager lo quita automáticamente de las dependencias necesarias.

El elemento `dependsOn` no puede comenzar con una función [concat](template-functions-array.md#concat).

En el ejemplo siguiente **no se supera** la prueba porque contiene una función `if`.

```json
"dependsOn": [
  "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

En el ejemplo siguiente **no se supera** la prueba porque comienza por `concat`.

```json
"dependsOn": [
  "[concat(variables('storageAccountName'))]"
]
```

El ejemplo siguiente **supera** la prueba.

```json
"dependsOn": [
  "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Las implementaciones anidadas o vinculadas no pueden utilizar depuración

Nombre de la prueba: **Los recursos de implementación no deben depurarse**

Al definir una [plantilla anidada o vinculada](linked-templates.md) con el tipo de recurso `Microsoft.Resources/deployments`, puede habilitar la [depuración](/azure/templates/microsoft.resources/deployments#debugsetting-object). La depuración se usa cuando tiene que probar una plantilla, pero puede exponer información confidencial. Antes de usar la plantilla en producción, desactive la depuración. Puede quitar el objeto `debugSetting` o cambiar la propiedad `detailLevel` a `none`.

El ejemplo siguiente **no supera** la prueba.

```json
"debugSetting": {
  "detailLevel": "requestContent"
}
```

El ejemplo siguiente **supera** la prueba.

```json
"debugSetting": {
  "detailLevel": "none"
}
```

## <a name="admin-user-names-cant-be-literal-value"></a>Los nombres de usuario administrador no pueden ser valores literales

Nombre de la prueba: **adminUsername no debe ser un literal**

Al establecer `adminUserName`, no utilice un valor literal. Cree un parámetro para el nombre de usuario y use una expresión para hacer referencia al valor del parámetro.

En el ejemplo siguiente **no se supera** la prueba con un valor literal.

```json
"osProfile":  {
  "adminUserName": "myAdmin"
}
```

En el ejemplo siguiente **se supera** la prueba con una expresión.

```json
"osProfile": {
  "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Uso de la imagen de máquina virtual más reciente

Nombre de la prueba: **Las imágenes de máquina virtual deben usar la versión más reciente**

Esta prueba está deshabilitada, pero la salida muestra que se superó. El procedimiento recomendado es comprobar los siguientes criterios en la plantilla:

Si la plantilla incluye una máquina virtual con una imagen, asegúrese de que está usando la versión más reciente de dicha imagen.

## <a name="use-stable-vm-images"></a>Uso de imágenes de máquina virtual estables

Nombre de la prueba: **Virtual Machines no debe ser una versión preliminar**

Las máquinas virtuales no deben usar imágenes de vista previa. La prueba comprueba `storageProfile` para verificar que `imageReference` no usa una cadena que contiene _preview_, y que _preview_ no se use en las propiedades `offer`, `sku` ni `version` de `imageReference`.

Para más información sobre la propiedad `imageReference`, consulte [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines#imagereference-object) y [Microsoft.Compute virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets#imagereference-object).

En el ejemplo siguiente **no se supera** la prueba porque `imageReference` es una cadena que contiene _preview_.

```json
"properties": {
  "storageProfile": {
    "imageReference": "latest-preview"
  }
}
```

En el ejemplo siguiente **no se supera** la prueba cuando _preview_ se usa en `offer`, `sku` o `version`.

```json
"properties": {
  "storageProfile": {
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer_preview",
      "sku": "16.04-LTS-preview",
      "version": "preview"
    }
  }
}
```

El ejemplo siguiente **supera** la prueba.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
  }
}
```

## <a name="dont-use-managedidentity-extension"></a>No use la extensión ManagedIdentity

Nombre de la prueba: **No se debe usar ManagedIdentityExtension**

No aplique la extensión `ManagedIdentity` a una máquina virtual. La extensión quedó en desuso en 2019 y ya no debe usarse.

## <a name="outputs-cant-include-secrets"></a>Las salidas no pueden incluir secretos

Nombre de la prueba: **Las salidas no deben contener secretos**

No incluya ningún valor en la sección `outputs` que pueda exponer secretos. Por ejemplo, los parámetros protegidos de tipo `secureString` o `secureObject`, o funciones [list*](template-functions-resource.md#list), como `listKeys`.

La salida de una plantilla se almacena en el historial de implementación, por lo que un usuario malintencionado podría encontrar esa información.

En el siguiente ejemplo **no se supera** la prueba porque incluye un parámetro protegido en un valor de salida.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secureParam": {
      "type": "secureString"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "string",
      "value": "[concat('this is the value ', parameters('secureParam'))]"
    }
  }
}
```

En el siguiente ejemplo **no se supera** porque utiliza una función [list*](template-functions-resource.md#list) en las salidas.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "object",
      "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
    }
  }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>Uso de protectedSettings para secretos de commandToExecute

Nombre de la prueba: **CommandToExecute debe usar ProtectedSettings para secretos**

Para los recursos de tipo `CustomScript`, use `protectedSettings` cifrada cuando `commandToExecute` incluya datos secretos, como una contraseña. Por ejemplo, los datos secretos se pueden usar en parámetros protegidos de tipo `secureString` o `secureObject`, funciones [list*](template-functions-resource.md#list), como `listKeys`, o scripts personalizados.

No use datos secretos en el objeto `settings` porque usa texto sin formato. Para más información, consulte [Microsoft.Compute virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions), [Windows](
/azure/virtual-machines/extensions/custom-script-windows) o [Linux](../../virtual-machines/extensions/custom-script-linux.md).

En el ejemplo siguiente **no se supera** la prueba porque `settings` usa `commandToExecute` con un parámetro protegido.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

En el ejemplo siguiente **no se supera** la prueba porque `settings` usa `commandToExecute` con una función `listKeys`.

```json
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

En el ejemplo siguiente **se supera** la prueba porque `protectedSettings` usa `commandToExecute` con un parámetro protegido.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

En el ejemplo siguiente **se supera** la prueba porque `protectedSettings` usa `commandToExecute` con una función `listKeys`.

```json
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

## <a name="use-recent-api-versions-in-reference-functions"></a>Uso de versiones de API recientes en funciones de referencia

Nombre de la prueba: **apiVersions debe ser reciente en las funciones reference**

La versión de API usada en una función [reference](template-functions-resource.md#reference) debe ser reciente y no una versión preliminar. La prueba evalúa la versión de API de la plantilla con respecto a las versiones del proveedor de recursos en la caché del kit de herramientas. Una versión de API con una antigüedad de menos de dos a partir de la fecha de ejecución de la prueba se considera reciente.

Una advertencia de que no se ha encontrado una versión de API solo indica que la versión no está incluida en la caché del kit de herramientas. El uso de la versión más reciente de una API, lo que se recomienda, puede generar la advertencia.

Obtenga más información sobre la [caché del kit de herramientas](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache).

En el ejemplo siguiente **no se supera** la prueba porque la versión de API tiene más de dos años.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01')]"
  }
}
```

En el ejemplo siguiente **no se supera** la prueba porque la versión de API es preliminar.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2020-08-01-preview')]"
  }
}
```

En el ejemplo siguiente **se supera** la prueba porque la versión de API tiene menos de dos años de antigüedad y no es una versión preliminar.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-02-01')]"
  }
}
```

## <a name="use-type-and-name-in-resourceid-functions"></a>Uso del tipo y el nombre en las funciones resourceId

Nombre de la prueba: **Los recursos no deben ser ambiguos**

Esta prueba está deshabilitada, pero la salida muestra que se superó. El procedimiento recomendado es comprobar los siguientes criterios en la plantilla:

Una función [resourceId](template-functions-resource.md#resourceid) debe incluir un tipo de recurso y un nombre de recurso. Esta prueba busca todas las funciones `resourceId` de la plantilla y comprueba que el recurso se use en la plantilla con la sintaxis correcta. De lo contrario, la función se considera ambigua.

Por ejemplo, una función `resourceId` se considera ambigua:

- Cuando no se encuentra un recurso en la plantilla y no se especifica un grupo de recursos.
- Si un recurso incluye una condición y no se especifica un grupo de recursos.
- Si un recurso relacionado contiene algunos segmentos de nombre, pero no todos. Por ejemplo, un recurso secundario contiene más de un segmento de nombre. Para más información, consulte [Observaciones de resourceId](template-functions-resource.md#remarks-3).

## <a name="use-inner-scope-for-nested-deployment-secure-parameters"></a>Uso del ámbito interno para parámetros protegidos de implementaciones anidadas

Nombre de prueba: **Parámetros protegidos en implementaciones anidadas**

Use el objeto `expressionEvaluationOptions` de la plantilla anidada con ámbito `inner` para evaluar expresiones que contienen parámetros protegidos de tipo `secureString` o `secureObject`, o funciones [list*](template-functions-resource.md#list), como `listKeys`. Si se usa el ámbito `outer`, las expresiones se evalúan en texto sin formato dentro del ámbito de la plantilla primaria. A continuación, el valor seguro es visible para cualquier usuario con acceso al historial de implementación. El valor predeterminado de `expressionEvaluationOptions` es `outer`.

Para más información sobre las plantillas anidadas, consulte [Implementaciones de Microsoft.Resources](/azure/templates/microsoft.resources/deployments) y [Ámbito de evaluación de expresiones en plantillas anidadas](linked-templates.md#expression-evaluation-scope-in-nested-templates).

En el ejemplo siguiente **no se supera** la prueba porque `expressionEvaluationOptions` usa el ámbito `outer` para evaluar parámetros protegidos o funciones `list*`.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "outer"
      }
    }
  }
]
```

En el ejemplo siguiente **se supera** la prueba porque `expressionEvaluationOptions` usa el ámbito `inner` para evaluar parámetros protegidos o funciones `list*`.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "inner"
      }
    }
  }
]
```

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo ejecutar el kit de herramientas para pruebas, consulte [Uso del kit de herramientas para pruebas de plantillas de Resource Manager](test-toolkit.md).
- Para obtener un módulo de Microsoft Learn que trate del uso del kit de herramientas de pruebas, vea [Obtención de una vista previa de los cambios y validación de recursos de Azure mediante What If y el kit de herramientas de pruebas de plantillas de ARM](/learn/modules/arm-template-test/).
- Para probar los archivos de parámetros, consulte [Casos de prueba para archivos de parámetros](parameters.md).
- Para las pruebas de createUiDefinition, consulte [Casos de prueba para createUiDefinition.json](createUiDefinition-test-cases.md).
- Para información sobre las pruebas de todos los archivos, consulte [Casos de prueba para todos los archivos](all-files-test-cases.md).
