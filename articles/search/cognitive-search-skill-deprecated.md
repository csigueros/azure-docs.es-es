---
title: Aptitudes cognitivas en desuso
titleSuffix: Azure Cognitive Search
description: Esta página contiene una lista de aptitudes cognitivas que se consideran en desuso y no se admitirán en un futuro próximo en conjuntos de aptitudes de Búsqueda cognitiva de Azure.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b44ba49fea555652bdf513e40c3e030ec5862cf5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751865"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Aptitudes cognitivas en desuso en Azure Cognitive Search

En este documento se describen las aptitudes cognitivas que se consideran en desuso. Use la siguiente guía para el contenido:

* Nombre de la aptitud: nombre de la aptitud que quedará en desuso; se asigna al atributo @odata.type.
* Última versión de API disponible: la última versión de la API pública de Azure Cognitive Search mediante la cual se pueden crear o actualizar conjuntos de aptitudes que contienen la correspondiente aptitud en desuso. Los indexadores con conjuntos de aptitudes asociados a estas aptitudes seguirán funcionando incluso en futuras versiones de API hasta la fecha de finalización del soporte técnico, momento en el que comenzarán a dar error.
* Finalización del soporte técnico: día después del cual se considera que la aptitud correspondiente no se admite y dejará de funcionar. Los conjuntos de habilidades creados anteriormente todavía funcionan, pero se recomienda a los usuarios realizar la migración desde una aptitud en desuso.
* Recomendaciones: Ruta de migración hacia delante para usar una aptitud admitida. Se aconseja a los usuarios seguir las recomendaciones para continuar recibiendo soporte técnico.

