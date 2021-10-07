---
title: 'Regla de linter: ninguna dirección URL de entorno codificada de forma rígida'
description: 'Regla de linter: ninguna dirección URL de entorno codificada de forma rígida'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: d35736860dd672ffc00ea1d4cde52d8f4d4ef8c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594565"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Regla de linter: ninguna dirección URL de entorno codificada de forma rígida

Linter facilita la aplicación de estándares de codificación proporcionando instrucciones durante el desarrollo. El conjunto actual de reglas de linter es mínimo y se obtiene de los [casos de prueba de arm-ttk](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Para obtener más información, consulte [Uso de linter de Bicep](./linter.md).

## <a name="code"></a>Código

`no-hardcoded-env-urls`

## <a name="description"></a>Descripción

No codifique de forma rígida direcciones URL de entorno en su plantilla. En su lugar, use la [función de entorno ](../templates/template-functions-deployment.md#environment) para obtener estas direcciones URL de forma dinámica durante la implementación. Para obtener una lista de los hosts de dirección URL que están bloqueados, consulte la lista predeterminada de `DisallowedHosts` en [bicepconfig.json](https://github.com/Azure/bicep/blob/main/src/Bicep.Core/Configuration/bicepconfig.json).

## <a name="examples"></a>Ejemplos

En el siguiente ejemplo no se supera esta prueba porque la URL está codificada de forma rígida.

```bicep
var AzureURL = 'https://management.azure.com'
```

La prueba también genera un error cuando se usa con concat o uri.

```bicep
var AzureSchemaURL1 = concat('https://','gallery.azure.com')
var AzureSchemaURL2 = uri('gallery.azure.com','test')
```

En el ejemplo siguiente se supera esta prueba.

```bicep
var AzureSchemaURL = environment().gallery
```

## <a name="configuration"></a>Configuración

El conjunto de hosts de dirección URL que se va a prohibir se puede personalizar mediante la propiedad disallowedHosts del archivo bicepconfig.json como se muestra a continuación:

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedHosts": [
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
          ]
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Visual Studio Code y la extensión Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
