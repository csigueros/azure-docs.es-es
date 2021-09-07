---
title: Asignación de campos de salida de aptitudes
titleSuffix: Azure Cognitive Search
description: Exporte el contenido enriquecido creado por un conjunto de aptitudes mediante la asignación de sus campos de salida a los campos de un índice de búsqueda.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 0e1db5f12e83a88697db38a6ddd77edab445f6c0
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195206"
---
# <a name="map-enrichment-output-to-fields-in-a-search-index"></a>Asignación de la salida de enriquecimiento a campos de un índice de búsqueda

![Fases del indexador](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "fases del indexador")

En este artículo, aprenderá a asignar campos de entrada enriquecidos a campos de salida en un índice de búsqueda. Una vez que haya [definido un conjunto de aptitudes](cognitive-search-defining-skillset.md), debe asignar los campos de salida de cualquier aptitud que aporte directamente valores a un campo dado del índice de búsqueda.

Las asignaciones de campos de salida son necesarias para mover contenido de documentos enriquecidos al índice.  El documento enriquecido es en realidad un árbol de información y, aunque hay compatibilidad con tipos complejos en el índice, a veces puede interesarle transformar la información del árbol enriquecido en un tipo más simple (por ejemplo, una matriz de cadenas). Las asignaciones de campos de salida permiten realizar transformaciones en la forma de los datos mediante la reducción de información. Las asignaciones de campos de salida siempre se producen después de la ejecución del conjunto de aptitudes, aunque es posible que esta fase se ejecute aunque no se haya definido ninguno.

Ejemplos de asignaciones de campos de salida:

* Como parte de su habilidades, ha extraído los nombres de las organizaciones mencionadas en cada una de las páginas del documento. Ahora desea asignar cada uno de los nombres de la organización a un campo en el índice de tipo Edm.Collection(Edm.String).

* Como parte de su conjunto de aptitudes, generó un nuevo nodo denominado "document/translated_text". Le gustaría asignar la información de este nodo a un campo específico del índice.

* No tiene un conjunto de aptitudes pero está indizando un tipo complejo desde una base de datos Cosmos DB. Quiere obtener acceso a un nodo de ese tipo complejo y asignarlo a un campo en el índice.

> [!NOTE]
> Las asignaciones de campos de salida solo se aplican a índices de búsqueda. En el caso de los indexadores que crean [almacenes de conocimiento](knowledge-store-concept-intro.md), se omiten las asignaciones de campos de salida.

## <a name="use-outputfieldmappings"></a>Usar outputFieldMappings

Para asignar los campos, agregue `outputFieldMappings` a la definición del indexador tal como se muestra a continuación:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

El cuerpo de la solicitud está estructurado de la siguiente manera:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

En cada asignación de campo de salida, establezca la ubicación de los datos del árbol de documentos enriquecidos (sourceFieldName) y el nombre del campo al que se hace referencia en el índice (targetFieldName). Asigne las [funciones de asignación](search-indexer-field-mappings.md#field-mapping-functions) que necesite para transformar el contenido de un campo antes de almacenarlo en el índice.

## <a name="flattening-information-from-complex-types"></a>Reducción de la información de tipos complejos 

La ruta de acceso en un campo sourceFieldName puede representar un elemento o varios elementos. En el ejemplo anterior, ```/document/content/sentiment``` representa un único valor numérico, mientras que ```/document/content/organizations/*/description``` representa varias descripciones de la organización. 

En casos donde hay varios elementos, estos son "reducidos" en una matriz que contiene cada uno de los elementos. 

Más concretamente, en el ejemplo ```/document/content/organizations/*/description```, los datos del campo *Descripciones* tienen el aspecto de una matriz plana de descripciones antes de que se indexe:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Este es un principio importante, así que se proporcionará otro ejemplo. Imagine que tiene una matriz de tipos complejos como parte del árbol de enriquecimiento. Supongamos que hay un miembro denominado customEntities que tiene una matriz de tipos complejos como la que se describe a continuación.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Supongamos que el índice tiene un campo denominado "diseases" de tipo Collection(Edm.String), donde quiere almacenar cada uno de los nombres de las entidades. 

Esto se puede hacer fácilmente mediante el símbolo "\*", como se indica a continuación:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Esta operación simplemente "reduce" cada uno de los nombres de los elementos customEntities a una sola matriz de cadenas como esta:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Pasos siguientes
Una vez haya asignado los campos enriquecidos a los campos de búsqueda, puede establecer los atributos de campo de cada uno de los campos de búsqueda [como parte de la definición del índice](search-what-is-an-index.md).

Para obtener más información acerca de las asignaciones de campos, consulte [Field mappings in Azure Cognitive Search indexers](search-indexer-field-mappings.md) (Asignaciones de campos en los indexadores de Búsqueda cognitiva de Azure).