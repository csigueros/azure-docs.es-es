---
title: Característica de la consulta moreLikeThis (versión preliminar)
titleSuffix: Azure Cognitive Search
description: Se describe la característica moreLikeThis (versión preliminar), que está disponible en las versiones preliminares de la API REST de Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 595884bf9d73e6e7af6663c051a2735908670eb4
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112578675"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (versión preliminar) en Azure Cognitive Search

> [!IMPORTANT] 
> Esta característica se encuentra en versión preliminar pública en los [Términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). La [API REST de versión preliminar](/rest/api/searchservice/index-preview) admite esta característica.

`moreLikeThis=[key]` es un parámetro de consulta de [Search Documents API](/rest/api/searchservice/search-documents) que busca documentos similares al documento especificado por la clave del documento. Cuando se realiza una solicitud de búsqueda con `moreLikeThis`, se genera una consulta con los términos de búsqueda extraídos del documento especificado que describen mejor ese documento. La consulta generada se usa luego para realizar la solicitud de búsqueda. De forma predeterminada, se tiene en cuenta el contenido de todos los campos que permiten búsquedas, menos cualquier campo restringido especificado mediante el parámetro `searchFields`. El parámetro `moreLikeThis` no se puede usar con el parámetro de búsqueda, `search=[string]`.

De forma predeterminada, se tiene en cuenta el contenido de todos los campos que permiten búsquedas de nivel superior. Si quiere especificar campos concretos en su lugar, puede usar el parámetro `searchFields`. 

No se puede usar `MoreLikeThis` en campos secundarios que permiten búsquedas de un [tipo complejo](search-howto-complex-data-types.md).

## <a name="examples"></a>Ejemplos

En todos los ejemplos siguientes se usa el ejemplo de hoteles de [Inicio rápido: Creación de un índice de búsqueda en Azure Portal](search-get-started-portal.md).

### <a name="simple-query"></a>Consulta sencilla

La siguiente consulta busca los documentos cuyos campos de descripción son lo más parecido al campo del documento de origen según se especifica con el parámetro `moreLikeThis`:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

En este ejemplo, la solicitud busca hoteles parecidos al que tiene el identificador `HotelId` 29.
En lugar de usar HTTP GET, también puede invocar `MoreLikeThis` con HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Aplicación de filtros

`MoreLikeThis` se puede combinar con otros parámetros de consulta habituales como `$filter`. Por ejemplo, la consulta se puede restringir solo a aquellos hoteles cuya categoría es "Budget" y la puntuación superior a 3.5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>Selección de cambios y limitación de resultados

El selector `$top` se puede utilizar para limitar el número de resultados que se devuelven en una consulta de `MoreLikeThis`. Además, los campos pueden seleccionarse con `$select`. Aquí se seleccionan los tres hoteles principales junto con su identificador, nombre y puntuación: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>Pasos siguientes

Puede usar cualquier herramienta de pruebas web para experimentar con esta característica.  Se recomienda usar Postman para este ejercicio.

> [!div class="nextstepaction"]
> [Exploración de las API REST de Cognitive Search](search-get-started-rest.md)