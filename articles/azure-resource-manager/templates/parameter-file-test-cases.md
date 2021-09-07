---
title: Casos de prueba de archivos de parámetros para el kit de herramientas de pruebas de Azure Resource Manager
description: Describe las pruebas de archivos de parámetros que ejecuta el kit de herramientas para pruebas de plantillas de Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 37fefd84a6385e003a4bb501a789003e8c63d461
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393731"
---
# <a name="test-cases-for-parameter-files"></a>Casos de prueba para archivos de parámetros

En este artículo se describen las pruebas que se ejecutan con el [kit de herramientas para pruebas de plantillas](test-toolkit.md) para [archivos de parámetros](parameter-files.md). Por ejemplo, un archivo denominado _azuredeploy.parameters.json_. En los ejemplos se incluyen los nombres de las pruebas y los ejemplos de código que **pasan** o **no pasan** las pruebas. Para más información sobre cómo ejecutar pruebas o sobre la ejecución de una prueba específica, consulte [Parámetros de prueba](test-toolkit.md#test-parameters).

El kit de herramientas incluye [casos de prueba](template-test-cases.md) para plantillas de Azure Resource Manager (plantillas de ARM) y los archivos principales de plantilla denominados _azuredeploy.json_ o _maintemplate.json_.

## <a name="use-valid-contentversion"></a>Uso de un elemento contentVersion válido

Nombre de prueba: DeploymentParameters debe contener un elemento ContentVersion.

`contentVersion` debe contener una cadena en el formato `1.0.0.0` y usar solo números.

El ejemplo siguiente **no pasa** la prueba porque falta `contentVersion`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

El ejemplo siguiente **no pasa** la prueba porque `contentVersion` no es una cadena.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": {},
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
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
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="file-must-include-parameters"></a>El archivo debe incluir parámetros

Nombre de prueba: DeploymentParameters debe contener un elemento Parameters.

Un archivo de parámetros debe incluir la sección `parameters`.

El ejemplo siguiente **no pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="use-valid-schema-version"></a>Uso de una versión de esquema válida

Nombre de prueba: DeploymentParameters debe contener un elemento Schema.

El archivo de parámetros debe incluir una versión de esquema válida.

Hay dos versiones de esquema válidas para los archivos de parámetros:

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#`

El ejemplo siguiente **no pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2021-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
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
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="parameters-must-contain-values"></a>Los parámetros deben contener valores

Nombre de prueba: DeploymentParameters debe contener un elemento Value.

Un parámetro debe contener `value` o `reference`. En el caso de secretos, como una contraseña, un almacén de claves usa `reference` en el archivo de parámetros. Para más información, consulte [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](key-vault-parameter.md).

El ejemplo siguiente **no pasa** la prueba porque `stgAcctName` no contiene un elemento `value`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {}
  }
}
```

El ejemplo siguiente **pasa** la prueba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre el kit de herramientas para pruebas, consulte [Uso del kit de herramientas para pruebas de plantillas de ARM](test-toolkit.md).
- Para pruebas de plantillas de ARM, consulte [Casos de prueba para plantillas de ARM](template-test-cases.md).
- Para las pruebas de createUiDefinition, consulte [Casos de prueba para createUiDefinition.json](createUiDefinition-test-cases.md).
- Para información sobre las pruebas de todos los archivos, vea [Casos de prueba para todos los archivos](all-files-test-cases.md).
