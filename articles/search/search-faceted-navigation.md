---
title: Agregar una jerarquía de categoría de navegación por facetas
titleSuffix: Azure Cognitive Search
description: Agregue navegación por facetas para el filtrado autodirigido en aplicaciones que se integran con Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/30/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ebd61d57e79864c1af8a583de349eb5a6706fce8
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273939"
---
# <a name="add-faceted-navigation-to-a-search-app"></a>Adición de navegación por facetas a una aplicación de búsqueda

La navegación por facetas se usa para aplicar filtrado de exploración en profundidad autodirigido en resultados de consulta de una aplicación de búsqueda, donde la aplicación ofrece controles de formulario para limitar la búsqueda a grupos de documentos (por ejemplo, categorías o marcas) y Azure Cognitive Search proporciona las estructuras de datos y los filtros para respaldar la experiencia. 

En este artículo, aprenderá los pasos básicos para crear una estructura de navegación por facetas en Azure Cognitive Search.

> [!div class="checklist"]
> * Establecimiento de atributos de campo en el índice
> * Estructura de solicitudes y respuestas
> * Adición de controles de navegación y filtros en la capa de presentación

El código de la capa de presentación realiza el trabajo pesado en una experiencia de navegación por facetas. Las demostraciones y ejemplos que aparecen al final de este artículo proporcionan código de trabajo que muestra cómo encajar todas las piezas.

## <a name="faceted-navigation-in-a-search-page"></a>Navegación por facetas en una página de búsqueda

Las facetas son dinámicas y se devuelven en una consulta. Una respuesta de búsqueda trae consigo todas las categorías de facetas utilizadas para navegar por los documentos en el resultado. Primero se ejecuta la consulta y luego se extraen las facetas de los resultados actuales y se ensamblan en una estructura de navegación por facetas.

En Cognitive Search, las facetas tienen una capa de profundidad y no pueden ser jerárquicas. Si no está familiarizado con la navegación por facetas estructurada, en el ejemplo siguiente se muestra uno a la izquierda. Los recuentos indican el número de coincidencias por cada faceta. El mismo documento se puede representar en varias facetas.

:::image source="media/tutorial-csharp-create-first-app/azure-search-facet-nav.png" alt-text="Resultados de la búsqueda por facetas":::

Las facetas pueden ayudarle a encontrar lo que busca y le garantizan que obtendrá al menos un resultado. Como desarrollador, las facetas permiten exponer los criterios de búsqueda más útiles para navegar por el índice de búsqueda.

## <a name="enable-facets-in-the-index"></a>Habilitación de facetas en el índice

La aplicación de facetas está habilitada campo por campo en una definición de índice cuando se establece el atributo "facetable" en true.

Aunque no es estrictamente necesario, también debe establecer el atributo "filterable" para que pueda crear los filtros necesarios que respalden la experiencia de navegación por facetas en la aplicación de búsqueda.

