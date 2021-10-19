---
title: Importación de datos en un índice de búsqueda con Azure Portal
titleSuffix: Azure Cognitive Search
description: Obtenga información sobre el asistente para la importación de datos de Azure Portal que se usa para crear y cargar un índice e invocar opcionalmente el enriquecimiento con IA mediante aptitudes integradas para el procesamiento de lenguaje natural, la traducción, el OCR y el análisis de imágenes.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 9fe443422bac06ccb934a34799bc29be29c45cb5
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714672"
---
# <a name="import-data-wizard-in-azure-cognitive-search"></a>Asistente para la importación de datos de Azure Cognitive Search

El **asistente para la importación de datos** de Azure Portal crea varios objetos que se usan para la indexación y el enriquecimiento con IA en un servicio de búsqueda. Si es la primera vez que utiliza Azure Cognitive Search, es una de las características más eficaces a su disposición. Con un mínimo esfuerzo, puede crear una canalización de indexación o enriquecimiento que ejercite la mayor parte de la funcionalidad de Azure Cognitive Search.

Si usa el asistente para las pruebas de concepto, en este artículo se explica el funcionamiento interno del asistente para que pueda usarlo de forma más eficaz.

No es un artículo paso a paso. Para obtener ayuda sobre el uso del asistente con datos de ejemplo integrados, consulte [Inicio rápido:Creación de un índice de búsqueda](search-get-started-portal.md) o [Inicio rápido: Creación de una traducción de texto y un conjunto de aptitudes de entidades](cognitive-search-quickstart-blob.md).

## <a name="starting-the-wizard"></a>Inicio del asistente

