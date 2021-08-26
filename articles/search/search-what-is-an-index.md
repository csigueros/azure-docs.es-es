---
title: Creación de un índice
titleSuffix: Azure Cognitive Search
description: Incluye conceptos y herramientas de indexación en Azure Cognitive Search, entre otras las definiciones de esquema y la estructura física de los datos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: cdfadc895de3af0f79c30a067f3e5376bfa8873b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769107"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Creación de índices de búsqueda en Azure Cognitive Search

Cognitive Search almacena contenido que permite búsquedas y se usa para las consultas filtradas y de texto completo en un *índice de búsqueda*. Un índice se define mediante un esquema y se guarda en el servicio y, a continuación, se realiza la importación como segundo paso. 

Los índices contienen *documentos de búsqueda*. Desde un punto de vista conceptual, un documento es una sola unidad de datos habilitada para búsquedas en el índice. Un minorista podría tener un documento para cada objeto que vende, una organización de noticias podría tener un documento para cada artículo, etc. Estos conceptos se pueden asignar a conceptos equivalentes de bases de datos más conocidos: un *índice de búsqueda* equivale a una *tabla* y los *documentos* son más o menos equivalentes a las *filas* de una tabla.

## <a name="whats-an-index-schema"></a>¿Qué es un esquema de índice?

