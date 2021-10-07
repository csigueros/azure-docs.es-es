---
title: 'Funciones de Bicep: deployment'
description: Describe las funciones que se usarán en un archivo de Bicep para recuperar información de implementación.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: e909ea77da32675beec6d5117430717acf274ab1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731098"
---
# <a name="deployment-functions-for-bicep"></a>Funciones de implementación para Bicep

En este artículo se describen las funciones de Bicep para obtener valores relacionados con la implementación actual.

## <a name="deployment"></a>implementación

`deployment()`

Devuelve información sobre la operación de implementación actual.

### <a name="return-value"></a>Valor devuelto

Esta función devuelve el objeto pasado durante la implementación. Las propiedades del objeto devuelto difieren en función de si:

* se implementa un archivo de Bicep local.
* se implementa en un grupo de recursos o en uno de los otros ámbitos ([suscripción a Azure](deploy-to-subscription.md), [grupo de administración](deploy-to-management-group.md) o [inquilino](deploy-to-tenant.md)).

Al implementar un archivo de Bicep local en un grupo de recursos, la función devuelve el siguiente formato:

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

Al implementar en una suscripción a Azure, en un grupo de recursos o en un inquilino, el objeto devuelto incluye una propiedad `location`. La propiedad location se incluye al implementar un archivo de Bicep local. El formato es:

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

### <a name="example"></a>Ejemplo

El ejemplo siguiente devuelve el objeto de implementación:

```bicep
output deploymentOutput object = deployment()
```

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

El archivo de Bicep de ejemplo siguiente devuelve el objeto de entorno.

```bicep
output environmentOutput object = environment()
```

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

## <a name="next-steps"></a>Pasos siguientes

* Para obtener valores de recursos, grupos de recursos o suscripciones, consulte [Funciones de recursos](./bicep-functions-resource.md).
