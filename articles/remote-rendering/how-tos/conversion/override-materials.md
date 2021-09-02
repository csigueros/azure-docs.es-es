---
title: Reemplazo de materiales durante la conversión de modelos
description: Explica el flujo de trabajo de reemplazo de material en el momento de la conversión.
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: f99439ae71f8a66b9fc36b32603681e62f2224a9
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968254"
---
# <a name="override-materials-during-model-conversion"></a>Reemplazo de materiales durante la conversión de modelos

La configuración de materiales del modelo de origen se usa para definir los [materiales de PBR](../../overview/features/pbr-materials.md) que usa el representador.
A veces, la [conversión predeterminada](../../reference/material-mapping.md) no proporciona los resultados deseados y es necesario realizar cambios.
Cuando se convierte un modelo para su uso en Azure Remote Rendering, se puede proporcionar un archivo de invalidación de materiales para personalizar cómo se realiza la conversión de cada material.
Si se encuentra un archivo llamado `<modelName>.MaterialOverrides.json` en el contenedor de entrada situado junto al modelo de entrada `<modelName>.<ext>`, se usará como archivo de invalidación de materiales.

## <a name="the-override-file-used-during-conversion"></a>Archivo de invalidación usado durante la conversión

Como ejemplo sencillo, supongamos que un modelo de cuadros tiene un único material, denominado "Default".
Además, digamos que el color albedo debe ajustarse para usarse en ARR.
En este caso, se puede crear un archivo `box.MaterialOverrides.json` de la siguiente manera:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

El archivo `box.MaterialOverrides.json` se coloca en el contenedor de entrada junto a `box.fbx`, que indica al servicio de conversión que aplique la nueva configuración.

### <a name="color-materials"></a>Materiales de color

El modelo de [material de color](../../overview/features/color-materials.md) describe una superficie sombreada constantemente que es independiente de la iluminación.
Los materiales de color son útiles para los recursos que se crean con los algoritmos de fotogrametría, por ejemplo.
En los archivos de invalidación de materiales, un material se puede declarar como material de color estableciendo `unlit` en `true`.

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Omitir mapas de texturas específicas

En ocasiones, es posible que quiera que el proceso de conversión ignore determinados mapas de texturas. Este puede ser el caso cuando el modelo se generó mediante una herramienta que genera mapas especiales que el representador no entiende correctamente. Por ejemplo, un elemento "OpacityMap" que se usa para definir un valor distinto de la opacidad o un modelo donde "NormalMap" se almacena como "BumpMap". (En este último caso, es recomendable omitir "NormalMap", lo que hará que el convertidor use "BumpMap" como "NormalMap").

El principio es sencillo. Simplemente, agregue una propiedad llamada `ignoreTextureMaps` y cualquier mapa de texturas que quiera omitir:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Para obtener la lista completa de mapas de texturas que puede ignorar, vea el siguiente esquema JSON.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Aplicación de las mismas invalidaciones a varios materiales

De forma predeterminada, una entrada en el archivo de reemplazo de material se aplica cuando su nombre coincide exactamente con el nombre del material.
Dado que es bastante común que la misma invalidación se aplique a varios materiales, puede proporcionar opcionalmente una expresión regular como nombre de la entrada.
El campo `nameMatching` tiene un valor predeterminado `exact`, pero se puede establecer en `regex` para indicar que la entrada debe aplicarse a todos los materiales coincidentes.
La sintaxis usada es la misma que la que se usa para JavaScript. En el ejemplo siguiente se muestra una invalidación que se aplica a los materiales con nombres como "material2", "Material01" y "Material999".

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

Como máximo, una entrada en un archivo de invalidación de material se aplica a un único material.
Si hay una coincidencia exacta (es decir, `nameMatching` está ausente o es igual a `exact`) para el nombre del material, se elige dicha entrada.
De lo contrario, se elige la primera entrada regex del archivo que coincida con el nombre del material.

### <a name="getting-information-about-which-entries-applied"></a>Obtención de información sobre las entradas aplicadas

El [archivo info](get-information.md#information-about-a-converted-model-the-info-file) escrito en el contenedor de salida contiene información sobre el número de invalidaciones proporcionadas y el número de materiales que se han invalidado.

## <a name="json-schema"></a>Esquema JSON

Aquí se proporciona el esquema JSON completo para los archivos de materiales. A excepción de `unlit` y `ignoreTextureMaps`, las propiedades disponibles son un subconjunto de las propiedades descritas en las secciones de los modelos de [material de color](../../overview/features/color-materials.md) y [material de PBR](../../overview/features/pbr-materials.md).

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" },
            "transparencyWriteDepth": {"type" : "boolean" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Materiales de color](../../overview/features/color-materials.md)
* [Materiales de PBR](../../overview/features/pbr-materials.md)
