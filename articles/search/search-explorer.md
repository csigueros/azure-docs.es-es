---
title: 'Inicio rápido: Herramienta de consulta del Explorador de búsqueda'
titleSuffix: Azure Cognitive Search
description: El Explorador de búsqueda es una herramienta de consulta de Azure Portal que envía solicitudes de consulta a un índice de búsqueda en Azure Cognitive Search. Úselo para aprender sintaxis, probar expresiones de consulta o inspeccionar un documento de búsqueda.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 08/24/2021
ms.openlocfilehash: d246c9aad024b1086a531c31a2a9559dfa798642
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772837"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Inicio rápido: Uso del Explorador de búsqueda para ejecutar consultas en el portal

El **Explorador de búsqueda** es una herramienta de consulta integrada en Azure Portal que se usa para ejecutar consultas en un índice de búsqueda en Azure Cognitive Search. Esta herramienta facilita aprender la sintaxis de las consultas, probar una expresión de consulta o filtro, o verificar si existe contenido nuevo en el índice para confirmar la actualización de datos.

En esta guía de inicio rápido se usa un índice existente para hacer una demostración del Explorador de búsqueda. 

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, tiene que cumplir los siguientes requisitos previos:

+ Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/).

+ Un servicio de Azure Cognitive Search. [Cree un servicio](search-create-service-portal.md) o [busque uno existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) en su suscripción actual. Puede usar un servicio gratuito para este inicio rápido. 

+ En esta guía de inicio rápido se usa *realestate-us-sample-index*. Utilice el [tutorial rápido para crear un índice](search-import-data-portal.md) con los valores predeterminados. Un origen de datos de ejemplo integrado hospedado por Microsoft (**realestate-us-sample**) proporciona los datos.

## <a name="start-search-explorer"></a>Inicio del Explorador de búsqueda