Si usa [Microsoft.Skills.Text.EntityRecognitionSkill](#microsoftskillstextentityrecognitionskill), este artículo le ayudará a actualizar su conjunto de aptitudes para usar [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md), que está disponible de forma general e incorpora nuevas características. 

Si usa [Microsoft.Skills.Text.SentimentSkill](#microsoftskillstextsentimentskill), este artículo le ayudará a actualizar su conjunto de aptitudes para usar [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md), que está disponible de forma general e incorpora nuevas características. 

Si usa [Microsoft.Skills.Text.NamedEntityRecognitionSkill](#microsoftskillstextnamedentityrecognitionskill), este artículo le ayudará a actualizar su conjunto de aptitudes para usar [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md), que está disponible de forma general e incorpora nuevas características.

## <a name="microsoftskillstextentityrecognitionskill"></a>Microsoft.Skills.Text.EntityRecognitionSkill

### <a name="last-available-api-version"></a>Última versión de API disponible

2021-04-30-Preview

### <a name="end-of-support"></a>Finalización del soporte técnico

31 de agosto de 2024

### <a name="recommendations"></a>Recomendaciones 

Use [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) en su lugar. Proporciona la mayor parte de la funcionalidad de EntityRecognitionSkill con una calidad superior. También tiene una información más completa en sus campos de salida compleja.

Para migrar a [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md), tendrá que realizar uno o varios de los siguientes cambios en la definición de la aptitud. Puede actualizar la definición de aptitud mediante la [API de actualización de conjuntos de aptitudes](/rest/api/searchservice/update-skillset).

1. *(Obligatorio)* Cambie `@odata.type` de `"#Microsoft.Skills.Text.EntityRecognitionSkill"` a `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`.

2. *(Opcional)* El parámetro `includeTypelessEntities` ya no se admite, ya que la nueva aptitud solo devolverá entidades con tipos conocidos, por lo que, si la definición de aptitud anterior hacía referencia a él, ahora debería quitarse.

3. *(Opcional)* Si usa el resultado `namedEntities`, tenga en cuenta que hay algunos cambios secundarios en los nombres de las propiedades.
    1. `value` ahora se llama `text`
    2. `confidence` ahora se llama `confidenceScore`

    Si necesita generar exactamente los mismos nombres de propiedad, deberá agregar un objeto [ShaperSkill](cognitive-search-skill-shaper.md) para volver a dar forma al resultado con los nombres necesarios. Por ejemplo, este objeto ShaperSkill cambia el nombre de las propiedades a sus valores antiguos.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntitiesV3/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntitiesV3/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntitiesV3/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntitiesV3/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "namedEntities"
            }
        ]
    }
    ```

4. *(Opcional)* Si usa el resultado `entities` para vincular entidades a entidades conocidas, tenga en cuenta que esta función es ahora una nueva aptitud: [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md). Agregue la aptitud de vinculación de entidad al conjunto de aptitudes para generar las entidades vinculadas. También hay algunos cambios secundarios en los nombres de las propiedades del resultado `entities` entre el objeto `EntityRecognitionSkill` y el nuevo objeto `EntityLinkingSkill`.
    1. `wikipediaId` ahora se llama `id`
    2. `wikipediaLanguage` ahora se llama `language`
    3. `wikipediaUrl` ahora se llama `url`
    4. Las propiedades `type` y `subtype` ya no se devuelven.

    Si necesita generar exactamente los mismos nombres de propiedad, deberá agregar un objeto [ShaperSkill](cognitive-search-skill-shaper.md) para volver a dar forma al resultado con los nombres necesarios. Por ejemplo, este objeto ShaperSkill cambia el nombre de las propiedades a sus valores antiguos.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "LinkedEntitiesShaper",
        "description": "LinkedEntitiesShaper",
        "context": "/document/linkedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/linkedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "name",
                        "source": "/document/linkedEntitiesV3/*/name"
                    },
                    {
                        "name": "wikipediaId",
                        "source": "/document/linkedEntitiesV3/*/id"
                    },
                    {
                        "name": "wikipediaLanguage",
                        "source": "/document/linkedEntitiesV3/*/language"
                    },
                    {
                        "name": "wikipediaUrl",
                        "source": "/document/linkedEntitiesV3/*/url"
                    },
                    {
                        "name": "bingId",
                        "source": "/document/linkedEntitiesV3/*/bingId"
                    },
                    {
                        "name": "matches",
                        "source": "/document/linkedEntitiesV3/*/matches"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

5. *(Opcional)* Si no especifica explícitamente `categories`, `EntityRecognitionSkill V3` puede devolver un tipo diferente de categorías, además de las que eran compatibles con `EntityRecognitionSkill`. Si no desea este comportamiento, asegúrese de establecer explícitamente el parámetro `categories` en `["Person", "Location", "Organization", "Quantity", "Datetime", "URL", "Email"]`.

    _Definiciones de migración de ejemplo_

    * Migración simple

        _(Antes) Definición de la aptitud EntityRecognition_

        ```json
        {   
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(Después) Definición de la aptitud EntityRecognition V3_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * Migración complicada

        _(Antes) Definición de la aptitud EntityRecognition_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "includeTypelessEntities": true,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntities"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        }
        ```
    
        _(Después) Definición de la aptitud EntityRecognition V3_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntitiesV3"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntitiesV3/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntitiesV3/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntitiesV3/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntitiesV3/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "entities",
                    "targetName": "linkedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "LinkedEntitiesShaper",
            "description": "LinkedEntitiesShaper",
            "context": "/document/linkedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/linkedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "name",
                            "source": "/document/linkedEntitiesV3/*/name"
                        },
                        {
                            "name": "wikipediaId",
                            "source": "/document/linkedEntitiesV3/*/id"
                        },
                        {
                            "name": "wikipediaLanguage",
                            "source": "/document/linkedEntitiesV3/*/language"
                        },
                        {
                            "name": "wikipediaUrl",
                            "source": "/document/linkedEntitiesV3/*/url"
                        },
                        {
                            "name": "bingId",
                            "source": "/document/linkedEntitiesV3/*/bingId"
                        },
                        {
                            "name": "matches",
                            "source": "/document/linkedEntitiesV3/*/matches"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="microsoftskillstextsentimentskill"></a>Microsoft.Skills.Text.SentimentSkill

### <a name="last-available-api-version"></a>Última versión de API disponible

2021-04-30-Preview

### <a name="end-of-support"></a>Finalización del soporte técnico

31 de agosto de 2024

### <a name="recommendations"></a>Recomendaciones 

Use [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md) en su lugar. Proporciona un modelo mejorado e incluye la opción de agregar minería de opiniones u opiniones basadas en aspectos. Como la aptitud es significativamente más compleja, los resultados también son muy diferentes.

Para migrar a [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md), tendrá que realizar uno o varios de los siguientes cambios en la definición de la aptitud. Puede actualizar la definición de aptitud mediante la [API de actualización de conjuntos de aptitudes](/rest/api/searchservice/update-skillset).

> [!NOTE]
> Los resultados de aptitudes para la aptitud de opinión V3 no son compatibles con la definición de índice basada en SentimentSkill. Tendrá que realizar cambios en la definición del índice, el conjunto de aptitudes (entradas de aptitudes posteriores o proyecciones del almacén de conocimiento) y las asignaciones de campos de salida del indexador para reemplazar la aptitud de opinión por la nueva versión.

1. *(Obligatorio)* Cambie `@odata.type` de `"#Microsoft.Skills.Text.SentimentSkill"` a `"#Microsoft.Skills.Text.V3.SentimentSkill"`.

2. *(Obligatorio)* La aptitud de opinión V3 proporciona una puntuación `positive`, `neutral` y `negative` para el texto general y las mismas puntuaciones para cada oración del texto general, mientras que el objeto SentimentSkill anterior solo proporcionaba un único doble que oscilaba entre 0 (negativo) y 1 (positivo) para el texto general. Deberá actualizar la definición de índice para aceptar los tres valores dobles en lugar de una sola puntuación y asegurarse de que todas las entradas de aptitud de nivel inferior, las proyecciones del almacén de conocimiento y las asignaciones de campos de salida sean coherentes con los cambios de nomenclatura.

Se recomienda reemplazar el objeto SentimentSkill anterior por SentimentSkill V3 por completo, actualizar las entradas de aptitud de nivel inferior, las proyecciones del almacén de conocimiento, las asignaciones de campos de salida del indexador y la definición de índice para que coincidan con el nuevo formato de salida y restablecer el indexador para que todos los documentos tengan resultados de opinión coherentes en el futuro.

> [!NOTE]
> Si necesita ayuda adicional para actualizar la canalización de enriquecimiento a fin de usar la versión más reciente de la aptitud de opinión o si no considera restablecer el indexador como opción, abra una [nueva solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en la que podamos trabajar directamente con usted.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Última versión de API disponible

2017-11-11-Preview

### <a name="end-of-support"></a>Finalización del soporte técnico

31 de agosto de 2024

### <a name="recommendations"></a>Recomendaciones 

Use [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) en su lugar. Proporciona la mayor parte de la funcionalidad de NamedEntityRecognitionSkill con una calidad superior. También tiene una información más completa en sus campos de salida compleja.

Para migrar a [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md), tendrá que realizar uno o varios de los siguientes cambios en la definición de la aptitud. Puede actualizar la definición de aptitud mediante la [API de actualización de conjuntos de aptitudes](/rest/api/searchservice/update-skillset).

1. *(Obligatorio)* Cambie `@odata.type` de `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` a `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`.

2. *(Opcional)* Si está usando la salida `entities`, use la salida de colección compleja `namedEntities` de `EntityRecognitionSkill V3` en su lugar. Hay algunos cambios secundarios en los nombres de las propiedades del nuevo resultado complejo `namedEntities`:
    1. `value` ahora se llama `text`
    2. `confidence` ahora se llama `confidenceScore`
    
    Si necesita generar exactamente los mismos nombres de propiedad, deberá agregar un objeto [ShaperSkill](cognitive-search-skill-shaper.md) para volver a dar forma al resultado con los nombres necesarios. Por ejemplo, este objeto ShaperSkill cambia el nombre de las propiedades a sus valores antiguos.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntities",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntities/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntities/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntities/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntities/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntities/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

3. *(Opcional)* Si no especifica explícitamente `categories`, `EntityRecognitionSkill V3` puede devolver un tipo diferente de categorías, además de las que eran compatibles con `NamedEntityRecognitionSkill`. Si no desea este comportamiento, asegúrese de establecer explícitamente el parámetro `categories` en `["Person", "Location", "Organization"]`.

    _Definiciones de migración de ejemplo_

    * Migración simple

        _(Antes) Definición de aptitud NamedEntityRecognition_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(Después) Definición de la aptitud EntityRecognition V3_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * Migración ligeramente complicada

        _(Antes) Definición de aptitud NamedEntityRecognition_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "entities"
                }
            ]
        }
        ```

        _(Después) Definición de la aptitud EntityRecognition V3_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntities",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntities/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntities/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntities/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntities/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntities/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="see-also"></a>Consulte también

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Aptitud de reconocimiento de entidades (V3)](cognitive-search-skill-entity-recognition-v3.md)
+ [Aptitud de opinión (V3)](cognitive-search-skill-sentiment-v3.md)