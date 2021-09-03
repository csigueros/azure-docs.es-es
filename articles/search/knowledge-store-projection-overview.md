---
title: Conceptos de proyección
titleSuffix: Azure Cognitive Search
description: Dé forma a los datos enriquecidos de la canalización de indexación de enriquecimiento con IA y guárdelos en un almacén de conocimiento para usarlos en escenarios que no sean la búsqueda de texto completo.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 5b28540f30c23abc4ba1d58f6984524984f2c001
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740226"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>"Proyecciones" en un almacén de conocimiento en Azure Cognitive Search

Las proyecciones, un elemento del [almacén de conocimiento](knowledge-store-concept-intro.md), son vistas de documentos enriquecidos que se pueden guardar en el almacenamiento físico con fines de minería de datos de conocimiento. Una proyección le permite "proyectar" los datos en una forma que se adapte a sus necesidades y manteniendo las relaciones para que herramientas como Power BI puedan leer los datos sin ningún trabajo adicional.

Las proyecciones pueden ser tabulares, donde los datos se articulan en filas y columnas en Azure Table Storage, u objetos JSON almacenados en Azure Blob Storage o imágenes binarias también almacenadas en Blob Storage. Puede definir varias proyecciones de los datos a medida que se enriquecen. Estas distintas proyecciones resultan útiles cuando quiere que los mismos datos tengan formas diferentes para casos de uso individuales.

El almacén de conocimiento admite tres tipos de proyecciones:

+ **Tablas**: Para los datos que se representan mejor como filas y columnas, las proyecciones de tabla le permiten definir una forma esquematizada o una proyección en el almacenamiento de tablas. Solo los objetos JSON válidos se pueden proyectar como tablas. Puesto que un documento enriquecido puede contener nodos que no son objetos JSON con nombre, agregará una [aptitud Conformador o usará la forma insertada](knowledge-store-projection-shape.md) en una aptitud para crear un objeto JSON válido. 

+ **Objetos**: si necesita una representación JSON de los datos y enriquecimientos, use proyecciones de objeto para guardar la salida como blobs. Al igual que con las proyecciones de tabla, solo los objetos JSON válidos se pueden proyectar como objetos y el modelado puede ayudarle a hacerlo.

+ **Archivos**: Cuando necesita guardar las imágenes extraídas de los documentos, las proyecciones de archivos le permiten guardar las imágenes normalizadas en el almacenamiento de blobs.

Para ver las proyecciones definidas en contexto, consulte [Creación de un almacén de conocimiento con REST](knowledge-store-create-rest.md).

## <a name="basic-pattern"></a>Patrón básico

Las proyecciones son una matriz de colecciones complejas bajo una definición `knowledgeStore` en un objeto de conjunto de aptitudes. Cada conjunto de tablas, objetos y archivos es un *grupo de proyectos* y puede tener varios grupos si los requisitos de almacenamiento incluyen la compatibilidad con diferentes herramientas y escenarios. Dentro de un único grupo, puede tener múltiples tablas, objetos y archivos. 

Normalmente solo se usa un grupo, pero en el ejemplo siguiente se muestran dos para ilustrar el patrón cuando existen varios grupos.

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

### <a name="projection-groups"></a>Grupos de proyecciones

Tener varios conjuntos de combinaciones de tabla-objeto-archivo es útil para la compatibilidad de distintos escenarios. Puede usar un conjunto para diseñar y depurar un conjunto de aptitudes, y capturar la salida para un examen posterior, mientras que un segundo conjunto recopila la salida usada para una aplicación en línea y un tercero se usa para las cargas de trabajo de ciencia de datos.

Los grupos de proyecciones tienen las siguientes características clave de exclusividad mutua y relación. 

| Principio | Descripción |
|-----------|-------------|
| Exclusividad mutua | Todo el contenido proyectado en un mismo grupo es independiente de los datos proyectados en otros grupos de proyecciones. Esta independencia implica que puede tener los mismos datos repetidos en cada grupo de proyecciones, pero con una forma diferente. Cada grupo obtiene datos del mismo origen (árbol de enriquecimiento), pero se aísla completamente de la combinación tabla-objeto-archivo de cualquier grupo de proyección del mismo nivel.|
| Relación | Las proyecciones admiten la relación dentro del grupo. Dentro de un grupo, el contenido de una tabla está relacionado con el contenido de un objeto o archivo. Entre tipos (tablas, objetos y archivos) del mismo grupo, las relaciones se conservan cuando un único nodo de un árbol de enriquecimiento (por ejemplo, `/document/translated_text`) se proyecta en tablas y objetos diferentes. Dentro de las tablas, las relaciones se basan en una clave generada y cada nodo secundario mantiene una referencia al nodo primario. Por ejemplo, considere un escenario en el que tiene un documento que contiene imágenes y texto. Podría proyectar el texto en tablas u objetos y las imágenes en archivos donde las tablas u objetos tienen una columna o propiedad que contiene la dirección URL del archivo.|

