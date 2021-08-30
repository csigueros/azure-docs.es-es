---
title: Casos de prueba de createUiDefinition.json para el kit de herramientas de Azure Resource Manager
description: Describe las pruebas de createUiDefinition.json que ejecuta el kit de herramientas para pruebas de plantillas de Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 573f7db7c1f6fd3d45531e8be15db3d8affc8eba
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392040"
---
# <a name="test-cases-for-createuidefinitionjson"></a>Casos de prueba para createUiDefinition.json

En este artículo se describen las pruebas que se ejecutan con el [kit de herramientas para pruebas de plantillas](test-toolkit.md) para los archivos [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md). Los ejemplos incluye los nombres de las pruebas y los ejemplos de código que **pasan** o **no pasan** las pruebas.

El kit de herramientas incluye [casos de prueba](template-test-cases.md) para plantillas de Azure Resource Manager (plantillas de ARM) y los archivos principales de plantilla denominados _azuredeploy.json_ o _maintemplate.json_. Cuando el directorio contiene un archivo _createUiDefinition.json_, se ejecutan pruebas específicas para los controles de interfaz de usuario. Para más información sobre cómo ejecutar pruebas o sobre la ejecución de una prueba específica, consulte [Parámetros de prueba](test-toolkit.md#test-parameters).

El archivo _createUiDefinition.json_ crea controles de interfaz de usuario (UI) personalizados con [elementos](../managed-applications/create-uidefinition-elements.md) y [funciones](../managed-applications/create-uidefinition-functions.md).

## <a name="verify-template-parameter-allows-values"></a>Comprobación de que el parámetro de plantilla permite valores

Nombre de la prueba: **Los valores permitidos realmente se deben permitir**

Esta prueba comprueba que los valores de cada control en _createUiDefinition.json_ estén permitidos en los parámetros de la plantilla principal. Los parámetros se asignan por nombre entre la plantilla principal y el archivo _createUiDefinition.json_.

El parámetro de la plantilla principal debe aceptar los valores de `allowedValues` del control. La prueba también comprueba que se haga referencia al control en la sección `outputs` de _createUiDefinition.json_.

Esta prueba revisa la plantilla principal y el archivo _createUiDefinition.json_. Después de los ejemplos de la plantilla principal, se muestra un ejemplo del archivo _createUiDefinition.json_.

El ejemplo siguiente **no pasa** la prueba porque el nombre del parámetro `combo` de la plantilla principal no coincide con el nombre del parámetro `comboBox` del control.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "combo": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

El ejemplo siguiente **no pasa** la prueba porque el tipo de parámetro `int` de la plantilla principal no acepta el valor `string` del control. Y si un parámetro de la plantilla principal define un `defaultValue`, debe ser un `value` válido en los `allowedValues` del control.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba porque el nombre del parámetro de la plantilla principal coincide con el nombre del parámetro del control, y el tipo de parámetro de la plantilla es una `string` con un `defaultValue` especificado en los `allowedValues` del control.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    }
  }
}
```

El archivo _createUiDefinition.json_ para este ejemplo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "demoComboBox",
        "label": "demoComboBoxLabel",
        "elements": [
          {
            "name": "comboBox",
            "type": "Microsoft.Common.DropDown",
            "label": "Example drop down",
            "defaultValue": "Value two",
            "toolTip": "This is a tool tip",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Value one",
                  "description": "The value to select for option 1.",
                  "value": "one"
                },
                {
                  "label": "Value two",
                  "description": "The value to select for option 2.",
                  "value": "two"
                }
              ],
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "comboBox": "[steps('demoComboBox').comboBox]"
    }
  }
}
```

## <a name="output-controls-must-exist"></a>Deben existir controles en las salidas

Nombre de la prueba: **Deben existir controles en las salidas**

Los controles que se utilizan en la sección `outputs` deben existir en un elemento en otra parte de _createUiDefinition.json_. El nombre al que se hace referencia en `outputs` debe coincidir con un nombre utilizado en `basics[]` o en `steps[]`.

El ejemplo siguiente **no pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "nameDoesNotMatchOutput",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="properties-must-include-values"></a>Las propiedades deben incluir valores

Nombre de la prueba: **CreateUIDefinition no puede tener valores vacíos en blanco**

Las propiedades deben incluir valores. Las propiedades necesarias deben usar valores válidos. Se deben quitar las propiedades opcionales que estén en blanco. La prueba permite los valores en blanco `"basics": []`, `"steps": []` o `defaultValue`.

El ejemplo siguiente **no pasa** la prueba porque los valores `label`, `placeholder` y `toolTip` están en blanco.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "",
        "placeholder": "",
        "defaultValue": "",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba porque `label` y `toolTip` tienen valores, y se quitó `placeholder`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "defaultValue": "",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="use-valid-schema-and-version"></a>Uso de una versión y un esquema válidos

Nombre de la prueba: **CreateUIDefinition debe tener un esquema**

