---
title: Declaración de recursos en plantillas
description: En este artículo se describe cómo declarar recursos para implementar en una plantilla de Azure Resource Manager (plantilla de ARM).
ms.topic: conceptual
ms.date: 05/11/2021
ms.openlocfilehash: 7d7bae8adec81aa3344c5c571f0e40556928a1a9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960122"
---
# <a name="resource-declaration-in-arm-templates"></a>Declaración de recursos en plantillas de ARM

Para implementar un recurso a través de una plantilla de Azure Resource Manager (plantilla de ARM), agregue una declaración de recursos. Use la matriz `resources` en una plantilla JSON.

## <a name="set-resource-type-and-version"></a>Definición del tipo de recurso y versión

Al agregar un recurso a la plantilla, empiece por establecer el tipo de recurso y la versión de la API. Estos valores determinan las demás propiedades que están disponibles para el recurso.

En el ejemplo siguiente se muestra cómo establecer el tipo de recurso y la versión de la API para una cuenta de almacenamiento. En el ejemplo no se muestra la declaración de recursos completa.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

## <a name="set-resource-name"></a>Definición del nombre del recurso

Cada recurso tiene un nombre. Al establecer el nombre del recurso, preste atención a las [reglas y restricciones de los nombres de los recursos](../management/resource-name-rules.md).

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

## <a name="set-location"></a>Establezca una ubicación

Muchos recursos requieren una ubicación. Puede determinar si el recurso necesita una ubicación a través de IntelliSense o de la [referencia de plantillas](/azure/templates/). En el ejemplo siguiente se agrega un parámetro de ubicación que se usa para la cuenta de almacenamiento.

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

Para obtener más información, consulte [Establecimiento de la ubicación del recurso en la plantilla de Resource Manager](resource-location.md).

## <a name="set-tags"></a>Definición de etiquetas

Puede aplicar etiquetas a un recurso durante la implementación. Las etiquetas le ayudan a organizar de forma lógica los recursos implementados. Para ver ejemplos de las diferentes maneras de especificar las etiquetas, consulte [Etiquetas de plantillas de ARM](../management/tag-resources.md#arm-templates).

## <a name="set-resource-specific-properties"></a>Definición de propiedades específicas de recursos

Las propiedades anteriores son genéricas para la mayoría de los tipos de recursos. Después de establecer esos valores, tiene que establecer las propiedades que son específicas del tipo de recurso que va a implementar.

Use IntelliSense o la [referencia de plantillas](/azure/templates/) para decidir qué propiedades están disponibles y cuáles son obligatorias. En el ejemplo siguiente se establecen las propiedades restantes para una cuenta de almacenamiento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para implementar un recurso de manera condicional, consulte [Implementación condicional en las plantillas de ARM](conditional-resource-deployment.md).
* Para establecer dependencias de recursos, consulte [Definición del orden de implementación de recursos en plantillas de ARM](./resource-dependency.md).