En [Azure Portal](https://portal.azure.com), abra la página del servicio de búsqueda desde el panel o [busque el servicio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) en la lista. En la parte superior de la página de información general del servicio, haga clic en **Importar datos**.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Captura de pantalla del comando para importar datos" border="true":::

El asistente se abre totalmente expandido en la ventana del explorador para que tenga más espacio para trabajar. Varias páginas son bastante densas.

**Importar datos** también se puede ejecutar desde otros servicios de Azure, como Azure Cosmos DB, Azure SQL Database, SQL Managed Instance y Azure Blob Storage. Busque la opción para **agregar Azure Cognitive Search** en el panel de navegación izquierdo en la página de información general del servicio.

## <a name="objects-created-by-the-wizard"></a>Objetos creados por el asistente

El asistente generará los objetos de la tabla siguiente. Una vez creados los objetos, puede revisar sus definiciones JSON en el portal o llamarlos desde el código.

| Object | Descripción | 
|--------|-------------|
| [Indexador](/rest/api/searchservice/create-indexer)  | Un objeto de configuración que especifica un origen de datos, un índice de destino, un conjunto de aptitudes opcional, una programación opcional y valores de configuración opcionales para el control de errores y la codificación en base 64. |
| [Data Source](/rest/api/searchservice/create-data-source) (Origen de datos)  | Conserva la información de conexión en los datos de origen, incluidas las credenciales. Un objeto de origen de datos se utiliza exclusivamente con indexadores. | 
| [Index](/rest/api/searchservice/create-index) | Estructura de datos física que se usa para la búsqueda de texto completo y otras consultas. | 
| [Conjunto de aptitudes](/rest/api/searchservice/create-skillset) | Opcional. Un conjunto completo de instrucciones para manipular, transformar y dar forma al contenido que incluye el análisis y la extracción de información de archivos de imagen. Excepto en el caso de estructuras muy simples y limitadas, incluye una referencia a un recurso de Cognitive Services que proporciona enriquecimiento. | 
| [Almacén de conocimiento](knowledge-store-concept-intro.md) | Opcional. Almacena la salida de una [canalización de enriquecimiento con IA](cognitive-search-concept-intro.md) en tablas y blobs de Azure Storage para realizar análisis independientes o procesamientos de bajada. |

## <a name="benefits-and-limitations"></a>Beneficios y limitaciones

Antes de escribir cualquier código, puede usar el asistente para crear prototipos y pruebas de concepto. El asistente se conecta a orígenes de datos externos, muestrea los datos para crear un índice inicial y, a continuación, los importa como documentos JSON en un índice de Azure Cognitive Search. 

Si va a evaluar conjuntos de aptitudes, el asistente controlará todas las asignaciones de los campos de salida y agregará funciones auxiliares para crear objetos utilizables. La división de texto se agrega si especifica un modo de análisis. La combinación de texto se agrega si elige el análisis de imágenes para que el asistente pueda volver a unir descripciones de texto con el contenido de la imagen. Se agregan las aptitudes de conformador para admitir proyecciones válidas si elige la opción de almacén de conocimiento. Todas las tareas anteriores incluyen una curva de aprendizaje. Si es la primera vez que utiliza el enriquecimiento, la capacidad de controlar estos pasos le permite medir el valor de una aptitud sin tener que invertir mucho tiempo y esfuerzo.

El muestreo es el proceso mediante el cual se infiere un esquema de índice y tiene algunas limitaciones. Al crear el origen de datos, el asistente elige una muestra aleatoria de documentos para decidir las columnas que forman parte del origen de datos. No se leen todos los archivos, ya que esto podría tardar horas en el caso de orígenes de datos muy grandes. Dada una selección de documentos, los metadatos de origen como, por ejemplo, el nombre o el tipo de campo, se usan para crear una colección de campos en un esquema de índice. En función de la complejidad de los datos de origen, es posible que tenga que modificar el esquema inicial para que sea más preciso, o bien ampliarlo para que sea más exhaustivo. Puede insertar los cambios en la página de definición del índice.

En general, las ventajas del uso del asistente son claras: siempre que se cumplan los requisitos, puede crear un prototipo de un índice consultable en cuestión de minutos. Algunas de las dificultades de la indexación como, por ejemplo, serializar datos como documentos JSON, se controlan mediante el asistente.

El asistente no está exento de limitaciones. A continuación se resumen las restricciones:

+ El asistente no admite la iteración ni la reutilización. Cada paso a través del asistente crea una nueva configuración del índice, del conjunto de aptitudes y del indexador. Solo los orígenes de datos se pueden conservar y reutilizar en el asistente. Para editar o refinar otros objetos, elimínelos y empiece de nuevo o use las API de REST o el SDK de .NET para modificar las estructuras.

+ El contenido de origen debe residir en un [origen de datos compatible](search-indexer-overview.md#supported-data-sources).

+ El muestreo se realiza sobre un subconjunto de los datos de origen. En el caso de orígenes de datos de gran tamaño, es posible que el asistente omita algunos campos. Es posible que tenga que ampliar el esquema o corregir los tipos de datos inferidos si el muestreo es insuficiente.

+ El enriquecimiento con IA, tal como se expone en el portal, se limita a un subconjunto de aptitudes integradas. 

+ El [almacén de conocimiento](knowledge-store-concept-intro.md), que se puede crear mediante el asistente, se limita a algunas proyecciones predeterminadas y usa la convención de nomenclatura predeterminada. Si desea personalizar los nombres o las proyecciones, deberá crear el almacén de conocimiento mediante REST o los SDK.

## <a name="workflow"></a>Flujo de trabajo

El asistente se organiza en cuatro pasos principales:

1. Conexión a un origen de datos de Azure compatible.

1. Creación de un esquema de índice que se deduce mediante el muestreo de los datos de origen.

1. Opcionalmente, incorporación de características de enriquecimiento con IA para extraer o generar contenido y estructuras. En este paso se recopilan entradas para crear un almacén de conocimiento.

1. Ejecución del asistente para crear objetos, cargar datos, establecer una programación y otras opciones de configuración.

<a name="data-source-inputs"></a>

### <a name="data-source-configuration-in-the-wizard"></a>Configuración del origen de datos en el asistente

El asistente para la **importación de datos** se conecta a un [origen de datos externo compatible](search-indexer-overview.md#supported-data-sources) mediante la lógica interna que proporcionan los indizadores de Azure Cognitive Search, que están equipados para muestrear el origen, leer los metadatos, descifrar documentos para leer el contenido y la estructura y serializar el contenido como JSON para la importación posterior en Azure Cognitive Search.

No se garantiza que todos los orígenes de datos de la versión preliminar estén disponibles en el asistente. Como cada origen de datos tiene el potencial de introducir otros cambios de nivel inferior, solo se agregará un origen de datos de la versión preliminar a la lista de orígenes de datos si es totalmente compatible con todas las experiencias del asistente, como la definición del conjunto de aptitudes y la inferencia de esquemas de índice.

Solo se puede importar desde una única tabla, vista de base de datos o estructura de datos equivalente; sin embargo, la estructura puede incluir subestructuras jerárquicas o anidadas. Para más información, consulte el artículo sobre la [Cómo modelar tipos complejos](search-howto-complex-data-types.md).

### <a name="skillset-configuration-in-the-wizard"></a>Configuración del conjunto de aptitudes en el asistente

La configuración del conjunto de aptitudes se produce después de la definición del origen de datos, porque el tipo de origen de datos informará de la disponibilidad de determinadas aptitudes integradas. En concreto, si va a indexar archivos de Blob Storage, la elección del modo de análisis de dichos archivos determinará si el análisis de sentimiento estará disponible.

El asistente agregará las aptitudes que elija, pero también agregará otras aptitudes necesarias para lograr un resultado correcto. Por ejemplo, si especifica un almacén de conocimiento, el asistente agrega una aptitud de conformador para admitir proyecciones (o estructuras de datos físicos).

Los conjuntos de aptitudes son opcionales y hay un botón en la parte inferior de la página para saltar si no desea el enriquecimiento con IA.

<a name="index-definition"></a>

### <a name="index-schema-configuration-in-the-wizard"></a>Configuración del esquema de índice en el asistente

El asistente muestrea el origen de datos para detectar los campos y sus tipos. En función del origen de datos, también puede ofrecer campos para indexar metadatos.

Como el muestreo es un ejercicio impreciso, revise el índice para atender a las consideraciones siguientes:

1. ¿La lista de campos es precisa? Si el origen de datos contiene campos que no se han seleccionado en el muestreo, puede agregar manualmente los nuevos campos que falten en el muestreo y quitar los que no agreguen valor a una experiencia de búsqueda o que no se vayan a utilizar en una [expresión de filtro](search-query-odata-filter.md) o un [perfil de puntuación](index-add-scoring-profiles.md).

1. ¿Es el tipo de datos adecuado para los datos entrantes? Azure Cognitive Search admite los [tipos de datos de Entity Data Model (EDM)](/rest/api/searchservice/supported-data-types). En el caso de datos SQL de Azure, hay un [gráfico de asignaciones](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) que muestra valores equivalentes. Para más información, consulte [Transformaciones y asignaciones de campos](search-indexer-field-mappings.md).

1. ¿Tiene un campo que pueda actuar como *clave*? Este campo debe ser Edm.string y debe identificar de forma exclusiva un único documento. En el caso de los datos relacionales, se podría asignar a una clave principal. En el caso de los blobs, esta podría ser `metadata-storage-path`. Si los valores de campo incluyen espacios o guiones, debe establecer la opción **Claves de codificación Base 64** del paso **Crear un indexador** en **Opciones avanzadas**, a fin de suprimir la comprobación de validación para estos caracteres.

1. Establezca los atributos para determinar cómo se utiliza ese campo en un índice. 

   Realice con cuidado este paso porque los atributos determinarán la expresión física de los campos del índice. Si desea cambiar los atributos más adelante, incluso mediante programación, casi siempre tendrá que quitar y recompilar el índice. Los atributos principales como **Searchable** y **Retrievable** tienen un [impacto insignificante en el almacenamiento](search-what-is-an-index.md#index-size). La habilitación de filtros y el uso de proveedores de sugerencias aumentan los requisitos de almacenamiento. 

   + **Searchable** permite la búsqueda de texto completo. Todos los campos utilizados en consultas de formato libre o en expresiones de consulta deben tener este atributo. Para cada campo que marque como **Searchable**, se crearán índices invertidos.

   + **Retrievable** devuelve el campo en los resultados de búsqueda. Todos los campos que proporcionan contenido a los resultados de búsqueda deben tener este atributo. Al establecer este campo, el tamaño del índice no se verá afectado de manera apreciable.

   + **Filterable** permite que se haga referencia al campo en las expresiones de filtro. Cada campo utilizado en una expresión **$filter** debe tener este atributo. Las expresiones de filtro son para coincidencias exactas. Dado que las cadenas de texto permanecen intactas, se necesita más almacenamiento para dar cabida al contenido textual.

   + **Facetable** permite que el campo se use en la navegación con facetas. Solo los campos marcados también como **Filterable** pueden marcarse como **Facetable**.

   + **Sortable** permite que el campo se use en una ordenación. Cada campo utilizado en una expresión **$Orderby** debe tener este atributo.

1. ¿Necesita [análisis léxico](search-lucene-query-architecture.md#stage-2-lexical-analysis)? En el caso de los campos Edm.string que son **Searchable**, puede establecer un **analizador** si quiere indexación y consulta con un lenguaje mejorado. 

   La opción predeterminada es *Estándar - Lucene*, pero puede elegir *Inglés - Microsoft* si desea usar el analizador de Microsoft para procesamiento léxico avanzado, como para la resolución de formas verbales y sustantivos irregulares. Solo los analizadores de idiomas se pueden especificar en el portal. Si se usa un analizador personalizado o un analizador que no sea de idiomas, como palabra clave o patrón, se deberá hacer mediante programación. Para más información acerca de los analizadores, consulte [Adición de analizadores de idiomas](search-language-support.md).

1. ¿Necesita la funcionalidad de escritura anticipada en forma de autocompletar o de resultados sugeridos? Active la casilla **Proveedor de sugerencias** para habilitar las [sugerencias de consultas de escritura anticipada y la función autocompletar](index-add-suggesters.md) en los campos seleccionados. Los proveedores de sugerencias se suman al número de términos con tokens en el índice y, por tanto, consumen más almacenamiento.

### <a name="indexer-configuration-in-the-wizard"></a>Configuración del indizador en el asistente

La última página del asistente recopila entradas de usuario para la configuración del indizador. Puede [especificar una programación](search-howto-schedule-indexers.md) y establecer otras opciones que variarán según el tipo de origen de datos.

Internamente el asistente también configura lo siguiente, que no es visible en el indizador hasta después de su creación:

+ [Asignaciones de campos](search-indexer-field-mappings.md) entre el origen de datos y el índice.
+ [Asignaciones de campos de salida](cognitive-search-output-field-mapping.md) entre la salida de la aptitud y un índice.

## <a name="next-steps"></a>Pasos siguientes

La mejor manera de comprender las ventajas y limitaciones del asistente es recorrer sus pasos. El siguiente inicio rápido le guiará a través de cada paso.

> [!div class="nextstepaction"]
> [Inicio rápido: Creación de un índice de búsqueda mediante Azure Portal](search-get-started-portal.md)