El archivo _createUiDefinition.json_ debe incluir una propiedad `$schema` y utilizar `$schema` y `version` válidos. Los números de versión en `$schema` y `version` deben coincidir.

El ejemplo siguiente **no pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.9.9-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.9.9-preview"
}
```

El ejemplo siguiente **pasa** la prueba porque utilizar los valores `$schema` y `version` más recientes.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-credential-confirmation"></a>Confirmación de credenciales sin ocultar

Nombre de la prueba: **La confirmación de credenciales no se debe ocultar**

Esta prueba revisa las credenciales que están confirmadas para [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) o [Microsoft.Compute.CredentialsCombo](../managed-applications/microsoft-compute-credentialscombo.md). La propiedad `hideConfirmation` se debe establecer en `false`a fin de que la confirmación sea visible.

El ejemplo siguiente **no pasa** la prueba porque `hideConfirmation` es `true`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": true
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba porque `hideConfirmation` es `false`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": false
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

## <a name="use-correct-handler"></a>Uso del controlador correcto

Nombre de la prueba: **El controlador debe ser correcto**

Use `Microsoft.Azure.CreateUIDef` o `Microsoft.Compute.MultiVm` en el archivo _createUiDefinition.json_.

El ejemplo siguiente **no pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.",
  "version": "0.1.2-preview"
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-existing-resources"></a>Recursos existentes sin ocultar

Nombre de la prueba: **HideExisting se debe controlar de manera correcta**

Si `hideExisting` se omite o está establecido en `false`, `outputs` debe contener `resourceGroup` y `newOrExisting`. El valor predeterminado de `hideExisting` es `false`.

Algunos ejemplos de tipos de control que incluyen `hideExisting` son [Microsoft.Storage.StorageAccountSelector](../managed-applications/microsoft-storage-storageaccountselector.md), [Microsoft.Network.PublicIpAddressCombo](../managed-applications/microsoft-network-publicipaddresscombo.md) o [Microsoft.Network.VirtualNetworkCombo](../managed-applications/microsoft-network-virtualnetworkcombo.md).

El ejemplo siguiente **no pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": false
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "resourceGroup": "[basics('storage').resourceGroup]",
      "newOrExisting": "[basics('storage').newOrExisting]"
    }
  }
}
```

## <a name="use-location-in-outputs"></a>Uso de ubicación en salidas

Nombre de la prueba: **La ubicación debe aparecer en las salidas**