El esquema determina la estructura física de un índice. La recopilación de "campos" es normalmente el elemento más grande de un índice, en el que cada campo recibe un nombre y se le asigna un [tipo de datos](/rest/api/searchservice/Supported-data-types) y unos atributos con los comportamientos permitidos que determinan cómo se usa.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Otros elementos están contraídos por motivos de brevedad, pero los siguientes vínculos pueden proporcionar [proveedores de sugerencias](index-add-suggesters.md), [perfiles de puntuación](index-add-scoring-profiles.md), [analizadores](search-analyzers.md) que se utilizan para procesar cadenas en tokens de acuerdo con las reglas lingüísticas u otras características admitidas por el analizador y los valores de [scripting remoto entre orígenes (CORS)](#corsoptions).

## <a name="choose-a-client"></a>Elección de un cliente

Hay varias maneras de crear un índice de búsqueda. Se recomienda Azure Portal o los SDK en las primeras fases de desarrollo y con las pruebas de concepto.

Durante el desarrollo, haga planes para realizar recompilaciones con frecuencia. Como se crean estructuras físicas en el servicio, la mayoría de las modificaciones realizadas en una definición de campo existente, requieren [quitar los índices y volverlos a crear](search-howto-reindex.md). Considere la posibilidad de trabajar con un subconjunto de los datos para asegurarse de que las recompilaciones van más rápido.

### <a name="permissions"></a>Permisos

Todas las operaciones relacionadas con el índice de búsqueda, incluidas las solicitudes GET o definición, requieren que la solicitud tenga una [clave de API de administración](search-security-api-keys.md).

### <a name="limits"></a>Límites

Todos los [niveles de servicio limitan](search-limits-quotas-capacity.md#index-limits) el número de objetos que se pueden crear. Si está experimentando en el nivel Gratis, solo puede tener tres índices en un momento dado.

### <a name="use-azure-portal-to-create-a-search-index"></a>Use Azure Portal para crear un índice de búsqueda

El portal proporciona dos opciones para crear un índice de búsqueda: El [**Asistente para la importación de datos**](search-import-data-portal.md) y la opción **Agregar índice**, que proporciona los campos necesarios para especificar un esquema de índice. El asistente incluye operaciones adicionales al crear también un indizador, un origen de datos y cargar datos. Si es mayor que lo que desea, solo debe usar **Agregar índice** u otro enfoque.

En la siguiente captura de pantalla se muestra en qué parte del portal se pueden encontrar la opción **Agregar índice**. **Importar datos** es la puerta derecha siguiente.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Incorporación de un comando de índice" border="true":::

> [!Tip]
> El diseño de índices a través del portal aplica ciertos requisitos y reglas de esquema para tipos de datos específicos, como no permitir funciones de búsqueda de texto completo en campos numéricos. Una vez que tenga un índice viable, puede copiar el archivo JSON desde el portal y agregarlo a la solución.

### <a name="use-a-rest-client"></a>Uso de un cliente REST

Tanto Postman como Visual Studio Code (con una extensión para Azure Cognitive Search) pueden funcionar como un cliente de un índice de búsqueda. Con cualquiera de las dos herramientas puede conectarse al servicio de búsqueda y enviar solicitudes [Crear índices (REST)](/rest/api/searchservice/create-index). Hay numerosos tutoriales y ejemplos en los que se pueden encontrar clientes REST para crear objetos. 

Para más información acerca de los distintos clientes, puede empezar por leer estos artículos:

+ [Creación de un índice de búsqueda mediante REST y Postman](search-get-started-rest.md)
+ [Introducción a Visual Studio Code y Azure Cognitive Search](search-get-started-vs-code.md)

Consulte el artículo sobre [operaciones de índice (REST)](/rest/api/searchservice/index-operations) para obtener ayuda con la formulación de solicitudes de índice.

### <a name="use-an-sdk"></a>Uso de un SDK

En el caso de Cognitive Search, los SDK de Azure implementan características disponibles con carácter general. Como tales, puede utilizar cualquiera de los SDK para crear un índice de búsqueda. Todas ellas proporcionan **SearchIndexClient**, que tiene métodos para crear y actualizar índices.

| SDK de Azure | Cliente | Ejemplos |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [azure-search-dotnet-samples/quickstart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Índices](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>Definición de campos

La colección `fields` define un documento de búsqueda. Necesitará campos para las consultas y las claves. Probablemente también necesitará campos para admitir filtros, facetas y ordenaciones. Es posible que también necesite campos para los datos que el usuario nunca ve, por ejemplo, puede que desee campos para los márgenes de beneficios o las promociones de marketing que puede usar para modificar el intervalo de búsqueda.

Un campo de tipo Edm.String se debe diseñar como la clave del documento. Se usa para identificar de forma única cada documento de búsqueda y distingue mayúsculas de minúsculas. Puede recuperar un documento por su clave para rellenar una página de detalles.

Si los datos de entrada son jerárquicos por naturaleza, asigne el [tipo complejo](search-howto-complex-data-types.md) de tipo de datos para representar las estructuras anidadas. El conjunto de datos de ejemplo integrado, Hotels, muestra tipos complejos mediante una dirección (contiene varios subcampos) que tiene una relación de uno a uno con cada hotel y una colección compleja de habitaciones, en la que hay varias habitaciones asociadas con cada hotel. 

Asigne cualquier analizador a los campos de cadena antes de que se cree el índice. Haga lo mismo para los proveedores de sugerencias si desea habilitar Autocomplete en campos específicos.

<a name="index-attributes"></a>

### <a name="attributes"></a>Atributos

Los atributos de campo determinan cómo se usa un campo, por ejemplo, si se usa en la búsqueda de texto completo, la navegación por facetas, las operaciones de ordenación, etc. 

Los campos de cadena se suelen marcar como "buscables" y "recuperables". Los campos que se usan para restringir los resultados de búsqueda incluyen "ordenable", "filtrable" y "clasificable".

|Atributo|Descripción|  
|---------------|-----------------|  
|"buscable" |Permite realizar búsquedas de texto completo, sujetas a análisis léxico, como la separación de palabras durante la indexación. Si establece un campo buscable en un valor como "día soleado", internamente se dividirá en los tokens individuales "soleado" y "día". Para obtener detalles, vea [Búsqueda de texto completo](search-lucene-query-architecture.md).|  
|"filtrable" |Se hace referencia en consultas $filter. Los campos filtrables de tipo `Edm.String` o `Collection(Edm.String)` no sufren separación de palabras, por lo que las comparaciones son solo de coincidencias exactas. Por ejemplo, si establece un campo de este tipo en "día soleado", `$filter=f eq 'sunny'` no encontrará ninguna coincidencia, pero `$filter=f eq 'sunny day'` sí. |  
|"ordenable" |De forma predeterminada, el sistema ordena los resultados por puntuación, pero puede configurar la ordenación en función de los campos de los documentos. Los campos de tipo `Collection(Edm.String)` no pueden ser "ordenables". |  
|"clasificable" |Normalmente se usa en una presentación de resultados de búsqueda que incluye un recuento de visitas por categoría (por ejemplo, hoteles de una ciudad concreta). Esta opción no puede utilizarse con campos de tipo `Edm.GeographyPoint`. Los campos de tipo `Edm.String` que son "filtrables", "ordenables" o "clasificables" pueden tener como máximo 32 kilobytes de longitud. Para obtener detalles, vea [Creación de un índice de Búsqueda de Azure con la API de REST](/rest/api/searchservice/create-index).|  
|"clave" |Identificador único de los documentos del índice. Es necesario elegir exactamente un campo como campo de clave, y debe ser de tipo `Edm.String`.|  
|"recuperable" |Determina si el campo se puede devolver en un resultado de búsqueda. Esto resulta útil cuando se quiere usar un campo (por ejemplo, *margen de beneficio*) como mecanismo de filtrado, ordenación o puntuación, pero no se quiere que sea visible para el usuario final. Este atributo debe ser `true` for `key` .|  

Aunque puede agregar nuevos campos en cualquier momento, las definiciones de campo existentes se bloquean durante la vigencia del índice. Por este motivo, los desarrolladores suelen usar el portal para crear índices sencillos, probar ideas o emplear las páginas del portal para buscar una configuración. La iteración frecuente de un diseño de índice es más eficaz si se sigue un enfoque basado en código para poder volver a crear el índice fácilmente.

> [!NOTE]
> Las API que usa para crear un índice tienen distintos comportamientos predeterminados. En el caso de las [API REST](/rest/api/searchservice/Create-Index), la mayoría de los atributos están habilitados de forma predeterminada (por ejemplo, "buscable" y "recuperable" son verdaderos para los campos de cadena), y a menudo solo tiene que establecerlos si desea desactivarlos. Para el SDK de .NET, ocurre lo contrario. En cualquier propiedad que no establezca de forma explícita, el valor predeterminado es que el comportamiento de búsqueda correspondiente esté deshabilitado, a menos que lo habilite específicamente.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Atributos y tamaño de índice (implicaciones de almacenamiento)

El tamaño de un índice viene determinado por el tamaño de los documentos que se cargan, además de la configuración del índice, por ejemplo, si incluye los proveedores de sugerencias y cómo establece los atributos en los campos individuales. 

La siguiente captura de pantalla ilustra los patrones de almacenamiento de índices resultantes de diversas combinaciones de atributos. El índice se basa en el **índice de ejemplo de bienes inmuebles**, que puede crear fácilmente mediante el Asistente para la importación de datos. Aunque no se muestran los esquemas de índice, puede deducir los atributos según el nombre del índice. Por ejemplo, el índice *realestate-searchable* tiene seleccionado el atributo "buscable" y nada más, el índice *realestate-retrievable* tiene seleccionado el atributo "recuperable" y nada más y así sucesivamente.

![Tamaño del índice basado en la selección de atributos](./media/search-what-is-an-index/realestate-index-size.png "Tamaño del índice basado en la selección de atributos")

Aunque estas variantes de índice son artificiales, se puede hacer referencia a ellas en comparaciones más amplias de cómo los atributos afectan al almacenamiento. ¿El valor "recuperable" aumenta el tamaño del índice? No. ¿Agregar campos a un **proveedor de sugerencias** aumenta el tamaño del índice? Sí. 

Hacer que un campo se pueda filtrar u ordenar también aumenta el consumo de almacenamiento porque los campos filtrados y ordenados no se tokenizan para que las secuencias de caracteres puedan coincidir literalmente.

Tampoco se refleja en la tabla anterior el impacto de los [analizadores](search-analyzers.md). Si usa el tokenizador edgeNgram para almacenar secuencias textuales de caracteres (a, AB, ABC, ABCD), el tamaño del índice será mayor que si utiliza un analizador estándar.

> [!Note]
> La arquitectura de almacenamiento se considera un detalle de implementación de Azure Cognitive Search y podría cambiar sin previo aviso. No hay ninguna garantía de que el comportamiento actual se conserve en el futuro.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>Acerca de `corsOptions`

Los esquemas de índice incluyen una sección para establecer `corsOptions`. JavaScript del lado cliente no puede llamar a ninguna API de forma predeterminada debido a que el explorador impedirá todas las solicitudes entre orígenes. Para permitir consultas de origen cruzado en el índice, habilite CORS (uso compartido de recursos entre orígenes) estableciendo el atributo **corsOptions**. Por motivos de seguridad, solamente las API de consulta admiten CORS. 

Se pueden establecer las opciones siguientes para CORS:

+ **allowedOrigins** (obligatorio): se trata de una lista de orígenes a los que se le concederá acceso a su índice. Esto significa que cualquier código JavaScript que se suministre desde esos orígenes podrá consultar el índice (suponiendo que proporcione la clave de API correcta). Cada origen tiene normalmente el formato `protocol://<fully-qualified-domain-name>:<port>` aunque `<port>` se omite a menudo. Para más información, consulte [Uso compartido de recursos entre orígenes (wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Si desea permitir el acceso a todos los orígenes, incluya `*` como elemento único en la matriz **allowedOrigins**. *Esto no es recomendable para los servicios de búsqueda de producción* pero a menudo resulta útil para el desarrollo y la depuración.

+ **maxAgeInSeconds** (opcional): los exploradores usan este valor para determinar la duración (en segundos) para almacenar en la memoria caché las respuestas preparatorias de CORS. Esto debe ser un entero no negativo. Cuanto mayor sea este valor es, mejor será el rendimiento, pero más tiempo tardarán en surtir efecto los cambios en la directiva CORS. Si no se establece, se usará una duración predeterminada de 5 minutos.

## <a name="next-steps"></a>Pasos siguientes

Puede obtener experiencia práctica en la creación de un índice con casi cualquier ejemplo o tutorial para Cognitive Search. En el caso de los iniciadores, puede elegir cualquiera de las guías de inicio rápido de la tabla de contenido.

Pero también querrá familiarizarse con las metodologías para cargar un índice con datos. Las estrategias de definición de índices y de importación de datos se definen en tándem. En los artículos siguientes se proporciona más información sobre la carga de un índice.

+ [Introducción a la importación de datos](search-what-is-data-import.md)

+ [Adición, actualización o eliminación de documentos (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 