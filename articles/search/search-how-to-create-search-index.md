---
title: Creación de un índice de búsqueda
titleSuffix: Azure Cognitive Search
description: Realización de un índice de búsqueda mediante Azure Portal, las API de REST o un SDK de Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: 2e2b10b5fd42a51951d35f7a1e73e1fcac68ba82
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371683"
---
# <a name="create-a-search-index-in-azure-cognitive-search"></a>Creación de un índice de búsqueda en Azure Cognitive Search

Las solicitudes de consulta en Azure Cognitive Search tienen como objetivo texto que permite búsquedas en un índice de búsqueda. En este artículo, aprenderá los pasos para definir y publicar un índice de búsqueda mediante cualquiera de las modalidades admitidas por Azure Cognitive Search. 

A menos que use un [indizador](search-howto-create-indexers.md), la creación y el relleno de un índice son tareas independientes. Para escenarios que no son de indizador, el siguiente paso después de la creación del índice será la [importación de datos](search-what-is-data-import.md). Para más información, consulte [Índices de búsqueda en Azure Cognitive Search](search-what-is-an-index.md).

## <a name="prerequisites"></a>Prerrequisitos

Los permisos de escritura son necesarios para crear y cargar índices, concedidos a través de una [clave de API de administración](search-security-api-keys.md) en la solicitud. Como alternativa, si participa en la [versión preliminar pública del control de acceso basado en rol](search-security-rbac.md) de Azure Active Directory, puede emitir la solicitud como miembro del rol de colaborador de búsqueda.

La creación de índices es en gran medida un ejercicio de definición de esquema. Antes de crear uno, debe disponer de lo siguiente:

