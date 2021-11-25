---
title: Segmentos de nombre y tipo de recurso no válidos
description: Describe cómo resolver un error cuando el nombre y el tipo del recurso no tienen el mismo número de segmentos.
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.openlocfilehash: c4b04c78f84dada1e41289d1523b9ee2c49bd4e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404325"
---
# <a name="resolve-errors-for-resource-name-and-type-mismatch"></a>Resolución de errores de coincidencia entre el nombre y el tipo del recurso

En este artículo se describe cómo resolver el error cuando el formato del nombre del recurso no coincide con el del tipo de recurso.

## <a name="symptom"></a>Síntoma

Al implementar una plantilla, recibirá un error con el código de error **InvalidTemplate**. El mensaje indica que el tipo de recurso y el nombre no coinciden. Sugiere corregir el número de segmentos en el nombre.

## <a name="cause"></a>Causa

Un tipo de recurso contiene el espacio de nombres del proveedor de recursos y uno o varios segmentos para los tipos. Cada segmento representa un nivel en la jerarquía de recursos y está separado por una barra diagonal.

```
{resource-provider-namespace}/{type-segment-1}/{type-segment-2}
```

El nombre del recurso contiene uno o varios segmentos separados por barras diagonales. El número de segmentos debe coincidir con el número del tipo de recurso.

```
{name-segment-1}/{name-segment-2}
```

Si el tipo y el nombre de recurso contienen un número diferente de segmentos se producirá este error.

## <a name="solution"></a>Solución

Asegúrese de que comprende el nivel del tipo de recurso. Por ejemplo, un recurso de almacén de claves tiene un tipo de recurso completo de `Microsoft.KeyVault/vaults`. Puede omitir el espacio de nombres del proveedor de recursos **(Microsoft.KeyVault)** y centrarse en el tipo (**almacenes**). Tiene un segmento.

Un secreto del almacén de claves es un recurso secundario del almacén. Tiene un tipo de recurso completo de `Microsoft.KeyVault/vaults/secrets`. Este tipo de recurso tiene dos segmentos (**almacenes o secretos**).

Para especificar un nombre para el almacén de claves, proporcione un solo segmento, como `examplevault123`. Para especificar un nombre para el secreto, proporcione dos segmentos, como en `examplevault123/examplesecret`. El primer segmento indica el almacén de claves donde se almacena este secreto.

En el ejemplo siguiente se muestra un formato válido para el nombre del recurso.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
}
```

---

Aparecerá un **error** si proporcionó un nombre con más de un segmento.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'contoso/examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "contoso/examplevault123",
  ...
}
```

---

Cuando anide un recurso secundario dentro del recurso primario, proporcione solo el segmento adicional. El tipo de recurso completo y el nombre todavía contienen los valores del recurso primario, pero se construyen automáticamente. En el ejemplo siguiente, el tipo es `secrets` y el nombre es `examplesecret`.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
  resource kvsecret 'secrets' = {
    name: 'examplesecret'
    properties: {
     value: secretValue
    }
  }
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
  "resources": [
    {
      "type": "secrets",
      "apiVersion": "2019-09-01",
      "name": "examplesecret",
      "properties": {
        "value": "[parameters('secretValue')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
      ]
    }
  ]
}
```

---

Si define el recurso secundario fuera del elemento primario, proporcione el tipo de recurso completo. Para JSON, proporcione el nombre completo del recurso.

Para Bicep, use la propiedad `parent` y proporcione el nombre simbólico del recurso primario. Cuando se usa la propiedad primaria, el nombre completo se construye automáticamente, por lo que puede proporcionar el nombre del recurso secundario como un único segmento.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kvsecret 'Microsoft.KeyVault/vaults/secrets@2021-06-01-preview' = {
  name: 'examplesecret'
  parent: kv
  properties: {
     value: secretValue
  }
}

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults/secrets",
  "apiVersion": "2019-09-01",
  "name": "examplevault123/examplesecret",
  "properties": {
    "value": "[parameters('secretValue')]"
  },
  "dependsOn": [
    "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
  ]
},
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
```

---

Para más información, consulte [Establecimiento del nombre y el tipo de recursos secundarios en Bicep](../bicep/child-resource-name-type.md) o [Establecimiento del nombre y el tipo de recursos secundarios en plantillas de Resource Manager](../templates/child-resource-name-type.md).