La sección `outputs` debe contener una ubicación con la función [location](../managed-applications/create-ui-definition-referencing-functions.md#location).

El ejemplo siguiente **no pasa** la prueba porque `outputs` no incluye una ubicación.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="include-control-outputs-in-template-parameters"></a>Inclusión de salidas de control en parámetros de plantilla

Nombre de la prueba: **Las salidas deben aparecer en los parámetros de plantilla**

La prueba revisa que _createUiDefinition.json_ incluya una sección `outputs`. La prueba también revisa que esas `outputs` estén definidas en la sección `parameters` de la plantilla principal. Los nombres deben coincidir porque los parámetros están asignados por nombre entre el archivo _createUiDefinition.json_ y la plantilla principal.

Esta prueba revisa la plantilla principal y el archivo _createUiDefinition.json_. Después de los ejemplos de la plantilla principal, se muestra un ejemplo del archivo _createUiDefinition.json_.

El ejemplo siguiente **no pasa** la prueba porque la plantilla principal no incluye el parámetro `comboBox` de la sección `outputs` del archivo _createUiDefinition.json_.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba porque la plantilla principal incluye el parámetro `comboBox`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

El archivo _createUiDefinition.json_ para este ejemplo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="parameters-without-default-must-exist-in-outputs"></a>Las salidas deben incluir parámetros sin un valor predeterminado

Nombre de la prueba: **CreateUIDefinition debe incluir parámetros sin un valor predeterminado**

En la sección `outputs` del archivo _createUiDefinition.json_ deben existir parámetros en la plantilla principal sin un valor predeterminado.

Esta prueba revisa la plantilla principal y el archivo _createUiDefinition.json_. Después de los ejemplos del control, se muestra un ejemplo del archivo _azuredeploy.json_.

El ejemplo siguiente **no pasa** la prueba porque el valor `outputs` del archivo _createUiDefinition.json_ no incluye el parámetro `comboBox` de la plantilla principal.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba porque _createUiDefinition.json_ incluye `comboBox` en `outputs`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

El archivo _azuredeploy.json_ para este ejemplo. El parámetro `comboBox` no tiene un valor predeterminado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

## <a name="use-secure-parameter-with-password-box"></a>Uso de un parámetro seguro con el cuadro de contraseña

Nombre de la prueba: **Se deben usar cuadros de texto de contraseña para los parámetros de contraseña**

Esta prueba revisa que haya un elemento [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) definido en los `parameters` de la plantilla principal y las `outputs` de _createUiDefinition.json_. El tipo de parámetro de la plantilla principal para un cuadro de contraseña debe ser `secureString` o `secureObject`.

Esta prueba revisa la plantilla principal y el archivo _createUiDefinition.json_. Después de los ejemplos de la plantilla principal, se muestra un ejemplo del archivo _createUiDefinition.json_.

El ejemplo siguiente **no pasa** la prueba porque el parámetro `passwordBox` de la plantilla principal es una `string`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba porque el parámetro `passwordBox` de la plantilla principal es una `secureString`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "secureString"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

El archivo _createUiDefinition.json_ para este ejemplo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="password-box-requires-minimum-length"></a>El cuadro de contraseña requiere una longitud mínima

Nombre de la prueba: **Los cuadros de contraseña deben tener una longitud mínima**

La prueba revisa que el elemento [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) utilice `constraints` con una `regex` que requiere al menos 12 caracteres.

El ejemplo siguiente **no pasa** la prueba porque no hay `constraints`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba porque `regex` requiere al menos 12 caracteres.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password",
        "constraints": {
          "required": true,
          "regex": "^[a-zA-Z0-9]{12,}$",
          "validationMessage": "Password must be at least 12 characters long, contain only numbers and letters"
        }
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="text-box-must-use-validation"></a>El cuadro de texto debe usar la validación

Nombre de la prueba: **Los cuadros de texto tienen un formato correcto**

Use la validación con cuadros de texto para comprobar las `constraints` que contienen un elemento `regex` y un `message`.

El ejemplo siguiente **no pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="tooltip-must-exist-with-a-value"></a>Información sobre herramientas debe existir con un valor

Nombre de la prueba: **Debe haber información sobre herramientas**

Esta prueba revisa que exista la propiedad `toolTip` y que contenga un valor.

El ejemplo siguiente **no pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="dont-set-a-default-user-name"></a>Nombre de usuario predeterminado no establecido

Nombre de la prueba: **Los nombres de usuario no deben tener un valor predeterminado**

Esta prueba revisa si hay un `defaultValue` establecido para [Microsoft.Compute.UserNameTextBox](../managed-applications/microsoft-compute-usernametextbox.md).

El ejemplo siguiente **no pasa** la prueba porque se proporciona un `defaultValue`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "defaultValue": "admin",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

## <a name="use-message-with-validations"></a>Uso de mensajes con validaciones

Nombre de la prueba: **Las validaciones deben tener un mensaje**

Esta prueba revisa que todo `validations` en _createUiDefinition.json_ incluya `message`.

El ejemplo siguiente **no pasa** la prueba porque la validación de `regex` no tiene `message`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$"
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="virtual-machine-sizes-must-match"></a>Los tamaños de máquina virtual deben coincidir

Nombre de la prueba: **Los tamaños de VM deben coincidir con la plantilla**

Esta prueba revisa que [Microsoft.Compute.SizeSelector](../managed-applications/microsoft-compute-sizeselector.md) esté en las `outputs` del archivo _createUiDefinition.json_ y la sección `parameters` de la plantilla principal. Los parámetros de la plantilla principal que especifican un `defaultValue` deben coincidir con un valor en los `allowedSizes` del control.

Esta prueba revisa la plantilla principal y el archivo _createUiDefinition.json_. Después de los ejemplos de la plantilla principal, se muestra un ejemplo del archivo _createUiDefinition.json_.

El ejemplo siguiente **no pasa** la prueba porque el `defaultValue` de la plantilla principal no coincide con un valor en `allowedSizes`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D9"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba porque el `defaultValue` de la plantilla principal coincide con un valor en `allowedSizes`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D3"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

El archivo _createUiDefinition.json_ para este ejemplo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "vmSize",
        "type": "Microsoft.Compute.SizeSelector",
        "label": "VM Size",
        "toolTip": "Select a virtual machine size",
        "recommendedSizes": [
          "Standard_D1"
        ],
        "constraints": {
          "allowedSizes": [
            "Standard_D1",
            "Standard_D2",
            "Standard_D3"
          ]
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "vmSize": "[basics('vmSize')]"
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Para crear una interfaz de usuario de Azure Portal, consulte [CreateUiDefinition.json para la experiencia de creación de aplicaciones administradas de Azure](../managed-applications/create-uidefinition-overview.md).
- Para usar el espacio aislado Crear definición de UI, consulte [Prueba de la interfaz de su portal para Azure Managed Applications](../managed-applications/test-createuidefinition.md).
- Para más información sobre los controles de UI, consulte [Elementos CreateUiDefinition](../managed-applications/create-uidefinition-elements.md) y [Funciones CreateUiDefinition](../managed-applications/create-uidefinition-functions.md).
- Para más información sobre las pruebas de plantillas de ARM, consulte el artículo sobre [casos de prueba para plantillas de ARM](template-test-cases.md).
