---
title: Casos de prueba de todos los archivos para el kit de herramientas de pruebas de Azure Resource Manager
description: Describe las pruebas que el kit de herramientas de pruebas de Azure Resource Manager ejecuta todos los archivos.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 6af9a7c149aacbd50537086ae70a8d845604d2ef
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393699"
---
# <a name="test-cases-for-all-files"></a>Casos de prueba de todos los archivos

En este artículo se describen las pruebas que se ejecutan con el [kit de pruebas de plantillas](test-toolkit.md) en todos los archivos JSON. Los ejemplos incluye los nombres de las pruebas y los ejemplos de código que **pasan** o **no pasan** las pruebas. Para más información sobre cómo ejecutar pruebas o sobre la ejecución de una prueba específica, consulte [Parámetros de prueba](test-toolkit.md#test-parameters).

## <a name="use-valid-json-syntax"></a>Uso de una sintaxis JSON válida

Nombre de prueba: JSONFiles debe ser válido

Esta prueba comprueba que todos los archivos JSON contienen una sintaxis válida. Por ejemplo, los archivos _azuredeploy.json_, _azuredeploy.parameters.json_ o _createUiDefinition.json_. Si se produce un **error** en la prueba, verá errores o advertencias en otras pruebas o análisis de JSON.

### <a name="template-file-example"></a>Ejemplo de archivo de plantilla

En el ejemplo siguiente se produce un error porque en _azuredeploy.json_ la llave inicial (`{`) falta en `parameters`, `comboBox` y `location`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters":
    "comboBox":
      "type": "string"
    },
    "location":
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

El ejemplo siguiente **pasa** la prueba.

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

### <a name="parameter-file-example"></a>Ejemplo de archivo de parámetros

El siguiente ejemplo **se produce un error** porque _azuredeploy.parameters.json_ usa un parámetro sin un `value`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value":
    }
  }
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value": "westus"
    }
  }
}
```

### <a name="createuidefintion-example"></a>Ejemplo de CreateUiDefintion

En el ejemplo siguiente **se produce un error** porque en _createUiDefinition.json_ la llave inicial (`{`) falta en la sección `outputs`.

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
    "outputs":
      "comboBox": "[basics('comboBox')]",
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

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre el kit de herramientas para pruebas, consulte [Uso del kit de herramientas para pruebas de plantillas de ARM](test-toolkit.md).
- Para pruebas de plantillas de ARM, consulte [Casos de prueba para plantillas de ARM](template-test-cases.md).
- Para probar los archivos de parámetros, consulte [Casos de prueba para archivos de parámetros](parameters.md).
- Para las pruebas de createUiDefinition, consulte [Casos de prueba para createUiDefinition.json](createUiDefinition-test-cases.md).
