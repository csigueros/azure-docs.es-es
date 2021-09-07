---
title: Dar forma a los datos del almacén de conocimiento
titleSuffix: Azure Cognitive Search
description: Defina las estructuras de datos de un almacén de conocimiento creando formas de datos y pasándolas a una proyección.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 05ba404217fcab9171fad011cedd64cc06557279
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781184"
---
# <a name="shaping-data-for-projection-into-a-knowledge-store"></a>Dar forma a los datos para la proyección en un almacén de conocimiento

En Azure Cognitive Search, "dar forma a los datos" describe un paso en el [flujo de trabajo del almacén de conocimiento](knowledge-store-concept-intro.md) que crea una representación de datos del contenido que quiere proyectar en tablas, objetos y archivos de Azure Storage.

A medida que se ejecutan las aptitudes, las salidas se escriben en un árbol de enriquecimiento en una jerarquía de nodos. Aunque es posible que quiera ver y consumir el árbol de enriquecimiento en su totalidad, es más probable que le interese un nivel más específico y crear subconjuntos de nodos para distintos escenarios, como colocar los nodos relacionados con texto traducido o entidades extraídas en tablas específicas.

Por sí mismo, el árbol de enriquecimiento no incluye lógica que informe de cómo se representa su contenido en un almacén de conocimiento. Las formas de datos rellenan esta carencia proporcionando el esquema de lo que irá en cada proyección de tablas, objetos y archivos. Puede considerar una forma de datos como una definición o vista personalizada de los datos enriquecidos. Puede crear tantas formas como necesite y, luego, asignarlas a proyecciones en una definición de almacén de conocimiento. 

## <a name="approaches-for-creating-shapes"></a>Enfoques para crear formas

Hay dos maneras de dar forma al contenido enriquecido para que se pueda proyectar en un almacén de conocimiento:

+ Usar la [aptitud Conformador](cognitive-search-skill-shaper.md) para crear nodos en un árbol de enriquecimiento que se usen expresamente para la proyección. La mayoría de las aptitudes crean contenido nuevo. Por el contrario, una aptitud Conformador funciona con nodos existentes, normalmente para consolidar varios nodos en un único objeto complejo. Esto es útil para las tablas, donde quiere que la salida de varios nodos se exprese físicamente como columnas de la tabla. 

+ Usar una forma insertada dentro de la propia definición de la proyección.

El uso de la aptitud Conformador externaliza la forma para que la puedan usar varias proyecciones o incluso otras aptitudes. También garantiza que todas las mutaciones del árbol de enriquecimiento se encuentran dentro de la aptitud y que la salida es un objeto que se puede reutilizar. Por el contrario, la forma en línea le permite crear la forma que necesita, pero es un objeto anónimo y solo está disponible para la proyección para la que se define.

Estos enfoques se pueden usar juntos o por separado. En este artículo se muestran ambos: una aptitud Conformador para las proyecciones de tablas y el modelado insertado con la proyección de tablas de frases clave.

## <a name="use-a-shaper-skill"></a>Uso de una aptitud Conformador

Las aptitudes Conformador normalmente se colocan al final de un conjunto de aptitudes, lo que crea una vista de los datos que quiere pasar a una proyección. En este ejemplo se crea una forma denominada "tableprojection" que contiene los siguientes nodos: "reviews_text", "reviews_title", "AzureSearch_DocumentKey" y puntuaciones de opinión y frases clave de las revisiones paginadas. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

### <a name="sourcecontext-property"></a>Propiedad SourceContext

Dentro de una aptitud Conformador, una entrada puede tener un elemento `sourceContext`. Esta misma propiedad también se puede usar en formas insertadas en proyecciones. 

`sourceContext` se usa para construir objetos anidados de varios niveles en una canalización de enriquecimiento. Si la entrada está en un contexto *diferente* al de la aptitud, use *sourceContext*. *sourceContext* exige definir una entrada anidada con el elemento específico considerado como origen. 

En el ejemplo anterior, el análisis de sentimiento y la extracción de frases clave se realizaron sobre texto que se dividió en páginas para facilitar el análisis. Suponiendo que quiera que las puntuaciones y frases se proyecten en una tabla, ahora deberá establecer el contexto en la entrada anidada que proporciona la puntuación y la frase.

### <a name="projecting-a-shape-into-multiple-tables"></a>Proyección de una forma en varias tablas

Con el nodo `tableprojection` definido en la sección `outputs` anterior, puede segmentar partes del nodo `tableprojection` en tablas individuales relacionadas:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

## <a name="inline-shaping-projections"></a>Modelado de proyecciones en línea

El modelado insertado es la capacidad de crear formas dentro de la propia definición de proyección. El modelado insertado tiene estas características:

+ La forma solo la puede usar la proyección que la contiene.
+ La forma puede ser idéntica a la que produciría una aptitud Conformador.

Se crea una forma insertada mediante `sourceContext` y `inputs`.

| Propiedad | Descripción |
|----------|-------------|
| sourceContext | Establece la raíz de la proyección.  |
| inputs | Cada entrada es una columna de la tabla. "name" es el nombre de la columna. "source" es el nodo de enriquecimiento que proporciona el valor. |

Para proyectar los mismos datos que en el ejemplo anterior, la opción de proyección en línea tendría el siguiente aspecto:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Una observación de ambos enfoques es cómo los valores de "Keyphrases" se proyectan mediante "sourceContext". El nodo "Keyphrases", que contiene una colección de cadenas, es en sí mismo un elemento secundario del texto de la página. Sin embargo, dado que las proyecciones requieren un objeto JSON y la página es primitiva (cadena), "sourceContext" se usa para encapsular la frase clave en un objeto con una propiedad con nombre. Esta técnica permite que incluso se proyecten primitivas de forma independiente.

## <a name="next-steps"></a>Pasos siguientes

En este artículo se describen los conceptos y principios de las formas de proyección. Como siguiente paso, vea cómo se aplican en patrones para proyecciones de tablas, objetos y archivos.

> [!div class="nextstepaction"]
> [Definición de proyecciones en un almacén de conocimiento](knowledge-store-projections-examples.md)