En el ejemplo siguiente del índice de ejemplo "hotels" se muestra "facetable" y "filterable" en campos de cardinalidad baja que contienen valores únicos o frases cortas: "Category", "Tags", "Rating".

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "Description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "HotelName", "type": "Edm.String", "facetable": false },
    { "name": "Category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint" }
  ]
}
```

### <a name="choosing-fields"></a>Elección de los campos

Las facetas se pueden calcular tanto para campos de valor único como para colecciones. Los campos que funcionan mejor en la navegación por facetas tienen estas características:

* Cardinalidad baja (un pequeño número de valores distintos que se repiten en los documentos del corpus de búsqueda)

* Valores descriptivos cortos (una o dos palabras) que se representarán correctamente en un árbol de navegación

El contenido de un campo, y no el propio campo, genera las facetas en una estructura de navegación por facetas. Si la faceta es un campo de cadena *Color*, las facetas serán azules, verdes y cualquier otro valor de ese campo.

Como procedimiento recomendado, compruebe que los campos no tengan valores NULL, errores ortográficos o discrepancias de mayúsculas y minúsculas, y versiones en singular y plural de la misma palabra. Los filtros y facetas no se someten a análisis léxico ni a [revisión ortográfica](speller-how-to-add.md), lo que significa que todos los valores de un campo "facetable" son facetas en potencia, incluso si las palabras difieren en un carácter.

### <a name="defaults-in-rest-and-azure-sdks"></a>Valores predeterminados en los SDK de REST y Azure

Si usa uno de los SDK de Azure, el código debe especificar todos los atributos de campo. Por el contrario, la API REST tiene valores predeterminados para los atributos de campo basados en el [tipo de datos](/rest/api/searchservice/supported-data-types). Los siguientes tipos de datos son "filtrables" y "clasificables" de forma predeterminada:

* `Edm.String`
* `Edm.DateTimeOffset`
* `Edm.Boolean`
* `Edm.Int32`, `Edm.Int64`, `Edm.Double`
* Colecciones de cualquiera de los tipos anteriores (por ejemplo, `Collection(Edm.String)` o `Collection(Edm.Double)`)

No puede usar campos `Edm.GeographyPoint` ni `Collection(Edm.GeographyPoint)` en la navegación por facetas. Las facetas funcionan mejor en los campos con cardinalidad baja. Debido a la resolución de coordenadas geográficas, es poco frecuente que dos conjuntos de coordenadas sean iguales en un conjunto de datos determinado. Como tales, las facetas no se admiten para coordenadas geográficas. Para crear una faceta de ubicación, se necesita un campo de ciudad o región.

> [!Tip]
> Como procedimiento recomendado para optimizar el rendimiento y el almacenamiento, desactive las facetas para los campos que nunca se deban usar como facetas. En concreto, los campos de cadena de valores únicos, como un identificador o un nombre de producto, deben establecerse en `"facetable": false` para evitar su uso accidental (e ineficaz) en la navegación por facetas. Esto es especialmente cierto para la API REST que habilita filtros y facetas de forma predeterminada.

## <a name="facet-request-and-response"></a>Solicitud y respuesta de faceta

Las facetas se especifican en la consulta y la estructura de navegación por facetas se devuelve en la parte superior de la respuesta. La estructura de una solicitud y respuesta es bastante sencilla. De hecho, el trabajo real detrás de la navegación por facetas se encuentra en la capa de presentación, que se trata en una sección posterior. 

El ejemplo de REST siguiente es una consulta no completa (`"search": "*"`) que tiene como ámbito todo el índice (consulte el [ejemplo integrado "hotels"](search-get-started-portal.md)). Las facetas suelen ser una lista de campos, pero esta consulta muestra solo uno para que la siguiente respuesta sea más legible.

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "queryType": "simple",
    "select": "",
    "searchFields": "",
    "filter": "",
    "facets": [ "Category"], 
    "orderby": "",
    "count": true
}
```

Resulta útil inicializar una página de búsqueda con una consulta abierta para rellenar completamente la estructura de navegación por facetas. Tan pronto como se pasen los términos de consulta en la solicitud, la estructura de navegación por facetas se limitará a solo las coincidencias en los resultados, en lugar de todo el índice.

La respuesta del ejemplo anterior incluye la estructura de navegación por facetas en la parte superior. La estructura consta de valores "Category" y un recuento de los hoteles para cada uno. Va seguido del resto de los resultados de búsqueda, que aquí se han recortado para mayor brevedad. Este ejemplo funciona bien por varias razones. El número de facetas de este campo se encuentra por debajo del límite (el valor predeterminado es 10), por lo que todas ellas aparecen y cada hotel del índice de 50 hoteles se representa exactamente en una de estas categorías.

```json
{
    "@odata.context": "https://demo-search-svc.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "@odata.count": 50,
    "@search.facets": {
        "Category": [
            {
                "count": 13,
                "value": "Budget"
            },
            {
                "count": 12,
                "value": "Resort and Spa"
            },
            {
                "count": 9,
                "value": "Luxury"
            },
            {
                "count": 7,
                "value": "Boutique"
            },
            {
                "count": 5,
                "value": "Suite"
            },
            {
                "count": 4,
                "value": "Extended-Stay"
            }
        ]
    },
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Category": "Boutique",
            "Tags": [
                "pool",
                "air conditioning",
                "concierge"
            ],
            "ParkingIncluded": false,
  . . .
```

## <a name="facets-syntax"></a>Sintaxis de las facetas

