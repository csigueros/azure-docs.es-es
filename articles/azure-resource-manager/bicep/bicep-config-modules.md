---
title: Configuración del módulo para la configuración de Bicep
description: Aquí se describe cómo personalizar los valores de configuración de las implementaciones de Bicep.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: e130695532d470a154acc0b35bca864fcdbcba9d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557173"
---
# <a name="add-module-settings-in-the-bicep-config-file"></a>Adición de una configuración de módulo en el archivo de configuración de Bicep

En un archivo **bicepconfig.json**, puede crear alias para las rutas de acceso del módulo y configurar la precedencia de credenciales para restaurar un módulo.

En este artículo se describe la configuración disponible para trabajar con [módulos](modules.md).

## <a name="aliases-for-modules"></a>Alias para módulos

A fin de simplificar la ruta de acceso para vincular a los módulos, puede crear alias en el archivo de configuración. Un alias puede hacer referencia a un registro de módulo o a un grupo de recursos que contiene especificaciones de plantilla.

El archivo de configuración tiene una propiedad para `moduleAliases`. Esta propiedad contiene todos los alias que defina. En esta propiedad, los alias se dividen en función de si hacen referencia a un registro o a una especificación de plantilla.

A fin de crear un alias para un **registro de Bicep**, agregue una propiedad `br`. Si quiere agregar un alias para una **especificación de plantilla**, use la propiedad `ts`.

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

## <a name="next-steps"></a>Pasos siguientes

* [Configuración del entorno de Bicep](bicep-config.md)
* [Adición de la configuración de linter a la configuración de Bicep](bicep-config-linter.md)
* Más información sobre los [módulos](modules.md)
