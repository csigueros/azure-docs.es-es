---
title: Configuración de linter para la configuración de Bicep
description: Aquí se describe cómo personalizar los valores de configuración del linter de Bicep.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: d48ea625d58b35ac18c39bfb3fb60eb4ec3d7edd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557206"
---
# <a name="add-linter-settings-in-the-bicep-config-file"></a>Adición de una configuración de linter en el archivo de configuración de Bicep

En un archivo **bicepconfig.json**, puede personalizar la configuración de validación para el [linter de Bicep](linter.md). Linter usa esta configuración al evaluar los archivos de Bicep para los procedimientos recomendados.

En este artículo se describe la configuración disponible para trabajar con el linter de Bicep.

## <a name="customize-linter"></a>Personalización de linter

La configuración de linter está disponible en el elemento `analyzers`. Puede habilitar o deshabilitar el linter, proporcionar valores específicos de la regla y establecer el nivel de reglas.

En el ejemplo siguiente se muestran las reglas que están disponibles para la configuración.

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "verbose": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning"
        },
        "no-unused-params": {
          "level": "error"
        },
        "no-unused-vars": {
          "level": "error"
        },
        "prefer-interpolation": {
          "level": "warning"
        },
        "secure-parameter-default": {
          "level": "error"
        },
        "simplify-interpolation": {
          "level": "warning"
        }
      }
    }
  }
}
```

Las propiedades son las siguientes:

- **enabled**: especifique **true** para habilitar el linter o **false** para deshabilitarlo.
- **verbose**: especifique **true** para mostrar el archivo bicepconfig.json empleado por Visual Studio Code.
- **rules**: especifique los valores específicos de la regla. Cada regla tiene un nivel que determina cómo responde el linter cuando se detecta una infracción.

Los valores disponibles de **level** son los siguientes:

| **level**  | **Comportamiento en tiempo de compilación** | **Comportamiento del editor** |
|--|--|--|
| `Error` | Las infracciones aparecen como Errores en la salida de compilación de la línea de comandos y provocan un error de compilación. | El código incorrecto se subraya en rojo y aparece en la pestaña Problemas. |
| `Warning` | Las infracciones aparecen como Advertencias en la salida de compilación de la línea de comandos, pero no provocan un error de compilación. | El código incorrecto se subraya en amarillo y aparece en la pestaña Problemas. |
| `Info` | Las infracciones no aparecen en la salida de compilación de la línea de comandos. | El código incorrecto se subraya en azul y aparece en la pestaña Problemas. |
| `Off` | Se suprime por completo. | Se suprime por completo. |

## <a name="environment-urls"></a>Direcciones URL de entorno

En el caso de la regla sobre las direcciones URL de entorno codificadas de forma rígida, puede personalizar las direcciones URL que se comprueban. De manera predeterminada, se aplican los siguientes valores:

```json
{
  "analyzers": {
    "core": {
      "verbose": false,
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedhosts": [
            "management.core.windows.net",
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "vault.azure.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "api.loganalytics.iov1",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "api.loganalytics.iov1",
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
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Configuración del entorno de Bicep](bicep-config.md)
* [Adición de la configuración del módulo en la configuración de Bicep](bicep-config-modules.md)
* Más información acerca del [linter de Bicep](linter.md)
