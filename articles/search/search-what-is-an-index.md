---
title: Introducción a los índices
titleSuffix: Azure Cognitive Search
description: Explica qué es un índice de búsqueda en Azure Cognitive Search y describe el contenido, la construcción, la expresión física y el esquema del índice.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 51b075dbce189370cf502bce6d46471da6c58348
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490542"
---
# <a name="indexes-in-azure-cognitive-search"></a>Índices en Azure Cognitive Search

En Azure Cognitive Search, un *índice de búsqueda* es el contenido que se puede buscar, disponible para el motor de búsqueda para la indexación, la búsqueda de texto completo y las consultas filtradas. Un índice se define mediante un esquema y se guarda en el servicio de búsqueda. A continuación, se realiza la importación de datos como segundo paso. Este contenido existe en el servicio de búsqueda aparte de los almacenes de datos principales, ya que es necesario que sea así para los tiempos de respuesta de milisegundos que se esperan de las operaciones modernas. Excepto en escenarios de indexación específicos, el servicio de búsqueda nunca se conectará con los datos locales ni los consultará.

Si va a crear y administrar un índice de búsqueda, este artículo le ayudará a comprender lo siguiente:

+ Contenido (documentos y esquema)
+ Representación física
+ Operaciones básicas

¿Prefiere ponerse manos a la obra directamente? En ese caso, consulte [Creación de un índice de búsqueda](search-how-to-create-search-index.md).

## <a name="content-of-a-search-index"></a>Contenido de un índice de búsqueda

En Cognitive Search, los índices contienen *documentos de búsqueda*. Desde un punto de vista conceptual, un documento es una sola unidad de datos habilitada para búsquedas en el índice. Por ejemplo, un distribuidor podría tener un documento para cada producto, una organización de noticias podría tener un documento para cada artículo, un sitio de viajes podría tener un documento para cada hotel y destino, etc. Estos conceptos se pueden asignar a conceptos equivalentes de bases de datos más conocidos: un *índice de búsqueda* equivale a una *tabla* y los *documentos* son más o menos equivalentes a las *filas* de una tabla.

La estructura de un documento viene determinada por el esquema del índice, como se muestra a continuación. La colección de "campos" es, normalmente, el elemento más grande de un índice, en el que cada campo recibe un nombre y se le asigna un [tipo de datos](/rest/api/searchservice/Supported-data-types) y unos atributos con los comportamientos permitidos que determinan cómo se usa.

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

Otros elementos están contraídos por motivos de brevedad, pero los vínculos siguientes pueden proporcionar los detalles: 