La decisión de crear grupos de proyección adicionales a menudo se basa en los requisitos de representación de datos. Puede hacerlo si desea relaciones de datos diferentes. Dentro de un conjunto, los datos están relacionados, si se da por sentado que las relaciones existen y se pueden detectar. Si crea más conjuntos, los documentos de cada grupo nunca estarán relacionados. Un ejemplo de uso de varios grupos de proyección podría ser el siguiente. Quiere usar los mismos datos proyectados en el sistema en línea y deben representarse de una forma específica, y también quiere que los mismos datos proyectados se utilicen en una canalización de ciencia de datos que se representa de otra forma.

<!-- ## Knowledge Store composition

The knowledge store consists of an annotation cache and projections. The *cache* is used by the service internally to cache the results from skills and track changes. A *projection* defines the schema and structure of the enrichments that match your intended use.

Within Azure Storage, projections can be articulated as tables, objects, or files.

+ As an object, the projection maps to Blob storage, where the projection is saved to a container, within which are the objects or hierarchical representations in JSON for scenarios like a data science pipeline.

+ As a table, the projection maps to Table storage. A tabular representation preserves relationships for scenarios like data analysis or export as data frames for machine learning. The enriched projections can then be easily imported into other data stores. 

You can create multiple projections in a knowledge store to accommodate various constituencies in your organization. A developer might need access to the full JSON representation of an enriched document, while data scientists or analysts might want granular or modular data structures shaped by your skillset.

For instance, if one of the goals of the enrichment process is to also create a dataset used to train a model, projecting the data into the object store would be one way to use the data in your data science pipelines. Alternatively, if you want to create a quick Power BI dashboard based on the enriched documents the tabular projection would work well. -->

## <a name="table-projections"></a>Proyecciones de tabla

Las proyecciones de tabla se recomiendan para escenarios que llaman a la exploración de datos, como el análisis con Power BI. La definición de las tablas es una lista de las tablas que desea proyectar. Cada tabla requiere tres propiedades:

+ tableName: nombre de la tabla en Azure Storage.

+ generatedKeyName: El nombre de columna para la clave que identifica de forma única esta fila.

+ source: El nodo del árbol de enriquecimiento del que obtiene sus enriquecimientos. Este nodo suele ser la salida de una aptitud Conformador que define la forma de la tabla. Las tablas tienen filas y columnas, y el modelado es el mecanismo por el que se especifican filas y columnas. Puede usar una [aptitud Conformador o formas insertadas](knowledge-store-projection-shape.md). La aptitud Conformador genera un objeto JSON válido, pero podría ser la salida de cualquier aptitud, si el JSON es válido. 

Como se muestra en este ejemplo, las entidades y frases clave se modelan en tablas diferentes y contendrán una referencia al elemento primario (MainTable) para cada fila. 

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [
        { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
        { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
        { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
      ]
    },
    {
      "objects": [ ]
    },
    {
      "files": [ ]
    }
  ]
}
```

El nodo de enriquecimiento especificado en "source" se puede segmentar para proyectarse en varias tablas. La referencia a "EnrichedShape" es la salida de una aptitud Conformador (no se muestra). Las entradas de la aptitud determinan la composición de la tabla y las filas que la rellenan. Las claves generadas y los campos comunes de cada tabla proporcionan la base para las relaciones de tabla.

## <a name="object-projections"></a>Proyecciones de objeto

Las proyecciones de objeto son representaciones JSON del árbol de enriquecimiento que pueden proceder de cualquier nodo. En muchos casos, la misma aptitud de conformador que crea una proyección de tabla se puede usar para generar una proyección de objeto. 

Para generar una proyección de objeto se requieren algunos atributos específicos del objeto:

+ storageContainer: El contenedor de blobs donde se guardarán los objetos

+ source: La ruta de acceso al nodo del árbol de enriquecimiento que es la raíz de la proyección

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
          "storageContainer": "hotelreviews", 
          "source": "/document/hotel"
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

## <a name="file-projections"></a>Proyecciones de archivos

Las proyecciones de archivo solo actúan en la colección `normalized_images`, pero son similares a las proyecciones de objeto en que se guardan en un contenedor de blobs, con un prefijo de carpeta del valor codificado en Base64 del id. de documento. 

Las proyecciones de archivos no pueden compartir el mismo contenedor que las proyecciones de objetos y deben proyectarse en un contenedor distinto. Las proyecciones de archivo tienen las mismas propiedades que las proyecciones de objeto:

+ storageContainer: El contenedor de blobs donde se guardarán los objetos

+ source: La ruta de acceso al nodo del árbol de enriquecimiento que es la raíz de la proyección

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [ ]
    },
    {
        "files": [
              {
              "storageContainer": "ReviewImages",
              "source": "/document/normalized_images/*"
            }
        ]
    }
  ]
}
```

