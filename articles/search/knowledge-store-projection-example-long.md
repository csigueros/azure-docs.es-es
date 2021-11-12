---
title: Ejemplos de proyección
titleSuffix: Azure Cognitive Search
description: Explore un ejemplo detallado que proyecte la salida de un conjunto de aptitudes enriquecido en formas complejas que informan de la estructura y composición del contenido en un almacén de conocimiento.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 41960e60479bf553eeb92cebb9888e2ae01aae55
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503079"
---
# <a name="detailed-example-of-shapes-and-projections-in-a-knowledge-store"></a>Ejemplo detallado de formas y proyecciones en un almacén de conocimiento

En este artículo se proporciona un ejemplo detallado que complementa los [conceptos de alto nivel](knowledge-store-projection-overview.md) y los [artículos basados en sintaxis](knowledge-store-projections-examples.md) al recorrer los pasos de creación de formas y proyección necesarios para expresar la salida de un conjunto de aptitudes enriquecido en un [almacén de conocimiento](knowledge-store-concept-intro.md).

Si los requisitos de la aplicación exigen varias aptitudes y proyecciones, este ejemplo puede proporcionarle una mejor idea de cómo forman intersecciones las formas y las proyecciones.

## <a name="download-sample-definitions"></a>Descarga de definiciones de ejemplo