+ Los [proveedores de sugerencias](index-add-suggesters.md) admiten consultas de escritura anticipada, como la función de autocompletar.
+ Los [perfiles de puntuación](index-add-scoring-profiles.md) se usan para el ajuste por relevancia.
+ Los [analizadores](search-analyzers.md) se usan para procesar cadenas en tokens según determinadas reglas lingüísticas u otras características que admite el analizador.
+ El [scripting remoto entre orígenes (CORS)](search-how-to-create-search-index.md#corsoptions) se usa para aplicaciones que emiten solicitudes desde distintos dominios.
+ La [clave de cifrado](search-security-manage-encryption-keys.md) se usa para el doble cifrado de contenido confidencial en el índice.

### <a name="field-definitions"></a>Definiciones de campo

La colección de "campos" define los documentos de búsqueda. Necesitará campos para la identificación de documentos (claves), almacenar texto que permite búsquedas y campos para filtros, facetas y ordenaciones de apoyo. Es posible que también necesite campos para los datos que los usuarios no ven. Por ejemplo, puede que desee campos para los márgenes de beneficios o para las promociones de marketing que puede usar para modificar el rango de búsqueda.

Si los datos entrantes son jerárquicos por naturaleza, puede representarlos dentro de un índice como un [tipo complejo](search-howto-complex-data-types.md) que se usa para representar estructuras anidadas. El conjunto de datos de ejemplo integrado, Hotels, muestra tipos complejos mediante una dirección (contiene varios subcampos) que tiene una relación de uno a uno con cada hotel y una colección compleja de habitaciones, en la que hay varias habitaciones asociadas con cada hotel. 

<a name="index-attributes"></a>

### <a name="field-attributes"></a>Atributos de campo

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

## <a name="physical-representation"></a>Representación física

En Azure Cognitive Search, la estructura física de un índice es, en gran medida, una implementación interna. Puede acceder a su esquema, consultar su contenido, supervisar su tamaño y administrar la capacidad, pero los propios clústeres (índices, particiones y otros archivos y carpetas) están fuera de los límites y Microsoft los administra internamente en su nombre.

El tamaño de un índice viene determinado por:

+ Cantidad y composición de los documentos
+ Configuración del índice (en concreto, si incluye los proveedores de sugerencias)
+ Atributos en campos individuales

Puede supervisar el tamaño del índice en la pestaña Índices de Azure Portal o mediante la emisión de una [solicitud GET INDEX](/rest/api/searchservice/get-index) en el servicio de búsqueda.

### <a name="factors-influencing-index-size"></a>Factores que influyen en el tamaño del índice

La composición y la cantidad de los documentos se determinarán según lo que decida importar. Recuerde que un índice de búsqueda solo debe incluir contenido que admita búsquedas. Si los documentos de origen incluyen campos binarios, normalmente se omitirían del esquema de índice (a menos que use el enriquecimiento con IA para descifrar y analizar el contenido para crear información que admita las búsquedas de texto).

La configuración del índice puede incluir otros componentes, además de documentos, como proveedores de sugerencias, analizadores de clientes, perfiles de puntuación, configuración de CORS e información de clave de cifrado. En la lista anterior, el único componente que puede afectar al tamaño del índice son los proveedores de sugerencias. Los [**proveedores de sugerencias**](index-add-suggesters.md) son construcciones que admiten consultas de estructura anticipada o con la función de autocompletar. Por lo tanto, cuando se incluye un proveedor de sugerencias, el proceso de indexación crea las estructuras de datos necesarias para las coincidencias textuales de caracteres. Los proveedores de sugerencias se implementan en el nivel de campo, así que elija solo los campos que sean razonables para el tipo por adelantado.

Los atributos de campo son la tercera consideración del tamaño del índice. Los atributos determinan los comportamientos. Para admitir esos comportamientos, el proceso de indexación creará las estructuras de datos compatibles. Por ejemplo, "searchable" invoca la [búsqueda de texto completo](search-lucene-query-architecture.md), que examina los índices invertidos para el término con tokens. Por el contrario, un atributo "filtrable" u "ordenable" admite la iteración en cadenas sin modificar.

### <a name="example-demonstrating-the-storage-implications-of-attributes-and-suggesters"></a>Ejemplo que muestra las implicaciones de almacenamiento de atributos y proveedores de sugerencias

La siguiente captura de pantalla ilustra los patrones de almacenamiento de índices resultantes de diversas combinaciones de atributos. El índice se basa en el **índice de ejemplo de bienes inmuebles**, que puede crear fácilmente mediante el Asistente para la importación de datos y los datos de ejemplo integrados. Aunque no se muestran los esquemas de índice, puede deducir los atributos según el nombre del índice. Por ejemplo, el índice *realestate-searchable* tiene seleccionado el atributo "buscable" y nada más, el índice *realestate-retrievable* tiene seleccionado el atributo "recuperable" y nada más y así sucesivamente.

![Tamaño del índice basado en la selección de atributos](./media/search-what-is-an-index/realestate-index-size.png "Tamaño del índice basado en la selección de atributos")

Aunque estas variantes de índice son, de algún modo, artificiales, se puede hacer referencia a ellas para obtener comparaciones más amplias de cómo los atributos afectan al almacenamiento. ¿El valor "recuperable" aumenta el tamaño del índice? No. ¿Agregar campos a un **proveedor de sugerencias** aumenta el tamaño del índice? Sí. 

Hacer que un campo se pueda filtrar u ordenar también aumenta el consumo de almacenamiento porque los campos filtrados y ordenados no se tokenizan para que las secuencias de caracteres puedan coincidir literalmente.

Tampoco se refleja en la tabla anterior el impacto de los [analizadores](search-analyzers.md). Si usa el tokenizador edgeNgram para almacenar secuencias textuales de caracteres (a, AB, ABC, ABCD), el tamaño del índice será mayor que si utiliza un analizador estándar.

## <a name="basic-operations"></a>Operaciones básicas

Ahora que tiene una idea mejor de lo que es un índice, en esta sección se presentan las operaciones en tiempo de ejecución del índice, incluida la conexión a un solo índice y su protección.

### <a name="index-isolation"></a>Aislamiento del índice
  
En Cognitive Search, trabajará con un índice a la vez, donde todas las operaciones relacionadas con el índice tienen como destino un único índice. No hay ningún concepto de índices relacionados ni unión de índices independientes para la indexación o la consulta. 

Al administrar un índice, tenga en cuenta que no hay compatibilidad con el portal o la API para mover o copiar un índice. En su lugar, los clientes suelen orientar su solución de implementación de aplicaciones a un servicio de búsqueda diferente (si se usa el mismo nombre de índice), o bien revisar el nombre para crear una copia en el servicio de búsqueda actual y, a continuación, compilarla.

### <a name="continuously-available"></a>Disponibilidad continua

Un índice tiene disponibilidad continua: no se puede pausar ni desconectar. Dado que está diseñado para el funcionamiento continuo, cualquier actualización de su contenido o adiciones al propio índice se hacen en tiempo real. Como resultado, las consultas podrían devolver temporalmente resultados incompletos si una solicitud coincide con una actualización del documento.

Tenga en cuenta que existe continuidad de consultas para las operaciones de documentos (actualización o eliminación) o para las modificaciones que no afectan a la estructura existente y a la integridad del índice actual (como agregar nuevos campos). Si necesita realizar actualizaciones estructurales (cambio de campos existentes), estas se suelen administrar mediante un flujo de trabajo de colocación y recompilación en un entorno de desarrollo o mediante la creación de una nueva versión del índice en el servicio de producción.

Para evitar la recompilación, algunos clientes que están realizando pequeños cambios eligen "versionar" un campo mediante la creación de uno nuevo que coexista junto con una versión anterior. Con el tiempo, esto conduce a contenido huérfano en forma de campos o definiciones de analizador personalizadas obsoletos, especialmente en un índice de producción, cuya replicación resulta costosa. Puede solucionar estos problemas en las actualizaciones planeadas del índice como parte de la administración del ciclo de vida de los índices.

### <a name="endpoint-connection-and-security"></a>Conexión y seguridad de puntos de conexión

Todas las solicitudes de indexación y consulta tienen como destino un índice. Los puntos de conexión suelen ser uno de los siguientes:

| Punto de conexión | Conexión y control de acceso |
|----------|-------------------------------|
| `<your-service>.search.windows.net/indexes` | Tiene como destino la colección de índices. Se usa al crear, mostrar o eliminar un índice. Se requieren derechos de administrador para estas operaciones, disponibles mediante claves de API de administración o un rol de colaborador de búsqueda. |
| `<your-service>.search.windows.net/indexes/<your-index>/docs` | Tiene como destino la colección de documentos de un mismo índice. Se usa al consultar un índice. Los derechos de lectura son suficientes y están disponibles mediante claves de API de consulta o un rol de lector de datos. |

## <a name="next-steps"></a>Pasos siguientes

Puede obtener experiencia práctica en la creación de un índice con casi cualquier ejemplo o tutorial de Cognitive Search. En el caso de los iniciadores, puede elegir cualquiera de las guías de inicio rápido de la tabla de contenido.

Pero también querrá familiarizarse con las metodologías para cargar un índice con datos. Las estrategias de definición de índices y de importación de datos se definen en tándem. En los artículos siguientes se proporciona más información sobre la creación y carga de un índice.

+ [Creación de un índice de búsqueda](search-how-to-create-search-index.md)

+ [Introducción a la importación de datos](search-what-is-data-import.md)

+ [Adición, actualización o eliminación de documentos (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 