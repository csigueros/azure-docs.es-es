---
title: Conceptos de proyección
titleSuffix: Azure Cognitive Search
description: Se presentan los conceptos de proyección y los procedimientos recomendados. Si va a crear un almacén de conocimiento en Cognitive Search, las proyecciones determinarán el tipo, la cantidad y la composición de los objetos de Azure Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 1b698ae5b03a7d622e4fccb717789be8cb330057
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561297"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>"Proyecciones" en un almacén de conocimiento en Azure Cognitive Search

Las proyecciones son las tablas, objetos y archivos físicos de un [**almacén de conocimiento**](knowledge-store-concept-intro.md) que aceptan contenido de una canalización de enriquecimiento con IA de Cognitive Search. Si va a crear un almacén de conocimiento, la mayor parte del trabajo consiste en definir y dar forma a las proyecciones.

En este artículo se presentan los conceptos de proyección y flujo de trabajo para que tenga algún conocimiento antes de empezar a codificar.

Las proyecciones se definen en los conjuntos de aptitudes de Cognitive Search, pero los resultados finales son las proyecciones de archivos de imagen, objetos y tablas de Azure Storage.

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Proyecciones expresadas en Azure Storage" border="true":::

## <a name="types-of-projections-and-usage"></a>Tipos de proyecciones y uso

Un almacén de conocimiento es una construcción lógica que se expresa físicamente como una colección dispersa de tablas, objetos JSON o archivos de imagen binarios en Azure Storage.