## <a name="projection-shaping"></a>Modelado de proyección

Es más fácil definir las proyecciones si tiene un objeto en el árbol de enriquecimiento que coincide con el esquema de la proyección, ya sean tablas u objetos. La capacidad de modelar o estructurar los datos en función del uso planeado normalmente se logra mediante la adición de una [aptitud Conformador](cognitive-search-skill-shaper.md) al conjunto de aptitudes. Las formas producidas a partir de una aptitud Conformador se usan como `source` para una proyección, pero también se pueden usar como entrada para otra aptitud.

Dicho de otro modo, en el caso de las proyecciones de tabla, la aptitud Conformador determina las columnas o campos de las tablas. Las entradas a la aptitud Conformador constan de nodos en un árbol de enriquecimiento. La salida es una estructura que se especifica en la proyección de tabla. En el ejemplo siguiente, una proyección de tabla denominada "mytableprojection" constará de las entradas, tal y como especifica la aptitud Conformador.

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

La aptitud Conformador le permite crear un objeto a partir de diferentes nodos del árbol de enriquecimiento y convertirlos en secundarios de un nuevo nodo. También le permite definir tipos complejos con objetos anidados. Para obtener ejemplos, consulte la documentación de la aptitud [Conformador](cognitive-search-skill-shaper.md).

## <a name="projection-slicing"></a>Segmentación de proyección

Dentro de un grupo de proyecciones de tabla, un único nodo del árbol de enriquecimiento se puede segmentar en varias tablas relacionadas, de modo que cada tabla contenga una categoría de datos. Esto puede ser útil para el análisis, donde puede controlar si se agregan los datos relacionados y de qué manera.

Para crear varias tablas secundarias, comience con la tabla primaria y, a continuación, cree tablas adicionales que se creen a partir del origen del elemento primario. En este ejemplo, "KeyPhrases" y "Entities" toman segmentos de "/document/EnrichedShape".

```json
"tables": [
  { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
  { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
  { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
]
```

Al proyectar a varias tablas, la forma completa se proyectará en cada tabla, a menos que un nodo secundario sea el origen de otra tabla dentro del mismo grupo. Agregar una proyección con una ruta de acceso de origen que sea un elemento secundario de una proyección existente hará que el nodo secundario se segmente fuera del nodo primario y se proyecte en la nueva tabla u objeto relacionado. Esta técnica permite definir un único nodo en una aptitud Conformador, que puede ser el origen de todas sus proyecciones.

## <a name="projection-lifecycle"></a>Ciclo de vida de las proyecciones

Las proyecciones tienen un ciclo de vida que está asociado a los datos de origen del origen de datos. A medida que los datos se actualizan y se vuelven a indexar, las proyecciones se actualizan con los resultados de los enriquecimientos, lo que garantiza la coherencia de las proyecciones con los datos del origen de datos. Sin embargo, las proyecciones también se almacenan de forma independiente en Azure Storage. Las proyecciones no se eliminarán cuando se eliminen el indexador o el propio servicio de búsqueda. 

## <a name="using-projections"></a>Uso de las proyecciones

Tras ejecutar el indexador, puede leer los datos proyectados en los contenedores o tablas que especificó a través de las proyecciones.

En cuanto al análisis, la exploración en Power BI es tan sencilla como establecer Azure Table Storage como origen de datos. Puede crear fácilmente un conjunto de visualizaciones en los datos usando las relaciones que contienen.

Como alternativa, si tiene que usar los datos enriquecidos en una canalización de ciencia de datos, podría [cargar los datos desde los blobs en un DataFrame de Pandas](/azure/architecture/data-science-process/explore-data-blob).

Por último, si tiene que exportar los datos desde el almacén de conocimiento, Azure Data Factory tiene conectores para exportar los datos y colocarlos en la base de datos que elija. 

## <a name="next-steps"></a>Pasos siguientes

Como siguiente paso, cree su primer almacén de conocimiento con instrucciones y datos de ejemplo.

> [!div class="nextstepaction"]
> [Creación de un almacén de conocimiento con REST](knowledge-store-create-rest.md)

Para ver conceptos más avanzados, como la segmentación, la forma insertada y las relaciones, consulte [Definición de proyecciones en un almacén de conocimiento](knowledge-store-projections-examples.md).
