---
title: Archivo de configuración de Bicep
description: Se describe cómo personalizar los valores de configuración de las implementaciones de Bicep.
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 7a057d353fd5b25ae122e7856f1ccb560d7fce56
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071841"
---
# <a name="add-custom-settings-in-the-bicep-config-file"></a>Adición de un valor personalizado en el archivo de configuración de Bicep

Para personalizar los valores de configuración de las implementaciones de Bicep, agregue un archivo denominado **bicepconfig.json** al directorio donde se almacenan los archivos de Bicep. En este archivo, especifique los valores de configuración que se usarán.

Puede agregar varios archivos bicepconfig.json. Se usa el archivo de configuración más cercano de la jerarquía de directorios.

En este artículo se describen las propiedades que están disponibles en el archivo de configuración. Sin embargo, también puede descubrir dichas propiedades a través de IntelliSense, una característica que proporciona la extensión de Bicep para Visual Studio Code.

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="Compatibilidad con IntelliSense para configurar bicepconfig.json":::

## <a name="aliases-for-modules"></a>Alias para módulos

A fin de simplificar la ruta de acceso para vincular a los módulos, puede crear alias en el archivo de configuración. Un alias puede hacer referencia a un registro de módulo o a un grupo de recursos que contiene especificaciones de plantilla. El archivo de configuración tiene una propiedad para `moduleAliases`. A fin de crear un alias para un registro de Bicep, agregue una propiedad `br` en la propiedad `moduleAliases`. Si quiere agregar un alias para una especificación de plantilla, use la propiedad `ts`.

```json
{
  "moduleAliases": {
    "br": {
      <add-registry-aliases>
    },
    "ts": {
      <add-template-specs-aliases>
    }
  }
}
```

En la propiedad `br`, agregue tantos alias como necesite. Para cada alias, asigne un nombre y las siguientes propiedades:

- **registry** (obligatorio): nombre del servidor de inicio de sesión del registro
- **modulePath** (opcional): repositorio del registro donde se almacenan los módulos

En la propiedad `ts`, agregue tantos alias como necesite. Para cada alias, asigne un nombre y las siguientes propiedades:

- **subscription** (obligatorio): el id. de la suscripción que hospeda las especificaciones de plantilla
- **resourceGroup** (obligatorio): el nombre del grupo de recursos que contiene las especificaciones de plantilla

En el ejemplo siguiente se muestra un archivo de configuración que define dos alias para un registro de módulos y un alias para un grupo de recursos que contiene especificaciones de plantilla.

```json
{
  "moduleAliases": {
    "br": {
      "ContosoRegistry": {
        "registry": "contosoregistry.azurecr.io"
      },
      "CoreModules": {
        "registry": "contosoregistry.azurecr.io",
        "modulePath": "bicep/modules/core"
      }
    },
    "ts": {
      "CoreSpecs": {
        "subscription": "00000000-0000-0000-0000-000000000000",
        "resourceGroup": "CoreSpecsRG"
      }
    }
  }
}
```

Al usar un alias en la referencia del módulo, debe usar los formatos siguientes:

```bicep
br/<alias>:<file>:<tag>
ts/<alias>:<file>:<tag>
```

Defina los alias en la carpeta o el grupo de recursos que contiene los módulos, no en el propio archivo. El nombre de archivo debe incluirse en la referencia al módulo.

**Sin los alias**, se vincularía a un módulo de un registro con la ruta de acceso completa.

```bicep
module stgModule 'br:contosoregistry.azurecr.io/bicep/modules/core/storage:v1' = {
```

**Con los alias**, puede simplificar el vínculo mediante el alias para el registro.

```bicep
module stgModule 'br/ContosoRegistry:bicep/modules/core/storage:v1' = {
```

También puede simplificar el vínculo mediante el alias que especifica la ruta de acceso del registro y del módulo.

```bicep
module stgModule  'br/CoreModules:storage:v1' = {
```

En el caso de una especificación de plantilla, use lo siguiente:

```bicep
module stgModule  'ts/CoreSpecs:storage:v1' = {
```

## <a name="credentials-for-restoring-modules"></a>Credenciales para restaurar módulos

Para [restaurar](bicep-cli.md#restore) módulos externos en la caché local, la cuenta debe tener los permisos correctos para acceder al registro. Puede configurar la precedencia de credenciales para la autenticación en el registro. De manera predeterminada, Bicep usa las credenciales del usuario autenticado en la CLI de Azure o en Azure PowerShell. Para personalizar la precedencia de las credenciales, agregue los elementos `cloud` y `credentialPrecedence` al archivo de configuración.

```json
{
    "cloud": {
      "credentialPrecedence": [
        "AzureCLI",
        "AzurePowerShell"
      ]
    }
}
```

Las credenciales disponibles son las siguientes:

* AzureCLI
* AzurePowerShell
* Entorno
* ManagedIdentity
* VisualStudio
* VisualStudioCode

## <a name="customize-linter"></a>Personalización de linter

En el archivo de configuración, puede personalizar los valores del [linter de Bicep](linter.md). Puede habilitar o deshabilitar el linter, proporcionar valores específicos de la regla y establecer el nivel de reglas.

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