| Proyección | Storage | Uso |
|------------|---------|-------|
| [Tablas](knowledge-store-projections-examples.md#define-a-table-projection) | Azure Table Storage | Se usa para los datos que se representan mejor como filas y columnas o cuando se necesitan representaciones detalladas de los datos (por ejemplo, como tramas de datos). Las proyecciones de tabla permiten definir una forma esquematizada, mediante una [aptitud Conformador o usar el modelado insertado](knowledge-store-projection-shape.md) para especificar columnas y filas. Puede organizar el contenido en varias tablas en función de los principios de normalización conocidos. Las tablas que están en el mismo grupo se relacionan automáticamente. |
| [Objects](knowledge-store-projections-examples.md#define-an-object-projection) | Azure Blob Storage | Se usa cuando se necesita la representación JSON completa de los datos y los enriquecimientos en un documento JSON. Al igual que con las proyecciones de tabla, solo los objetos JSON válidos se pueden proyectar como objetos y el modelado puede ayudarle a hacerlo. |
| [Archivos](knowledge-store-projections-examples.md#define-a-file-projection) | Azure Blob Storage | Se usa cuando necesita guardar archivos de imagen binarios normalizados. |

## <a name="projection-definition"></a>Definición de proyección

Las proyecciones se especifican en la propiedad "knowledgeStore" de un [conjunto de aptitudes](/rest/api/searchservice/create-skillset). Las definiciones de proyección se usan durante la invocación del indizador para crear y cargar objetos en Azure Storage con contenido enriquecido. Si no está familiarizado con estos conceptos, empiece por el [enriquecimiento con IA](cognitive-search-concept-intro.md) para obtener una introducción.

En el ejemplo siguiente se muestra la ubicación de las proyecciones en knowledgeStore y la construcción básica. El nombre, el tipo y el origen de contenido constituyen una definición de proyección.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [
          { "tableName": "ks-museums-main", "generatedKeyName": "ID", "source": "/document/tableprojection" },
          { "tableName": "ks-museumEntities", "generatedKeyName": "ID","source": "/document/tableprojection/Entities/*" }
        ],
        "objects": [
          { "storageContainer": "ks-museums", "generatedKeyName": "ID", "source": "/document/objectprojection" }
        ],
        "files": [ ]
      }
    ]
```

## <a name="projection-groups"></a>Grupos de proyecciones

Las proyecciones son una matriz de colecciones complejas, lo que significa que puede especificar varios conjuntos de cada tipo. Es habitual usar un solo grupo de proyección, pero puede usar varios si los requisitos de almacenamiento incluyen la compatibilidad con diferentes herramientas y escenarios. Por ejemplo, puede usar un grupo para diseñar y depurar un conjunto de aptitudes, mientras que un segundo conjunto recopila la salida usada para una aplicación en línea y un tercero se usa para las cargas de trabajo de ciencia de datos.

La misma salida del conjunto de aptitudes se usa para rellenar todos los grupos en las proyecciones. El ejemplo siguiente muestra dos.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [],
            "objects": [],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [],
            "files": []
        }
    ]
}
```

Los grupos de proyecciones tienen las siguientes características clave de exclusividad mutua y relación. 

| Principio | Descripción |
|-----------|-------------|
| Exclusividad mutua | Cada grupo está completamente aislado de otros grupos para admitir diferentes escenarios de modelado de datos. Por ejemplo, si va a probar diferentes combinaciones y estructuras de tabla, debería colocar cada conjunto en un grupo de proyección diferente para las pruebas AB. Cada grupo obtiene datos del mismo origen (árbol de enriquecimiento), pero se aísla completamente de la combinación tabla-objeto-archivo de cualquier grupo de proyección del mismo nivel.|
| Relación | En un grupo de proyección, el contenido de las tablas, objetos y archivos está relacionado. El almacén de conocimiento usa claves generadas como puntos de referencia a un nodo primario común. Por ejemplo, considere un escenario en el que tiene un documento que contiene imágenes y texto. Puede proyectar el texto en tablas y las imágenes en archivos binarios y tanto las tablas como los objetos tendrán una columna o propiedad que contiene la dirección URL del archivo.|

## <a name="projection-source"></a>Proyección "origen"

El parámetro de origen es el tercer componente de una definición de proyección. Dado que las proyecciones almacenan datos de una canalización de enriquecimiento con IA, el origen de una proyección siempre es la salida de una aptitud. Por lo tanto, la salida puede ser un único campo (por ejemplo, un campo de texto traducido), pero a menudo es una referencia a una forma de datos.

Las formas de datos proceden del conjunto de aptitudes. Entre todas las aptitudes integradas proporcionadas en Cognitive Search, hay una aptitud de utilidad denominada [**aptitud Conformador**](cognitive-search-skill-shaper.md) que se usa para crear formas de datos. Puede incluir aptitudes de Conformador (tantas como necesite) para admitir las proyecciones en el almacén de conocimiento.

Las formas se usan con frecuencia con proyecciones de tabla, donde la forma no solo especifica las filas que van a la tabla, sino también las columnas que se crean (también puede pasar una forma a una proyección de objeto).

Las formas pueden ser complejas y no procede analizarlas en profundidad aquí, pero en el ejemplo siguiente se muestra brevemente una forma básica. La salida de la aptitud Conformador se especifica como origen de una proyección de tabla. En la propia proyección de tabla habrá columnas para "metadata-storage_path", "reviews_text", "reviews_title", etc., tal y como se especifica en la forma.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
          "name": "reviews_text",
          "source": "/document/reviews_text"
        }, 
        {
          "name": "reviews_title",
          "source": "/document/reviews_title"
        },
        {
          "name": "reviews_username",
          "source": "/document/reviews_username"
        },
    ],
    "outputs": [
      {
        "name": "output",
        "targetName": "mytableprojection"
      }
    ]
}
```

## <a name="projection-lifecycle"></a>Ciclo de vida de las proyecciones

Las proyecciones tienen un ciclo de vida que está asociado a los datos de origen del origen de datos. A medida que los datos se actualizan y se vuelven a indexar, las proyecciones se actualizan con los resultados de los enriquecimientos, lo que garantiza la coherencia de las proyecciones con los datos del origen de datos. Sin embargo, las proyecciones también se almacenan de forma independiente en Azure Storage. Las proyecciones no se eliminarán cuando se eliminen el indexador o el propio servicio de búsqueda. 

## <a name="consume-in-apps"></a>Consumo en aplicaciones

Después de ejecutar el indizador, conéctese a las proyecciones y consuma los datos en otras aplicaciones y cargas de trabajo.

+ Use el [Explorador de almacenamiento](knowledge-store-view-storage-explorer.md) para verificar el contenido y la creación de los objetos.

+ Use [Power BI para la exploración de los datos](knowledge-store-connect-power-bi.md). Esta herramienta funciona mejor cuando los datos están en Azure Table Storage. En Power BI, puede manipular los datos en tablas nuevas que son más fáciles de consultar y analizar.

+ Use datos enriquecidos en un contenedor de blobs de una canalización de ciencia de datos. Por ejemplo, puede [cargar los datos de blobs en un DataFrame de Pandas](/azure/architecture/data-science-process/explore-data-blob).

+ Por último, si tiene que exportar los datos desde el almacén de conocimiento, Azure Data Factory tiene conectores para exportar los datos y colocarlos en la base de datos que elija.

## <a name="checklist-for-getting-started"></a>Lista de comprobación de introducción

Recuerde que las proyecciones son exclusivas de los almacenes de conocimiento y no se usan para estructurar un índice de búsqueda.

1. En Azure Storage, obtenga una cadena de conexión en **Claves de acceso** y compruebe si la cuenta es StorageV2 (de uso general V2).

1. Mientras esté en Azure Storage, familiarícese con el contenido existente de contenedores y tablas para elegir nombres que no entren en conflicto para las proyecciones. Un almacén de conocimiento es una colección dispersa de tablas y contenedores. Considere la posibilidad de adoptar una convención de nomenclatura para realizar un seguimiento de los objetos relacionados.

1. En Cognitive Search, [habilite el almacenamiento en caché de enriquecimiento (versión preliminar)](search-howto-incremental-index.md) en el indizador y, a continuación, [ejecute el indizador](search-howto-run-reset-indexers.md) para ejecutar el conjunto de aptitudes y rellenar la caché. Se trata de una característica en versión preliminar, por lo que debe asegurarse de usar la API de REST en versión preliminar (api-version=2020-06-30-preview o posterior) en la solicitud del indizador. Una vez rellenada la caché, puede modificar las definiciones de proyección en un almacén de conocimiento de forma gratuita (siempre y cuando no se modifiquen las propias aptitudes).

1. En el código, todas las proyecciones se definen únicamente en un conjunto de aptitudes. No hay propiedades de indizador (como asignaciones de campos o asignaciones de campos de salida) que se apliquen a las proyecciones. En una definición de conjunto de aptitudes, se centrará en dos áreas: la propiedad knowledgeStore y la matriz de aptitudes.

   1. En knowledgeStore, especifique las proyecciones de tabla, objeto y archivo en la sección `projections`. El tipo de objeto, el nombre de objeto y la cantidad (según el número de proyecciones que defina) se determinan en esta sección.

   1. En la matriz de aptitudes, determine a qué salidas de aptitud se hará referencia en la propiedad `source` de cada proyección. Todas las proyecciones tienen un origen. El origen puede ser la salida de una aptitud ascendente, pero a menudo es la salida de una aptitud Conformador. La composición de la proyección se determina a través de formas. 

1. Si va a añadir proyecciones a un conjunto de aptitudes existente, [actualícelo](/rest/api/searchservice/update-skillset) y [ejecute el indizador](/rest/api/searchservice/run-indexer).

1. Compruebe los resultados en Azure Storage. En ejecuciones posteriores, evite conflictos de nomenclatura mediante la eliminación de objetos en Azure Storage o el cambio de nombres de proyecto en el conjunto de aptitudes.

## <a name="next-steps"></a>Pasos siguientes

Revise la sintaxis y los ejemplos de cada tipo de proyección.

> [!div class="nextstepaction"]
> [Definición de proyecciones en un almacén de conocimiento](knowledge-store-projections-examples.md)