+ Una idea clara de los campos que quiere que se puedan buscar, recuperar, filtrar, clasificar y ordenar en el índice (obtenga más información sobre este aspecto en la [lista de comprobación del esquema](#schema-checklist)).

+ Un identificador único en los datos de origen que se puede usar como clave de documento (o identificador) en el índice.

+ Una ubicación de índice estable. No se admite el traslado de un índice existente a otro servicio de búsqueda de forma inmediata. Revise los requisitos de la aplicación y asegúrese de que el servicio de búsqueda existente, la capacidad y ubicación, son suficientes para sus necesidades.

Por último, todos los niveles de servicio tiene [límites de índice](search-limits-quotas-capacity.md#index-limits) en el número de objetos que se pueden crear. Por ejemplo, si está experimentando en el nivel Gratis, solo puede tener tres índices en un momento dado. Dentro del propio índice, hay límites en el número de campos y colecciones complejos.

## <a name="allowed-updates"></a>Actualizaciones permitidas

[Crear índice](/rest/api/searchservice/create-index) es una operación que crea estructuras de datos físicos (archivos e índices invertidos) en el servicio de búsqueda. La capacidad de realizar cambios mediante la opción [Actualizar índice](/rest/api/searchservice/update-index) depende de si la modificación invalida esas estructuras físicas. La mayoría de los atributos de campo no se pueden cambiar una vez creado el campo en el índice.

Para minimizar el abandono en el proceso de diseño, en la tabla siguiente se describen qué elementos son fijos y flexibles en el esquema. El cambio de un elemento fijo requiere una recompilación del índice, mientras que los elementos flexibles se pueden cambiar en cualquier momento sin afectar a la implementación física. 

| Elemento | ¿Se puede actualizar? |
|---------|-----------------|
| Nombre | No |
| Key | No |
| Tipos y nombres de campo | No |
| Atributos de campo (que se puedan buscar, filtrar, clasificar, ordenar) | No |
| Atributo de campo (recuperable) | Sí |
| [Analyzer](search-analyzers.md) | Puede agregar y modificar analizadores personalizados en el índice. Con respecto a las asignaciones de analizador en campos de cadena, solo puede modificar "searchAnalyzer". Todas las demás asignaciones y modificaciones requieren una recompilación. |
| [Perfiles de puntuación](index-add-scoring-profiles.md) | Sí |
| [Proveedores de sugerencias](index-add-suggesters.md) | No |
| [scripting remoto entre orígenes (CORS)](#corsoptions) | Sí |
| [Cifrado](search-security-manage-encryption-keys.md) | Sí |

> [!NOTE]
> [Los mapas de sinónimos](search-synonyms.md) no forman parte de la definición de un índice. Las modificaciones en un mapa de sinónimos no tienen ningún impacto en el índice de búsqueda físico.

## <a name="schema-checklist"></a>Lista de comprobación de esquema

Use esta lista de comprobación para ayudar a impulsar las decisiones de diseño para el índice de búsqueda.

1. Revise las [convenciones de nomenclatura](/rest/api/searchservice/naming-rules) para que los nombres de índice y campo se ajusten a las reglas de nomenclatura.

1. Consulte los [tipos de datos admitidos](/rest/api/searchservice/supported-data-types). El tipo de datos afectará a cómo se usa el campo. Por ejemplo, el contenido numérico se puede filtrar, pero no se puede buscar el texto completo. El tipo de datos más común es `Edm.String` para el texto que permite búsquedas, que se tokeniza y consulta mediante el motor de búsqueda de texto completo.

1. Identifique un campo en los datos de origen que contenga valores únicos, lo que le permite funcionar como el campo de clave en el índice. Por ejemplo, si va a indexar desde Blob Storage, la ruta de acceso de almacenamiento se suele usar como clave de documento. 

   Cada índice requiere un campo que actúa como *clave de documento* (a veces denominado "id. de documento"). La clave debe asignarse al identificador único de los datos de origen. La capacidad de identificar documentos de búsqueda específicos de forma única es necesaria para recuperar un documento específico en el índice de búsqueda y para el procesamiento selectivo de datos en el nivel por documento.

1. Identifique los campos del origen de datos que aportarán contenido que permite búsquedas en el índice. El contenido que permite búsquedas incluye cadenas cortas o largas que se consultan mediante el motor de búsqueda de texto completo. Si el contenido es detallado (frases pequeñas o fragmentos más grandes), experimente con diferentes analizadores para ver cómo se tokeniza el texto.

   Las [asignaciones de atributos de campo](search-what-is-an-index.md#index-attributes) determinarán los comportamientos de búsqueda y la representación física del índice en el servicio de búsqueda. Determinar cómo se deben especificar los campos es un proceso iterativo para muchos clientes. Para acelerar las iteraciones, comience con datos de ejemplo para que pueda anular y recompilar fácilmente.

1. Identifique qué campos de origen se pueden usar como filtros. El contenido numérico y los campos de texto corto, especialmente los que tienen valores repetidos, son buenas opciones. Al trabajar con filtros, recuerde:

   + Opcionalmente, los campos filtrables se pueden usar en la navegación por facetas.

   + Los campos filtrables se devuelven en orden arbitrario, por lo que considere la posibilidad de que también se puedan ordenar.

## <a name="formulate-a-request"></a>Formular una solicitud

Cuando esté a punto para crear el índice, hay varias maneras de avanzar. Se recomienda Azure Portal o las API REST para realizar las primeras pruebas de desarrollo y de prueba de concepto.

Durante el desarrollo, haga planes para realizar recompilaciones con frecuencia. Como se crean estructuras físicas en el servicio, la mayoría de las modificaciones realizadas requieren [anular los índices y volverlos a crear](search-howto-reindex.md). Considere la posibilidad de trabajar con un subconjunto de los datos para asegurarse de que las recompilaciones van más rápido.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/indexer-portal)

El diseño de índices a través del portal aplica ciertos requisitos y reglas de esquema para tipos de datos específicos, como no permitir funciones de búsqueda de texto completo en campos numéricos. En el portal hay dos opciones para crear un índice de búsqueda: 

+ **Agregar índice** es un editor incrustado para especificar un esquema de índice
+ [**Importar datos**](search-import-data-portal.md) es un asistente

El asistente incluye operaciones adicionales al crear también un indizador, un origen de datos y cargar datos. Si esto es más de lo que desea, solo debe usar **Agregar índice** u otro enfoque.

En la siguiente captura de pantalla se muestra en qué parte de la barra de comandos se pueden encontrar las opciones **Agregar índice** e **Importar datos**. Después de crear un índice, puede encontrarlo de nuevo en la pestaña **Índices**.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Incorporación de un comando de índice" border="true":::

> [!Tip]
> Después de crear un índice en el portal, puede copiar la representación JSON y agregarla al código de la aplicación.

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

[**Crear índice (REST)** ](/rest/api/searchservice/create-index) se usa para crear un índice. Tanto Postman como Visual Studio Code (con una extensión para Azure Cognitive Search) pueden funcionar como un cliente de un índice de búsqueda. Con cualquiera de las dos herramientas puede conectarse al servicio de búsqueda y enviar solicitudes:

+ [Creación de un índice de búsqueda mediante REST y Postman](search-get-started-rest.md)
+ [Introducción a Visual Studio Code y Azure Cognitive Search](search-get-started-vs-code.md)

La API de REST proporciona valores predeterminados para la atribución de campos. Por ejemplo, todos los campos Edm.String permiten búsquedas de manera predeterminada. Los atributos se muestran en su totalidad a continuación con fines ilustrativos, pero puede omitir la atribución en los casos en los que se aplican los valores predeterminados.

Consulte el artículo sobre [operaciones de índice (REST)](/rest/api/searchservice/index-operations) para obtener ayuda con la formulación de solicitudes de índice.

```json
POST https://[servicename].search.windows.net/indexes?api-version=[api-version] 
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "retrievable": true, "searchable": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Address", "type": "Edm.ComplexType", 
      "fields": [
          { "name": "StreetAddress", "type": "Edm.String", "retrievable": true, "filterable": false, "sortable": false, "facetable": false, "searchable": true },
          { "name": "City", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true },
          { "name": "StateProvince", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true }
        ]
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ]
  }
}
```

### <a name="net-sdk"></a>[**SDK de .NET**](#tab/kstore-dotnet)

El SDK de Azure para .NET tiene [**SearchIndexClient**](/dotnet/api/azure.search.documents.indexes.searchindexclient), que tiene métodos para crear y actualizar índices.

```csharp
// Create the index
string indexName = "hotels";
SearchIndex index = new SearchIndex(indexName)
{
    Fields =
    {
        new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
        new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
        new SearchableField("description") { AnalyzerName = LexicalAnalyzerName.EnLucene },
        new SearchableField("descriptionFr") { AnalyzerName = LexicalAnalyzerName.FrLucene }
        new ComplexField("address")
        {
            Fields =
            {
                new SearchableField("streetAddress"),
                new SearchableField("city") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("stateProvince") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("country") { SynonymMapNames = new[] { synonymMapName }, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("postalCode") { IsFilterable = true, IsSortable = true, IsFacetable = true }
            }
        }
    }
};

await indexClient.CreateIndexAsync(index);
```

Para obtener más ejemplos, consulte [azure-search-dotnet-samples/quickstart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11).

### <a name="other-sdks"></a>[**Otros SDK**](#tab/other-sdks)

En el caso de Cognitive Search, los SDK de Azure implementan características disponibles con carácter general. Como tales, puede utilizar cualquiera de los SDK para crear un índice de búsqueda. Todas ellas proporcionan **SearchIndexClient**, que tiene métodos para crear y actualizar índices.

| SDK de Azure | Cliente | Ejemplos |
|-----------|--------|----------|
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Índices](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

---

<a name="corsoptions"></a>

## <a name="set-corsoptions-for-cross-origin-queries"></a>Establecer `corsOptions` para las consultas entre orígenes

Los esquemas de índice incluyen una sección para establecer `corsOptions`. JavaScript del lado cliente no puede llamar a ninguna API de forma predeterminada debido a que el explorador impedirá todas las solicitudes entre orígenes. Para permitir consultas de origen cruzado en el índice, habilite CORS (uso compartido de recursos entre orígenes) estableciendo el atributo **corsOptions**. Por motivos de seguridad, solamente las [API de consulta](search-query-create.md#choose-query-methods) admiten CORS.

```json
"corsOptions": {
  "allowedOrigins": [
    "*"
  ],
  "maxAgeInSeconds": 300
```

Es posible establecer para CORS las siguientes propiedades:

+ **allowedOrigins** (obligatorio): se trata de una lista de orígenes a los que se le concederá acceso a su índice. Esto significa que cualquier código JavaScript que se suministre desde esos orígenes podrá consultar el índice (suponiendo que proporcione la clave de API correcta). Cada origen tiene normalmente el formato `protocol://<fully-qualified-domain-name>:<port>` aunque `<port>` se omite a menudo. Para más información, consulte [Uso compartido de recursos entre orígenes (wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Si desea permitir el acceso a todos los orígenes, incluya `*` como elemento único en la matriz **allowedOrigins**. *Esta práctica no es recomendable para los servicios de búsqueda de producción*, pero a menudo resulta útil para el desarrollo y la depuración.

+ **maxAgeInSeconds** (opcional): los exploradores usan este valor para determinar la duración (en segundos) para almacenar en la memoria caché las respuestas preparatorias de CORS. Esto debe ser un entero no negativo. Cuanto mayor sea este valor es, mejor será el rendimiento, pero más tiempo tardarán en surtir efecto los cambios en la directiva CORS. Si no se establece, se usará una duración predeterminada de 5 minutos.

## <a name="next-steps"></a>Pasos siguientes

Use los vínculos siguientes para familiarizarse con la carga de un índice con datos.

+ [Introducción a la importación de datos](search-what-is-data-import.md)

+ [Adición, actualización o eliminación de documentos (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 