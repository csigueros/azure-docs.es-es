---
title: Errores de nombre de cuenta de almacenamiento
description: Describe los errores que pueden producirse al especificar un nombre de cuenta de almacenamiento en una plantilla de Azure Resource Manager (plantilla de ARM) o un archivo Bicep.
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.openlocfilehash: 78bf097ccaf5f826dc20a11ee36375111b1a7354
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091429"
---
# <a name="resolve-errors-for-storage-account-names"></a>Resolución de errores de nombres de cuenta de almacenamiento

En este artículo se describen los errores de nomenclatura que pueden producirse al implementar una cuenta de almacenamiento con una plantilla de Azure Resource Manager (plantilla de ARM) o un archivo Bicep.

## <a name="symptom"></a>Síntoma

Si el nombre de la cuenta de almacenamiento incluye caracteres prohibidos, como una letra mayúscula o un signo de exclamación, recibirá un error:

```Output
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Para las cuentas de almacenamiento, debe proporcionar un nombre al recurso que sea único en Azure. Si no lo hace, recibirá un error.

```Output
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Si implementa una cuenta de almacenamiento con el mismo nombre que una cuenta de almacenamiento existente en su suscripción, pero en una ubicación diferente, recibirá un error. El error indica que la cuenta de almacenamiento ya existe en una ubicación diferente. Elimine la cuenta de almacenamiento que ya existe o proporcione la misma ubicación que la de la cuenta de almacenamiento existente.

## <a name="cause"></a>Causa

Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y usar solo números y letras minúsculas. No se pueden utilizar letras mayúsculas ni caracteres especiales. El nombre debe ser único.

## <a name="solution"></a>Solución

Puede crear un nombre único concatenando la convención de nomenclatura con el resultado de la función `uniqueString`.

Las plantillas de ARM utilizan [concatenación](../templates/template-functions-string.md#concat) con [uniqueString](../templates/template-functions-string.md#uniquestring).

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Bicep utiliza la [interpolación de cadena](../bicep/bicep-functions-string.md#concat) con [uniqueString](../bicep/bicep-functions-string.md#uniquestring).

```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
```

Asegúrese de que el nombre de cuenta de almacenamiento no supere los 24 caracteres. La función `uniqueString` devuelve 13 caracteres. Si desea concatenar un prefijo o sufijo, proporcione un valor de 11 caracteres o menos.

En los ejemplos siguientes se usa un parámetro que crea un prefijo con un máximo de 11 caracteres.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The prefix value for the storage account name."
    }
  }
}
```

```bicep
@description('The prefix value for the storage account name.')
@maxLength(11)
param storageNamePrefix string = 'storage'
```

A continuación, concatena el valor del parámetro con el valor `uniqueString` para crear un nombre de cuenta de almacenamiento.

```json
"name": "[concat(parameters('storageNamePrefix'), uniquestring(resourceGroup().id))]"
```

```bicep
name: '${storageNamePrefix}${uniqueString(resourceGroup().id)}'
```