Un parámetro de consulta de faceta se establece en una lista delimitada por comas de campos "clasificables" y, según el tipo de datos, se puede parametrizar aún más para establecer recuentos, ordenaciones e intervalos: `count:<integer>`, `sort:<>`, `interval:<integer>` y `values:<list>`. Para más información sobre los parámetros de faceta, consulte ["Parámetros de consulta" en la API REST](/rest/api/searchservice/search-documents#query-parameters).

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "facets": [ "Category", "Tags,count:5", "Rating,values:1|2|3|4|5"],
    "count": true
}
```

Para cada árbol de navegación por facetas, hay un límite predeterminado de 10 facetas. Este valor predeterminado tiene sentido en las estructuras de navegación porque mantiene la lista de valores en un tamaño fácil de administrar. Puede invalidar el valor predeterminado y asignar un valor a "count". Por ejemplo, `"Tags,count:5"` reduce el número de etiquetas de la sección de etiquetas a las cinco principales.

Para los valores Numeric y DateTime únicamente, puede establecer explícitamente valores en el campo de faceta (por ejemplo, `facet=Rating,values:1|2|3|4|5`) para separar los resultados en intervalos contiguos (intervalos basados en valores numéricos o períodos de tiempo). Como alternativa, puede agregar "interval", como en `facet=Rating,interval:1`. 

Cada intervalo se genera usando 0 como punto de partida, un valor de la lista como extremo y, después, se recorta el intervalo anterior para crear diferentes intervalos.

### <a name="discrepancies-in-facet-counts"></a>Discrepancias en los recuentos de facetas

En algunos casos, puede que los recuentos de faceta no coincidan con los conjuntos de resultados (consulte [Navegación por facetas en Azure Cognitive Search, en la página de preguntas y respuestas de Microsoft](/answers/topics/azure-cognitive-search.html)).

Los recuentos de faceta pueden ser incorrectos debido a la arquitectura de particionamiento. Cada índice de búsqueda tiene varias particiones, y cada una notifica las N primeras facetas por recuento de documentos, que después se combina en un único resultado. Si algunas particiones tienen muchos valores coincidentes, mientras que otros tienen menos, verá que algunos valores de faceta faltan o se contabilizan con un número inferior en los resultados.

Aunque este comportamiento podría cambiar en cualquier momento, si se produce en la actualidad puede solucionarlo aumentando artificialmente el valor de count:\<number> en un número grande para forzar que se notifique el valor completo de cada partición. Si el valor de count: es mayor o igual que el número de valores únicos en el campo, se garantizan resultados precisos. Sin embargo, si el recuento de documentos es alto, esto afecta al rendimiento, por lo que debe usar esta opción con prudencia.

## <a name="presentation-layer"></a>Capa de presentación

En el código de aplicación, el patrón es usar parámetros de consulta de faceta para devolver la estructura de navegación por facetas junto con los resultados de las facetas, además de una expresión $filter.  La expresión de filtro controla el evento de clic y acota aún más el resultado de la búsqueda en función de la selección de las facetas.

### <a name="facet-and-filter-combination"></a>Combinación de faceta y filtro

El siguiente fragmento de código del archivo `JobsSearch.cs` de la demostración NYCJobs agrega el cargo empresarial seleccionado al filtro si selecciona un valor de la faceta "Business Title".

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

A continuación, se muestra otro caso del ejemplo "hotels". El fragmento de código siguiente agrega categorFacet al filtro si un usuario selecciona un valor de la faceta de categoría.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

### <a name="html-for-faceted-navigation"></a>HTML para la navegación por facetas

El ejemplo siguiente, tomado del archivo `index.cshtml` de la aplicación de ejemplo NYCJobs, muestra la estructura HTML estática para visualizar la navegación por facetas en la página de resultados de búsqueda. La lista de facetas se compila o recompila dinámicamente cuando se envía un término de búsqueda, o cuando se selecciona o borra una faceta.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

### <a name="build-html-dynamically"></a>Compilación dinámica de HTML

El siguiente fragmento de código de `index.cshtml` (también de la demostración NYCJobs) compila de forma dinámica el código HTML para mostrar la primera faceta, "Business Title". Funciones similares compilan dinámicamente el HTML para las otras facetas. Cada faceta tiene una etiqueta y un recuento, que muestra el número de elementos encontrados para ese resultado de la faceta.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

## <a name="tips-for-working-with-facets"></a>Sugerencias para trabajar con facetas

Esta sección es una colección de sugerencias y soluciones alternativas que pueden resultar útiles.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Conservar una estructura de navegación por facetas de forma asincrónica respecto a los resultados filtrados

Uno de los retos de la navegación por facetas de Azure Cognitive Search es que las facetas solo existen para los resultados actuales. En la práctica, se suele conservar un conjunto estático de facetas para que el usuario pueda navegar en orden inverso y reconstruir los pasos para explorar rutas alternativas a través del contenido de búsqueda. 

Aunque se trata de un caso de uso común, no es algo que la estructura de navegación por facetas proporcione de forma predeterminada actualmente. Para solucionar esta limitación, los desarrolladores que quieren facetas estáticas suelen emitir dos consultas filtradas: una centrada en los resultados y otra para crear una lista estática de facetas con fines de navegación.

### <a name="clear-facets"></a>Eliminación de las facetas

Al diseñar la página de resultados de búsqueda, no olvide agregar un mecanismo para borrar las facetas. Si agrega casillas de verificación, puede ver fácilmente cómo borrar los filtros. En otros diseños, puede que necesite un patrón de ruta de navegación u otro enfoque creativo. En el ejemplo "hotels" para C#, puede enviar una búsqueda vacía para restablecer la página. Por el contrario, la aplicación de ejemplo NYCJobs proporciona un elemento en el que se puede hacer clic, `[X]`, después de seleccionar una faceta para borrarla, que es una cola visual más fuerte para el usuario.

### <a name="trim-facet-results-with-more-filters"></a>Recorte de los resultados de faceta con más filtros

Los resultados de faceta son documentos que se encuentran en los resultados de búsqueda y que coinciden con un término de faceta. En el ejemplo siguiente, en los resultados de búsqueda de *cloud computing*, 254 elementos también tienen *internal specification* como tipo de contenido. Los elementos no son necesariamente excluyentes mutuamente. Si un elemento cumple los criterios de ambos filtros, se contabiliza en cada uno de ellos. Esta duplicación es posible cuando se usan facetas en campos `Collection(Edm.String)`, que suelen usarse para implementar el etiquetado de documentos.

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

En general, si encuentra que los resultados de faceta son demasiado grandes de forma persistente, se recomienda agregar más filtros para ofrecer a los usuarios más opciones para delimitar la búsqueda.

### <a name="a-facet-only-search-experience"></a>Experiencia de búsqueda solo con facetas

Si la aplicación usa exclusivamente la navegación por facetas (es decir, ningún cuadro de búsqueda), puede marcar el campo como `searchable=false`, `filterable=true` y `facetable=true` para generar un índice más compacto. El índice no incluirá índices invertidos y no habrá análisis de texto ni tokenización. Los filtros se realizan por coincidencias exactas a nivel de caracteres.

### <a name="validate-inputs-at-query-time"></a>Validación de entradas en tiempo de consulta

Si compila la lista de facetas de manera dinámica basándose en la entrada de usuarios que no son de confianza, valide que los nombres de los campos con facetas son válidos. O bien, anule los nombres al generar direcciones URL utilizando o bien `Uri.EscapeDataString()` en. NET, o su equivalente en la plataforma que elija.

## <a name="demos-and-samples"></a>Demostraciones y ejemplos

Varios ejemplos incluyen la navegación por facetas. En esta sección se incluyen vínculos a los ejemplos y también se indica qué lenguaje y biblioteca cliente se usan para cada uno de ellos.

### <a name="create-your-first-app-in-c-razor"></a>Creación de la primera aplicación en C# (Razor)

En este tutorial y en la serie de ejemplos de C# se incluye una [lección centrada en la navegación por facetas](tutorial-csharp-facets.md). La solución es una aplicación ASP.NET MVC y la capa de presentación usa las bibliotecas cliente de Razor.

### <a name="add-search-to-web-apps-react"></a>Incorporación de la búsqueda a las aplicaciones web (React)

Los tutoriales y ejemplos de [C#](tutorial-csharp-overview.md), [Python](tutorial-python-overview.md) y [JavaScript](tutorial-javascript-overview.md) incluyen la navegación por facetas, así como filtros, sugerencias y la función de autocompletar. Estos ejemplos usan React para la capa de presentación.

### <a name="nycjobs-sample-code-and-demo-ajax"></a>Demostración y código de ejemplo de NYCJobs (Ajax)

El ejemplo NYCJobs es una aplicación ASP.NET MVC que usa Ajax en la capa de presentación. Está disponible como [aplicación de demostración en vivo](https://aka.ms/azjobsdemo) y como código fuente en el [repositorio de ejemplos de Azure en GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

### <a name="video-demonstration"></a>Demostración en vídeo

En [Azure Cognitive Search Deep Dive](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410), hay una demostración sobre cómo implementar facetas.
