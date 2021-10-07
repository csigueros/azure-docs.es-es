---
title: 'Funciones de plantilla: implementación'
description: Se describen las funciones que se pueden usar en una plantilla de Azure Resource Manager para recuperar información de implementación.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 12f449cd70f0ccbeeb0d232299f38fb814a8cb24
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733323"
---
# <a name="deployment-functions-for-arm-templates"></a>Funciones de implementación para plantillas de ARM

Resource Manager proporciona las siguientes funciones para obtener los valores relacionados con la implementación actual de la plantilla de Azure Resource Manager:

* [deployment](#deployment)
* [environment](#environment)
* [parameters](#parameters)
* [variables](#variables)

Para obtener valores de recursos, grupos de recursos o suscripciones, consulte [Funciones de recursos](template-functions-resource.md).

## <a name="deployment"></a>implementación

`deployment()`

Devuelve información sobre la operación de implementación actual.

### <a name="return-value"></a>Valor devuelto

Esta función devuelve el objeto pasado durante la implementación. Las propiedades del objeto devuelto difieren en función de si:

* se implementa una plantilla o una especificación de plantilla.
* se implementa una plantilla que es un archivo local o una plantilla que es un archivo remoto al que se accede a través de un identificador URI.
* se implementa en un grupo de recursos o en uno de los otros ámbitos ([suscripción a Azure](deploy-to-subscription.md), [grupo de administración](deploy-to-management-group.md) o [inquilino](deploy-to-tenant.md)).

Al implementar una plantilla local en un grupo de recursos; la función devuelve el siguiente formato:

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Al implementar una plantilla remota en un grupo de recursos; la función devuelve el siguiente formato:

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "uri": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Al implementar una especificación de plantilla en un grupo de recursos, la función devuelve el siguiente formato:

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "id": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Al implementar en una suscripción a Azure, en un grupo de recursos o en un inquilino, el objeto devuelto incluye una propiedad `location`. La propiedad de ubicación se incluye al implementar una plantilla local o externa. El formato es:

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="remarks"></a>Observaciones

Puede usar `deployment()` para establecer un vínculo con otra plantilla basada en el identificador URI de la plantilla primaria.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Si vuelve a implementar una plantilla desde el historial de implementación en el portal, la plantilla se implementará como un archivo local. La propiedad `templateLink` no se devuelve en la función de la implementación. Si la plantilla se basa en `templateLink` para construir un vínculo con otra plantilla, no use el portal para volver a implementarla. En su lugar, use los comandos que utilizó originalmente para implementar la plantilla.

### <a name="example"></a>Ejemplo

El ejemplo siguiente devuelve un objeto de implementación.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deployment.json":::

El ejemplo anterior devuelve el objeto siguiente:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Para una implementación de la suscripción, en el ejemplo siguiente se devuelve un objeto de implementación.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deploymentsubscription.json":::

## <a name="environment"></a>Environment

`environment()`

Devuelve información sobre el entorno de Azure que se usa para la implementación.

### <a name="return-value"></a>Valor devuelto

Esta función devuelve las propiedades del entorno de Azure actual. En el ejemplo siguiente se muestran las propiedades de Azure global. Las nubes soberanas pueden devolver propiedades ligeramente distintas.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Ejemplo

La plantilla de ejemplo siguiente devuelve el objeto de entorno.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/environment.json":::

En el ejemplo anterior se devuelve el objeto siguiente cuando se implementa en Azure global:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parámetros

`parameters(parameterName)`

Devuelve un valor de parámetro. El nombre del parámetro especificado debe definirse en la sección de parámetros de la plantilla.

En Bicep, haga referencia directamente a los parámetros mediante sus nombres simbólicos.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| parameterName |Sí |string |El nombre del parámetro que se va a devolver. |

### <a name="return-value"></a>Valor devuelto

Valor del parámetro especificado.

### <a name="remarks"></a>Observaciones

Por lo general, se usan parámetros para establecer los valores de recurso. En el ejemplo siguiente se establece el nombre del sitio web en el valor del parámetro pasado durante la implementación.

```json
"parameters": {
  "siteName": {
    "type": "string"
  }
}, "resources": [
  {
    "type": "Microsoft.Web/Sites",
    "apiVersion": "2016-08-01",
    "name": "[parameters('siteName')]",
    ...
  }
]
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un uso simplificado de la función de los parámetros.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/parameters.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| stringOutput | String | opción 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | opción 1 |

Para más información sobre el uso de los parámetros, consulte [Parámetros en plantillas de Resource Manager](./parameters.md).

## <a name="variables"></a>variables

`variables(variableName)`

Devuelve el valor de variable. El nombre de la variable especificada debe definirse en la sección de variables de la plantilla.

En Bicep, haga referencia directamente a variables mediante sus nombres simbólicos.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| variableName |Sí |String |El nombre de la variable que se va a devolver. |

### <a name="return-value"></a>Valor devuelto

Valor de la variable especificada.

### <a name="remarks"></a>Observaciones

Por lo general, para simplificar la plantilla se usan variables para crear valores complejos de una sola vez. En el ejemplo siguiente se crea un nombre único para una cuenta de almacenamiento.

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "dependsOn": [
      "[variables('storageName')]"
    ],
    ...
  }
],

```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se devuelven distintos valores de variable.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/variables.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

Para más información sobre el uso de las variables, consulte [Variables en plantillas de Resource Manager](./variables.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una descripción de las secciones de una plantilla de ARM, vea [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](./syntax.md).