En este ejemplo se usa la [aplicación Postman Desktop](https://www.postman.com/downloads/) y las [API REST de búsqueda](/rest/api/searchservice/).

Clone o descargue [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) en GitHub e importe la [**colección de proyectos**](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) para revisar este ejemplo paso por paso usted mismo.

## <a name="set-up-sample-data"></a>Configuración de datos de ejemplo

Los documentos de ejemplo no están incluidos en la colección Proyecciones, pero los [archivos de datos de demostración de enriquecimiento con IA](https://github.com/azure-search-sample-data/tree/master/ai-enrichment-mixed-media) del [repositorio azure-search-sample-data](https://github.com/Azure-Samples/azure-search-sample-data) contienen texto e imágenes y funcionarán con las proyecciones que se describen en este ejemplo.

Cree un contenedor de blobs en Azure Storage y cargue los 14 elementos.

Mientras esté en Azure Storage, copie una cadena de conexión para que pueda especificarla en la colección de Postman.

## <a name="example-skillset"></a>Conjunto de aptitudes de ejemplo

Para comprender la dependencia entre formas y proyecciones, revise el siguiente conjunto de aptitudes que crea contenido enriquecido. Este conjunto de aptitudes procesa imágenes y texto sin formato, generando salidas a las que se hará referencia en formas y proyecciones.

Preste mucha atención a las salidas de aptitud (targetNames). Se hace referencia a las salidas escritas en el árbol de documentos enriquecidos en proyecciones y formas (a través de aptitudes de conformador).

```json
{
    "name": "projections-demo-ss",
    "description": "Skillset that enriches blob data found in "merged_content". The enrichment granularity is a document.",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "A Cognitive Services resource in the same region as Search.",
        "key": "<COGNITIVE SERVICES All-in-ONE KEY>"
    },
    "knowledgeStore": null
}
```

## <a name="example-shaper-skill"></a>Aptitud de conformador de ejemplo

Una [aptitud de conformador](cognitive-search-skill-shaper.md) es una utilidad para trabajar con contenido enriquecido existente en lugar de crearlo. Agregar un conformador a un conjunto de aptitudes le permite crear una forma personalizada que puede proyectar en Table Storage o Blob Storage. Sin una forma personalizada, las proyecciones se limitan a hacer referencia a un único nodo (una proyección por salida), lo que no es adecuado para las tablas. La creación de una forma personalizada agrega varios elementos a un nuevo conjunto lógico que se puede proyectar como una sola tabla, o que se puede segmentar y distribuir en una colección de tablas. 

En este ejemplo, la forma personalizada combina los metadatos de blobs con las entidades y frases clave identificadas. La forma personalizada se denomina `projectionShape` y tiene como elemento primario `/document`. 

Un propósito de la creación de formas es asegurarse de que todos los nodos de enriquecimiento se expresan en código JSON bien formado, lo cual es necesario para proyectar en el almacén de conocimiento. Esto sucede, sobre todo cuando un árbol de enriquecimiento contiene nodos que no son código JSON bien formado (por ejemplo, cuando un enriquecimiento tiene como elemento primario un tipo primitivo como una cadena).

Observe los dos últimos nodos, `KeyPhrases` y `Entities`. Estos se encapsulan en un objeto JSON válido con `sourceContext`. Este formato es obligatorio, ya que `keyphrases` y `entities` son enriquecimientos de primitivos y deben convertirse en código JSON válido antes de proyectarse.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "projectionShape"
        }
    ]
}
```

### <a name="add-shapers-to-a-skillset"></a>Agregación de conformadores a un conjunto de aptitudes

El conjunto de aptitudes de ejemplo que se presentó al principio de este artículo no incluía la aptitud de conformador, pero las aptitudes de conformador pertenecen a un conjunto de aptitudes y a menudo se colocan hacia el final.

Dentro de un conjunto de aptitudes, una aptitud de conformador podría tener este aspecto:

```json
    "name": "projections-demo-ss",
    "skills": [
        {
            <Shaper skill goes here>
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

## <a name="projecting-to-tables"></a>Proyección en tablas

Basándose en los ejemplos anteriores, hay una cantidad conocida de enriquecimientos y formas de datos a los que se puede hacer referencia en las proyecciones de tabla. En la proyección de tablas siguiente, se definen tres tablas estableciendo las propiedades `tableName`, `source` y `generatedKeyName`.

Las tres tablas se relacionan mediante claves generadas y según el elemento primario `/document/projectionShape` compartido.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "tblDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "tblKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases/*"
                },
                {
                    "tableName": "tblEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/projectionShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

### <a name="test-your-work"></a>Comprobación del trabajo

Puede comprobar definiciones de proyección siguiendo estos pasos:

1. Establezca la propiedad `storageConnectionString` del almacén de conocimiento en una cadena de conexión válida de una cuenta de almacenamiento de uso general V2.  

1. Actualice el conjunto de aptitudes. Para ello, emita la solicitud PUT.

1. Después de actualizar el conjunto de aptitudes, ejecute el indexador. 

Ya tiene una proyección activa con tres tablas. La [importación de estas tablas en Power BI](knowledge-store-connect-power-bi.md) debería permitir que este detecte automáticamente las relaciones.

Antes de pasar al siguiente ejemplo, vamos a volver a los distintos aspectos de la proyección de tabla para conocer la mecánica de la segmentación y relación de datos.

### <a name="slicing-a-table-into-multiple-child-tables"></a>Segmentación de una tabla en varias tablas secundarias

La segmentación es una técnica que subdivide una forma completa consolidada en las partes que la conforman. El resultado consta de tablas independientes, pero relacionadas, con las que se puede trabajar de forma individual.

En el ejemplo, `projectionShape` es la forma consolidada (o el nodo de enriquecimiento). En la definición de la proyección, `projectionShape` se segmenta en tablas adicionales, lo que le permite extraer las partes de la forma, `keyPhrases` y `Entities`. En Power BI, esto es útil, ya que hay varias entidades y keyPhrases asociadas con cada documento, y obtendrá más información si puede ver entidades y keyPhrases como datos con categorías.

La segmentación genera de forma implícita una relación entre las tablas primaria y secundaria, y usa `generatedKeyName` de la tabla primaria para crear una columna con el mismo nombre en la tabla secundaria. 

### <a name="naming-relationships"></a>Nomenclatura de relaciones

Las propiedades `generatedKeyName` y `referenceKeyName` se usan para establecer relaciones entre los datos de distintas tablas o incluso tipos de proyección. Cada fila de la tabla secundaria tiene una propiedad que apunta de vuelta al elemento primario. El nombre de la columna o propiedad en el elemento secundario es el `referenceKeyName` del elemento primario. Cuando no se proporciona el `referenceKeyName`, el servicio usa como valor predeterminado el `generatedKeyName` del elemento primario. 

Power BI usa estas claves generadas para detectar relaciones en las tablas. Si necesita que la columna de la tabla secundaria tenga un nombre diferente, establezca la propiedad `referenceKeyName` en la tabla primaria. Como ejemplo, podría establecer `generatedKeyName` como id. en la tabla tblDocument y `referenceKeyName` como DocumentID. Como resultado, la columna de las tablas tblEntities y tblKeyPhrases que contiene el identificador del documento tendría por nombre DocumentID.

## <a name="projecting-blob-documents"></a>Proyección de documentos de blob

Las proyecciones de objeto son representaciones JSON del árbol de enriquecimiento que pueden proceder de cualquier nodo. En comparación con las proyecciones de tabla, las proyecciones de objeto son más fáciles de definir y se usan al proyectar documentos completos. Las proyecciones de objeto están limitadas a una sola proyección en un contenedor y no se pueden segmentar.

Para definir una proyección de objeto, se usa la matriz `objects` en la propiedad de proyecciones.

El origen es la ruta de acceso a un nodo del árbol de enriquecimiento que es la raíz de la proyección. Aunque no es necesario, la ruta de acceso al nodo suele ser la salida de una aptitud de conformador. Esto se debe a que la mayoría de las aptitudes no generan objetos JSON válidos por sí mismos, lo que significa que es necesario algún tipo de forma. En muchos casos, la misma aptitud de conformador que crea una proyección de tabla se puede usar para generar una proyección de objeto. Como alternativa, el origen también se puede establecer en un nodo con [una creación de formas insertada](knowledge-store-projection-shape.md#inline-shape) para proporcionar la estructura.

El destino siempre es un contenedor de blobs.

En el ejemplo siguiente se proyectan documentos de hotel individuales, un documento de hotel por blob, en un contenedor denominado `hotels`.

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
        "storageContainer": "hotels",
        "source": "/document/objectprojection",
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

El origen es la salida de una aptitud de conformador, denominada "objectprojection". Cada blob tendrá una representación JSON de cada entrada de campo.

```json
    {
      "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
      "name": "#3",
      "description": null,
      "context": "/document",
      "inputs": [
        {
          "name": "HotelId",
          "source": "/document/HotelId"
        },
        {
          "name": "HotelName",
          "source": "/document/HotelName"
        },
        {
          "name": "Category",
          "source": "/document/Category"
        },
        {
          "name": "keyPhrases",
          "source": "/document/HotelId/keyphrases/*"
        },
      ],
      "outputs": [
        {
          "name": "output",
          "targetName": "objectprojection"
        }
      ]
    }
```

## <a name="projecting-an-image-file"></a>Proyección de un archivo de imagen

Las proyecciones de archivo siempre son imágenes binarias y normalizadas, donde la normalización hace referencia a un posible cambio de tamaño y rotación para el uso en la ejecución del conjunto de aptitudes. Las proyecciones de archivo, de forma similar a las proyecciones de objeto, se crean como blobs en Azure Storage y contienen la imagen.

Para definir una proyección de archivo, se usa la matriz `files` en la propiedad de proyecciones.

El origen es siempre `/document/normalized_images/*`. Las proyecciones de archivo solo actúan en la colección `normalized_images`. Ni los indizadores ni un conjunto de aptitudes pasarán a través de la imagen original no normalizada.

El destino es siempre un contenedor de blobs, con un prefijo de carpeta del valor codificado en base64 del id. del documento. Las proyecciones de archivos no pueden compartir el mismo contenedor que las proyecciones de objetos y deben proyectarse en un contenedor distinto. 

En el ejemplo siguiente se proyectan todas las imágenes normalizadas extraídas del nodo de documento de un documento enriquecido en un contenedor denominado `myImages`.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [ ],
            "objects": [ ],
            "files": [
                {
                    "storageContainer": "myImages",
                    "source": "/document/normalized_images/*"
                }
            ]
        }
    ]
}
```

## <a name="projecting-to-multiple-types"></a>Proyección en varios tipos

Un escenario más complejo podría requerir que se proyecte contenido en distintos tipos de proyección. Por ejemplo, proyectar frases clave y entidades en tablas, guardar resultados OCR de texto y texto de diseño como objetos y, a continuación, proyectar las imágenes como archivos. 

Pasos para varios tipos de proyección:

1. Crear una tabla con una fila para cada documento.
1. Crear una tabla relacionada con la tabla de documento, en la que cada frase clave se identificará como una fila en la tabla.
1. Crear una tabla relacionada con la tabla de documento, en la que cada entidad se identificará como una fila en la tabla.
1. Crear una proyección de objeto con el texto de diseño de cada imagen.
1. Crear una proyección de archivo en la que se proyecta cada imagen extraída.
1. Crear una tabla de referencias cruzadas que contenga referencias a la tabla de documentos, la proyección de objeto con el texto de diseño y la proyección de archivo.

### <a name="shape-data-for-cross-projection"></a>Dar forma a datos para la proyección cruzada

Para obtener las formas que necesitamos para estas proyecciones, empiece por agregar una nueva aptitud Conformador que cree un objeto con forma llamado `crossProjection`. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCrossProjection",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Definición de proyecciones de tabla, objeto y archivo

Desde el objeto crossProjection consolidado, segmente el objeto en varias tablas, capture la salida de OCR como blobs y, después, guarde la imagen como archivos (también en Blob Storage).

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [
                {
                    "tableName": "crossDocument",
                    "generatedKeyName": "Id",
                    "source": "/document/crossProjection"
                },
                {
                    "tableName": "crossEntities",
                    "generatedKeyName": "EntityId",
                    "source": "/document/crossProjection/entities/*"
                },
                {
                    "tableName": "crossKeyPhrases",
                    "generatedKeyName": "KeyPhraseId",
                    "source": "/document/crossProjection/keyPhrases/*"
                },
                {
                    "tableName": "crossReference",
                    "generatedKeyName": "CrossId",
                    "source": "/document/crossProjection/images/*"
                }
                    
            ],
            "objects": [
                {
                    "storageContainer": "crossobject",
                    "generatedKeyName": "crosslayout",
                    "source": null,
                    "sourceContext": "/document/crossProjection/images/*/layoutText",
                    "inputs": [
                        {
                            "name": "OcrLayoutText",
                            "source": "/document/crossProjection/images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": [
                {
                    "storageContainer": "crossimages",
                    "generatedKeyName": "crossimages",
                    "source": "/document/crossProjection/images/*/image"
                }
            ]
        }
    ]
}
```

Las proyecciones de objeto requieren un nombre de contenedor para cada proyección. Las proyecciones de objeto o archivo no pueden compartir un contenedor. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relaciones entre las proyecciones de tabla, objeto y archivo

En este ejemplo también se resalta otra característica de las proyecciones. Al definir varios tipos de proyecciones en el mismo objeto de proyección, existe una relación expresada en y entre los distintos tipos (tablas, objetos, archivos). Esto le permite empezar con una fila de tabla para un documento y buscar todo el texto de OCR de las imágenes de ese documento en la proyección de objeto. 

Si no desea los datos relacionados, defina las proyecciones en distintos grupos de proyección. Por ejemplo, el siguiente fragmento provocará que las tablas estén relacionadas, pero sin relaciones entre las tablas y las proyecciones de objeto (texto de OCR). 

Los grupos de proyección son útiles cuando se quieren proyectar los mismos datos en diferentes formas con distintas necesidades. Por ejemplo, un grupo de proyección para el panel de Power BI y otro para capturar los datos que se usan para entrenar un modelo de Machine Learning encapsulado en una aptitud personalizada.

Cuando se crean proyecciones de distintos tipos, primero se generan las proyecciones de archivo y objeto, y se agregan rutas de acceso a las tablas.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "unrelatedDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "unrelatedKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases"
                }
            ],
            "objects": [
                
            ],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "unrelatedocrtext",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/text",
                    "inputs": [
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                    ]
                },
                {
                    "storageContainer": "unrelatedocrlayout",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/layoutText",
                    "inputs": [
                        {
                            "name": "ocrLayoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": []
        }
    ]
}
```

## <a name="next-steps"></a>Pasos siguientes

El ejemplo de este artículo muestra patrones comunes sobre cómo crear proyecciones. Ahora que conoce los conceptos, está mejor preparado para compilar proyecciones para su escenario concreto.

> [!div class="nextstepaction"]
> [Configuración del almacenamiento en caché para el enriquecimiento incremental](search-howto-incremental-index.md)