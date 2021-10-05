---
title: 'Referencia del lenguaje de consulta de Azure Digital Twins: palabras clave reservadas'
titleSuffix: Azure Digital Twins
description: Documentación de referencia de las palabras claves reservadas del lenguaje de consulta de Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 6ffba46cacf452a4769709b115177bab41d5eca7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837894"
---
# <a name="azure-digital-twins-query-language-reference-reserved-keywords"></a>Referencia del lenguaje de consulta de Azure Digital Twins: palabras clave reservadas

Este documento contiene la lista de **palabras clave reservadas** en el [lenguaje de consulta de Azure Digital Twins](concepts-query-language.md). Estas palabras no se pueden usar como identificadores en las consultas a menos que se [escapen entre corchetes dobles](#escaping-reserved-keywords-in-queries). 

## <a name="list-of-reserved-keywords"></a>Lista de palabras clave reservadas

Estas son las palabras clave reservadas en el lenguaje de consulta de Azure Digital Twins:

* ALL 
* y
* AS
* ASC
* AVG
* BY
* COUNT
* DESC
* DEVICES_JOBS
* DEVICES_MODULES
* DISPOSITIVO
* ENDS_WITH
* FALSE
* FROM
* GROUP
* IN
* IS_BOOL
* IS_DEFINED
* IS_NULL
* IS_NUMBER
* IS_OBJECT
* IS_PRIMITIVE
* IS_STRING
* MÁX
* MÍN
* NOT
* NOT_IN
* NULL
* O BIEN
* ORDER
* SELECT
* STARTS_WITH
* SUM
* TOP
* TRUE
* WHERE
* IS_OF_MODEL

## <a name="escaping-reserved-keywords-in-queries"></a>Escape de palabras clave reservadas en las consultas

Para usar una palabra clave reservada como identificador en una consulta, debe escaparla incluyéndola entre corchetes dobles como los siguientes: `[[<keyword>]]`

Por ejemplo, considere un conjunto de gemelos digitales con una propiedad denominada `GROUP`, que es una palabra clave reservada. Para filtrar por ese valor de propiedad, el nombre de la propiedad debe escaparse donde se use en la consulta, como se muestra a continuación:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ReservedKeywordExample":::