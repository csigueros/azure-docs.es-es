---
title: 'Regla de linter: ninguna dirección URL de entorno codificada de forma rígida'
description: 'Regla de linter: ninguna dirección URL de entorno codificada de forma rígida'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 8bb3bbd4be61259d1a11184d879f737d970399cd
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165393"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Regla de linter: ninguna dirección URL de entorno codificada de forma rígida

Esta regla busca las direcciones URL codificadas de forma rígida que varían en función del entorno de nube.

## <a name="returned-code"></a>Código devuelto

`no-hardcoded-env-urls`

## <a name="solution"></a>Solución

En lugar de codificar de forma rígida las URL en el archivo Bicep, use la [función environment](../templates/template-functions-deployment.md#environment) para obtener estas direcciones URL de forma dinámica durante la implementación. La función de entorno devuelve distintas direcciones URL en función del entorno de nube en el que se implemente.

En el siguiente ejemplo no se supera esta prueba porque la URL está codificada de forma rígida.

```bicep
var managementURL = 'https://management.azure.com'
```

La prueba también genera un error cuando se usa con concat o uri.

```bicep
var galleryURL1 = concat('https://','gallery.azure.com')
var galleryURL2 = uri('gallery.azure.com','test')
```

Para corregirlo, reemplace la dirección URL codificada de forma rígida por la función `environment()`.

```bicep
var galleryURL = environment().gallery
```

En algunos casos, puede corregirlo obteniendo una propiedad de un recurso que ha implementado. Por ejemplo, en lugar de construir el punto de conexión para la cuenta de almacenamiento, recupérelo con `.properties.primaryEndpoints`.

```bicep
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: storageAccountName
  location: 'westus'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = sa.properties.primaryEndpoints.web
```

## <a name="configuration"></a>Configuración

De forma predeterminada, esta regla usa la siguiente configuración para determinar qué direcciones URL no están permitidas. 

```json
"analyzers": {
  "core": {
    "verbose": false,
    "enabled": true,
    "rules": {
      "no-hardcoded-env-urls": {
        "level": "warning",
        "disallowedhosts": [
          "gallery.azure.com",
          "management.core.windows.net",
          "management.azure.com",
          "database.windows.net",
          "core.windows.net",
          "login.microsoftonline.com",
          "graph.windows.net",
          "trafficmanager.net",
          "datalake.azure.net",
          "azuredatalakestore.net",
          "azuredatalakeanalytics.net",
          "vault.azure.net",
          "api.loganalytics.io",
          "asazure.windows.net",
          "region.asazure.windows.net",
          "batch.core.windows.net"
        ],
        "excludedhosts": [
          "schema.management.azure.com"
        ]
      }
    }
  }
}
```

Para personalizarla, puede agregar un archivo bicepconfig.json y aplicar una nueva configuración.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de linter, consulte [Uso de Bicep linter](./linter.md).
