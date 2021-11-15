---
title: Definición de proyecciones
titleSuffix: Azure Cognitive Search
description: Aprenda a definir proyecciones de tabla, objeto y archivo en un almacén de conocimiento mediante la revisión de la sintaxis y los ejemplos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 0aa980d2268f6d055f3aa05a8e30d57712c022df
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563632"
---
# <a name="define-projections-in-a-knowledge-store"></a>Definición de proyecciones en un almacén de conocimiento

Las [proyecciones](knowledge-store-projection-overview.md) son el componente de una [definición del almacén de conocimiento](knowledge-store-concept-intro.md) que determina cómo se almacena en contenido enriquecido con IA en Azure Storage. Las proyecciones determinan el tipo, la cantidad y la composición de las estructuras de datos que incluyen el contenido.

En este artículo, descubrirá cuál es la sintaxis para cada tipo de proyección:

+ [Proyecciones de tabla](#define-a-table-projection)
+ [Proyecciones de objeto](#define-an-object-projection)
+ [Proyecciones de archivos](#define-a-file-projection)

Las proyecciones se especifican en la propiedad "knowledgeStore" de un conjunto de aptitudes.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [ ],
        "objects": [ ],
        "files": [ ]
      }
    ]
```

Sin necesita más información antes de empezar, consulte las sugerencias y el flujo de trabajo correspondiente en [esta lista de comprobación](knowledge-store-projection-overview.md#checklist-for-getting-started).

> [!TIP]
> Al desarrollar proyecciones, [habilite el almacenamiento en caché de enriquecimiento (versión preliminar)](search-howto-incremental-index.md) para que pueda reutilizar los enriquecimientos existentes al editar las definiciones de proyección.  Se trata de una característica en versión preliminar, por lo que debe asegurarse de usar la API REST en versión preliminar (api-version=2020-06-30-preview o posterior) en la solicitud del indizador. Sin el almacenamiento en caché, las modificaciones sencillas en una proyección darán lugar a un reprocesamiento completo del contenido enriquecido. Al almacenar en caché los enriquecimientos, puede iterar las proyecciones sin incurrir en cargos de procesamiento del conjunto de aptitudes.

## <a name="requirements"></a>Requisitos

Todas las proyecciones tienen propiedades de origen y destino. El origen siempre es contenido de un árbol de enriquecimiento creado durante la ejecución del conjunto de aptitudes. El destino es el nombre y el tipo de objeto que se creará y cargará en Azure Storage.

Excepto para las proyecciones de archivo, que solo aceptan imágenes binarias, el origen debe ser:

+ JSON válido
+ Ruta de acceso a un nodo del árbol de enriquecimiento (por ejemplo, `"source": "/document/objectprojection"`)

Aunque un nodo puede resolverse en un solo campo, una representación más común es una referencia a una forma compleja. Las formas complejas se crean mediante una metodología de forma: bien una [aptitud de conformador](cognitive-search-skill-shaper.md) o [una definición de forma insertada](knowledge-store-projection-shape.md#inline-shape) (normalmente la primera).

Las aptitudes de conformador se favorecen porque la mayoría de las aptitudes no generan un código JSON válido por sí mismas. En muchos casos, las proyecciones de tabla y objeto pueden usar la misma forma de datos creada por una aptitud de conformador.

Dados los requisitos de entrada de origen, saber cómo [dar forma a los datos](knowledge-store-projection-shape.md) se convierte en un requisito práctico para la definición de proyección, especialmente si trabaja con tablas.

## <a name="define-a-table-projection"></a>Definición de una proyección de tabla

Las proyecciones de tabla se recomiendan para escenarios que llaman a la exploración de datos, como el análisis con Power BI o las cargas de trabajo que consumen marcos de datos. La sección de tablas de una matriz de proyecciones es una lista de tablas que desea proyectar.

Para definir una proyección de tabla, se usa la matriz `tables` de la propiedad de proyecciones. Una proyección de tabla tiene tres propiedades necesarias:

| Propiedad | Descripción |
|----------|-------------|
| tableName | Determina el nombre de una nueva tabla creada en Azure Table Storage.  |
| generatedKeyName | El nombre de columna para la clave que identifica de forma única cada fila. El sistema genera este valor. Si omite esta propiedad, se creará automáticamente una columna que usa el nombre de la tabla y la "clave" como la convención de nomenclatura. |
| source | Ruta de acceso a un nodo en un árbol de enriquecimiento. El nodo debe ser una referencia a una forma compleja que determine qué columnas se crean en la tabla.|

En proyecciones de tabla, "source" suele ser la salida de una [aptitud de conformador](cognitive-search-skill-shaper.md) que define la forma de la tabla. Las tablas tienen filas y columnas, y el modelado es el mecanismo por el que se especifican filas y columnas. Puede usar una [aptitud Conformador o formas insertadas](knowledge-store-projection-shape.md). La aptitud de conformador genera un objeto JSON válido, pero el origen podría ser la salida de cualquier aptitud, si el código JSON es válido.

> [!NOTE]
> Las proyecciones de tabla están sujetas a los [límites de almacenamiento](/rest/api/storageservices/understanding-the-table-service-data-model) que impone Azure Storage. El tamaño de la entidad no puede superar 1 MB, y una sola propiedad no puede ser mayor que 64 KB. Estas restricciones hacen que las tablas sean una buena solución para almacenar un gran número de entidades pequeñas.

### <a name="single-table-example"></a>Ejemplo de tabla única

El esquema de una tabla se especifica parcialmente mediante la proyección (nombre y clave de la tabla) y también mediante el origen que proporciona la forma de la tabla (columnas). En este ejemplo se muestra solo una tabla para que pueda centrarse en los detalles de la definición.

```json
"projections" : [
  {
    "tables": [
      { "tableName": "Hotels", "generatedKeyName": "HotelId", "source": "/document/tableprojection" }
    ]
  }
]
```

Las columnas derivan de "source". La siguiente forma de datos que contiene HotelId, HotelName, Category y Description dará lugar a la creación de esas columnas en la tabla.

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
        "name": "Description",
        "source": "/document/Description"
    },
    ],
    "outputs": [
    {
        "name": "output",
        "targetName": "tableprojection"
    }
    ]
}
```

### <a name="multiple-table-slicing-example"></a>Ejemplo de varias tablas (segmentación)

Un patrón común para las proyecciones de tabla es tener varias tablas relacionadas, donde se crean columnas partitionKey y rowKey generadas por el sistema para admitir relaciones entre tablas para todas las tablas del mismo grupo de proyecciones. 

La creación de varias tablas puede ser útil para controlar cómo se agregan los datos relacionados. Si el contenido enriquecido tiene componentes no relacionados o independientes, por ejemplo, las palabras clave extraídas de un documento podrían no estar relacionadas con las entidades reconocidas en el mismo documento, puede dividir esos campos en tablas adyacentes.

Al proyectar en varias tablas, la forma completa se proyecta en cada tabla, a menos que un nodo secundario sea el origen de otra tabla dentro del mismo grupo. Agregar una proyección con una ruta de acceso de origen que sea un elemento secundario de una proyección existente hará que el nodo secundario se segmente fuera del nodo primario y se proyecte en la nueva tabla relacionada. Esta técnica permite definir un único nodo en una aptitud Conformador, que puede ser el origen de todas sus proyecciones.

El patrón para varias tablas consta de:

+ Una tabla como tabla principal o primaria
+ Tablas adicionales para contener segmentos del contenido enriquecido

Por ejemplo, suponga que una aptitud de conformador genera un valor de "EnrichedShape" que contiene información del hotel, además de contenido enriquecido, como frases clave, ubicaciones y organizaciones. La tabla principal incluiría campos que describen el hotel (identificador, nombre, descripción, dirección, categoría). Las frases clave obtendrían la columna de frases clave. Las entidades obtendrían las columnas de entidad.

```json
"projections" : [
  {
    "tables": [
    { "tableName": "MainTable", "generatedKeyName": "HotelId", "source": "/document/EnrichedShape" },
    { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
    { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
    ]
  }
]
```

### <a name="naming-relationships"></a>Nomenclatura de relaciones

Las propiedades `generatedKeyName` y `referenceKeyName` se usan para establecer relaciones entre los datos de distintas tablas o incluso tipos de proyección. Cada fila de la tabla secundaria tiene una propiedad que apunta de vuelta al elemento primario. El nombre de la columna o propiedad en el elemento secundario es el `referenceKeyName` del elemento primario. Cuando no se proporciona el `referenceKeyName`, el servicio usa como valor predeterminado el `generatedKeyName` del elemento primario. 

Power BI usa estas claves generadas para detectar relaciones en las tablas. Si necesita que la columna de la tabla secundaria tenga un nombre diferente, establezca la propiedad `referenceKeyName` en la tabla primaria. Como ejemplo, podría establecer `generatedKeyName` como id. en la tabla tblDocument y `referenceKeyName` como DocumentID. Como resultado, la columna de las tablas tblEntities y tblKeyPhrases que contiene el identificador del documento tendría por nombre DocumentID.

## <a name="define-an-object-projection"></a>Definición de una proyección de objeto

Las proyecciones de objeto son representaciones JSON del árbol de enriquecimiento que pueden proceder de cualquier nodo. En comparación con las proyecciones de tabla, las proyecciones de objeto son más fáciles de definir y se usan al proyectar documentos completos. Las proyecciones de objeto están limitadas a una sola proyección en un contenedor y no se pueden segmentar.

Para definir una proyección de objeto, se usa la matriz `objects` en la propiedad de proyecciones. Una proyección de objeto tiene tres propiedades necesarias:

| Propiedad | Descripción |
|----------|-------------|
| storageContainer | Determina el nombre de un nuevo contenedor creado en Azure Storage.  |
| generatedKeyName | El nombre de columna para la clave que identifica de forma única cada fila. El sistema genera este valor. Si omite esta propiedad, se creará automáticamente una columna que usa el nombre de la tabla y la "clave" como la convención de nomenclatura. |
| origen | La ruta de acceso al nodo del árbol de enriquecimiento que es la raíz de la proyección. El nodo suele ser una referencia a una forma de datos compleja que determina la estructura de blobs.|

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

## <a name="define-a-file-projection"></a>Definición de una proyección de archivo

Las proyecciones de archivo siempre son imágenes binarias y normalizadas, donde la normalización hace referencia a un posible cambio de tamaño y rotación para el uso en la ejecución del conjunto de aptitudes. Las proyecciones de archivo, de forma similar a las proyecciones de objeto, se crean como blobs en Azure Storage y contienen datos binarios (en lugar de código JSON).

Para definir una proyección de archivo, se usa la matriz `files` en la propiedad de proyecciones. Una proyección de archivos tiene tres propiedades necesarias:

| Propiedad | Descripción |
|----------|-------------|
| storageContainer | Determina el nombre de un nuevo contenedor creado en Azure Storage.  |
| generatedKeyName | El nombre de columna para la clave que identifica de forma única cada fila. El sistema genera este valor. Si omite esta propiedad, se creará automáticamente una columna que usa el nombre de la tabla y la "clave" como la convención de nomenclatura. |
| origen | La ruta de acceso al nodo del árbol de enriquecimiento que es la raíz de la proyección. Para los archivos de imágenes, el origen es siempre `/document/normalized_images/*`.  Las proyecciones de archivo solo actúan en la colección `normalized_images`. Ni los indizadores ni un conjunto de aptitudes pasarán a través de la imagen original no normalizada.|

El destino es siempre un contenedor de blobs, con un prefijo de carpeta del valor codificado en base64 del id. del documento. Si hay varias imágenes, se colocan juntas en la misma carpeta. Las proyecciones de archivos no pueden compartir el mismo contenedor que las proyecciones de objetos y deben proyectarse en un contenedor distinto. 

En el ejemplo siguiente se proyectan todas las imágenes normalizadas extraídas del nodo de documento de un documento enriquecido en un contenedor denominado `myImages`.

```json
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
```

## <a name="test-projections"></a>Proyecciones de prueba

Para procesar proyecciones, siga estos pasos:

1. Establezca la propiedad `storageConnectionString` del almacén de conocimiento en una cadena de conexión válida de una cuenta de almacenamiento de uso general V2.  

1. [Actualice el conjunto de aptitudes](/rest/api/searchservice/update-skillset). Para hacerlo, envíe una solicitud PUT con la definición de proyección en el cuerpo del conjunto de aptitudes.

1. [Ejecute el indizador](/rest/api/searchservice/run-indexer) para ejecutar el conjunto de aptitudes. 

1. [Supervise la ejecución del indizador](search-howto-monitor-indexers.md) para comprobar el progreso y detectar los errores.

1. Use el [Explorador de almacenamiento](knowledge-store-view-storage-explorer.md) para verificar la creación de objetos en Azure Storage.

1. Si proyecta tablas, [impórtelas en Power BI](knowledge-store-connect-power-bi.md) para manipular y visualizar tablas. En la mayoría de los casos, Power BI detectará automáticamente las relaciones entre las tablas.

## <a name="common-issues"></a>Problemas comunes

Si se omite cualquiera de los pasos siguientes, se pueden producir resultados inesperados. Compruebe las condiciones siguientes si la salida no parece correcta.

+ Los enriquecimientos de cadenas no tienen el formato de un código JSON válido. Cuando las cadenas están enriquecidas; por ejemplo, `merged_content` se ha enriquecido con frases clave, la propiedad enriquecida se representa como un elemento secundario de `merged_content` en el árbol de enriquecimiento. La representación predeterminada no es código JSON bien formado. En el momento de la proyección, asegúrese de que transforma el enriquecimiento en un objeto JSON válido con un nombre y un valor. El uso de una aptitud de conformador o la definición de formas insertadas ayuda a resolver el problema.

+ Omisión de `/*` al final de una ruta de acceso de origen. Si el origen de una proyección es `/document/projectionShape/keyPhrases`, la matriz de frases clave se proyecta como un solo objeto o fila. En su lugar, establezca la ruta de acceso de origen en `/document/projectionShape/keyPhrases/*` para generar una sola fila u objeto para cada una de las frases clave.

+ Errores de sintaxis de la ruta de acceso. Los [selectores de ruta de acceso](cognitive-search-concept-annotations-syntax.md) distinguen mayúsculas de minúsculas y pueden generar advertencias por entradas que faltan si no se usan las mayúsculas y minúsculas exactas en el selector. 

## <a name="next-steps"></a>Pasos siguientes

En el paso siguiente, se le guiará a lo largo del modelado y la proyección de la salida de un conjunto de aptitudes enriquecido. Si el conjunto de aptitudes es complejo, en el siguiente artículo se proporcionan ejemplos de formas y proyecciones.

> [!div class="nextstepaction"]
> [Ejemplo detallado de formas y proyecciones](knowledge-store-projection-example-long.md)