1. En [Azure Portal](https://portal.azure.com), abra la página del información general de búsqueda desde el panel o [busque el servicio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Abra el Explorador de búsqueda desde la barra de comandos:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Comando del Explorador de búsqueda en el portal" border="true":::

    O bien, use la pestaña **Explorador de búsqueda** insertada en un índice abierto:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Pestaña Explorador de búsqueda" border="true":::

## <a name="unspecified-query"></a>Consulta sin especificar

En el Explorador de búsqueda las solicitudes se formulan mediante la [API REST de Search](/rest/api/searchservice/search-documents), donde las respuestas se devuelven como documentos JSON detallados.

Para un echar un primer vistazo al contenido, ejecute una búsqueda vacía haciendo clic en **Buscar** sin especificar ningún término. Una búsqueda vacía resulta útil como primera consulta porque devuelve documentos completos para que pueda revisar la composición del documento. En una búsqueda vacía, no hay ninguna clasificación de búsqueda y los documentos se devuelven en orden arbitrario (`"@search.score": 1` para todos los documentos). De forma predeterminada, en una solicitud de búsqueda se devuelven 50 documentos.

La sintaxis equivalente para una búsqueda vacía es `*` o `search=*`.
   
   ```http
   search=*
   ```

   **Resultados**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Ejemplo de consulta vacía o incompleta" border="true":::

## <a name="free-text-search"></a>Búsqueda de texto libre

Las consultas de forma libre, con o sin operadores, resultan útiles para simular consultas definidas por el usuario enviadas desde una aplicación personalizada a Azure Cognitive Search. Solo se examinan las coincidencias de los campos atribuidos como **Buscable** en la definición del índice. 

Tenga en cuenta que, al proporcionar criterios de búsqueda, como expresiones o términos de consulta, entra en juego la clasificación de búsqueda. El ejemplo siguiente ilustra una búsqueda de texto libre.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultados**

   Puede usar Ctrl-F para buscar términos específicos de interés en los resultados.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Ejemplo de consulta de texto libre" border="true":::

## <a name="count-of-matching-documents"></a>Recuento de documentos coincidentes 

Agregue **$count=true** para obtener el número de coincidencias encontradas en un índice. En una búsqueda vacía, el recuento corresponde al número total de documentos en el índice. En una búsqueda completa, corresponde al número de documentos que coinciden con la entrada de la consulta. Recuerde que el servicio devuelve las 50 coincidencias principales de forma predeterminada, por lo que es posible que tenga más coincidencias en el índice de lo que se incluye en los resultados.

   ```http
   $count=true
   ```

   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Número de documentos coincidentes en el índice" border="true":::

## <a name="limit-fields-in-search-results"></a>Limitación de campos en los resultados de la búsqueda

Agregue [ **$select**](search-query-odata-select.md) para limitar los resultados a los campos designados de manera explícita para obtener una salida más legible en el **Explorador de búsqueda**. Para mantener la cadena de búsqueda y **$count = true**, anteponga **&** a los argumentos. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Restricción de campos en los resultados de la búsqueda" border="true":::

## <a name="return-next-batch-of-results"></a>Devolución del lote siguiente de resultados

Azure Cognitive Search devuelve las primeras 50 coincidencias en función de la clasificación de búsqueda. Para obtener el siguiente conjunto de documentos coincidentes, anexe **$top=100,&$skip=50** para aumentar el conjunto de resultados a 100 documentos (el valor predeterminado es 50, el máximo es 1000), lo que omite los primeros 50 documentos. Puede comprobar la clave del documento (listingID) para identificar el documento. 

Recuerde que debe proporcionar criterios de búsqueda, como una expresión o un término de consulta, para obtener los resultados clasificados. Tenga en cuenta que las puntuaciones de búsqueda disminuyen cuanto más profundamente se llega en los resultados de la búsqueda.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Devolución del lote siguiente de los resultados de búsqueda" border="true":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Expresiones de filtro (mayor que, menor que, igual a)

Utilice el parámetro [ **$filter**](search-query-odata-filter.md) cuando quiera especificar criterios precisos en lugar de búsqueda de texto libre. El campo se debe atribuir como **Filtrable** en el índice. En este ejemplo se buscan más de tres dormitorios:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Filtrar por criterios" border="true":::

## <a name="order-by-expressions"></a>Expresiones OrderBy

Agregue [ **$orderby**](search-query-odata-orderby.md) para ordenar los resultados por otro campo además de la puntuación de búsqueda. El campo se debe atribuir como **Ordenable** en el índice. Una expresión de ejemplo que puede usar para probar este caso es:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Cambio del criterio de ordenación" border="true":::

Ambas expresiones, **$filter** y **$orderby** son construcciones de OData. Para más información, consulte la [sintaxis de filtro de OData](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Puntos clave

En esta guía de inicio rápido, ha usado el **Explorador de búsqueda** para consultar un índice mediante la API de REST.

+ Los resultados se devuelven como documentos JSON detallados para que pueda ver la construcción y el contenido del documento en su totalidad. El parámetro **$select** de una expresión de consulta puede limitar los campos que se devuelven.

+ Los documentos se componen de todos los campos marcados como **Recuperable** en el índice. Para ver los atributos del índice en el portal, haga clic en *realestate-us-sample* en la lista **Índices** de la página de información general de búsqueda.

+ Las consultas de forma libre, similares a las que se pueden escribir en un explorador web comercial, resultan útiles para probar una experiencia de usuario final. Por ejemplo, si partimos del índice realestate de ejemplo integrado, podría escribir "Apartamentos Seattle Lake Washington" y luego usar Ctrl-F para buscar términos dentro de los resultados de búsqueda. 

+ Las expresiones de consulta y de filtro se articulan en una sintaxis implementada por Azure Cognitive Search. El valor predeterminado es una [sintaxis simple](/rest/api/searchservice/simple-query-syntax-in-azure-search), pero también puede usar la sintaxis de [Lucene completa](/rest/api/searchservice/lucene-query-syntax-in-azure-search) para realizar consultas más eficaces. Las [expresiones de filtro](/rest/api/searchservice/odata-expression-syntax-for-azure-search) son una sintaxis de OData.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando trabaje con su propia suscripción, es una buena idea al final de un proyecto identificar si todavía se necesitan los recursos que ha creado. Los recursos que se dejan en ejecución pueden costarle mucho dinero. Puede eliminar los recursos de forma individual o eliminar el grupo de recursos para eliminar todo el conjunto de recursos.

Puede encontrar y administrar recursos en el portal, mediante el vínculo **Todos los recursos** o **Grupos de recursos** en el panel de navegación izquierdo.

Si está usando un servicio gratuito, recuerde que está limitado a tres índices, indexadores y orígenes de datos. Puede eliminar elementos individuales en el portal para mantenerse por debajo del límite. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las estructuras y la sintaxis de consulta, use Postman o una herramienta equivalente para crear expresiones de consulta que aprovechen más partes de la API. La [API de REST de Search](/rest/api/searchservice/search-documents) es especialmente útil para el aprendizaje y la exploración.

> [!div class="nextstepaction"]
> [Creación de una consulta básica en Postman](search-get-started-rest.md)