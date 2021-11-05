---
title: 'Compatibilidad con idiomas: respuesta a preguntas personalizada'
titleSuffix: Azure Cognitive Services
description: Lista de idiomas naturales y referencia cultural admitidos por la respuesta a preguntas personalizada de la base de conocimiento. No mezcle idiomas en la misma base de conocimiento.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: reference
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 24e5f8baa8b90dbdaf848711ce87006c0de90ee6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093648"
---
# <a name="language-support-for-custom-question-answering-and-knowledge-bases"></a>Compatibilidad con idiomas en la respuesta a preguntas personalizada y las bases de conocimiento

En este artículo se describen las opciones de compatibilidad de idiomas con los recursos y las bases de conocimiento habilitados para la respuesta a preguntas personalizada. 

En la respuesta a preguntas personalizada, tiene la opción de seleccionar el idioma cada vez que agregue un nuevo proyecto a un recurso que permita la compatibilidad con varios idiomas, o bien puede seleccionar un idioma que se aplique a todos los proyectos futuros de un recurso.

Si decide habilitar la configuración de idioma en todos los proyectos, no podrá deshabilitarla.

## <a name="supporting-multiple-languages-in-one-custom-question-answering-enabled-resource"></a>Compatibilidad con varios idiomas en un recurso habilitado para la respuesta a preguntas personalizada

> [!div class="mx-imgBorder"]
> ![Selección de una base de conocimiento multilingüe](./media/language-support/choose-language.png)

* Al crear el primer proyecto en el servicio, puede elegir el idioma cada vez que cree un proyecto. Seleccione esta opción para crear bases de conocimiento que pertenezcan a distintos idiomas dentro de un servicio.
* Una vez que se crea la primera knowledge base, la opción de configuración de idioma no se puede modificar en el servicio.
* Si habilita varios idiomas para la knowledge base, en lugar de tener un índice de prueba para el servicio tendrá un índice de prueba por cada knowledge base.

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Compatibilidad con varios idiomas en una knowledge base

Si necesita admitir un sistema de base de conocimiento que incluye varios idiomas, puede:

* Usar el [servicio Translator](../../translator/translator-info-overview.md) para traducir una pregunta a un solo idioma antes de enviarla a la base de conocimiento. Esto le permite concentrarse en la calidad de un solo idioma, así como en la calidad de las preguntas y respuestas alternativas.
* Cree un recurso de idioma habilitado para la respuesta a preguntas personalizada y una base de conocimiento dentro de ese recurso, para cada idioma. Esta opción le permite administrar preguntas alternativas independientes y textos de respuesta con más matices para cada idioma. De este modo, obtiene mucha más flexibilidad, aunque tiene un costo de mantenimiento mucho mayor cuando las preguntas o respuestas son distintas en todos los idiomas.

## <a name="single-language-per-resource"></a>Un solo idioma por recurso

Si **selecciona la opción para establecer el idioma que se usan en todos los proyectos asociados al recurso**, tenga en cuenta lo siguiente: 
* Un recurso de idioma, y todos sus proyectos o bases de conocimiento, solo admitirán un idioma.
* El idioma se establece explícitamente cuando se crea el primer proyecto del servicio.
* El idioma no se puede cambiar en ningún otro proyecto asociado al recurso.
* Tanto el servicio Cognitive Search (clasificador 1) como la respuesta a preguntas personalizada (clasificador 2) usan el idioma para generar la mejor respuesta a una consulta.

## <a name="languages-supported"></a>Idiomas admitidos

La siguiente lista contiene los idiomas que admiten los recursos de QnA Maker. 

| Idioma |
|--|
| Árabe |
| Armenio |
| Bengalí |
| Vasco |
| Búlgaro |
| Catalán |
| Chino simplificado |
| Chino tradicional |
| Croata |
| Checo |
| Danés |
| Neerlandés |
| Inglés |
| Estonio |
| Finés |
| Francés |
| Gallego |
| Alemán |
| Griego |
| Gujarati |
| Hebreo |
| Hindi |
| Húngaro |
| Islandés |
| Indonesio |
| Irlandés |
| Italiano |
| Japonés |
| Canarés |
| Coreano |
| Letón |
| Lituano |
| Malayalam |
| Malayo |
| Noruego |
| Polaco |
| Portugués |
| Punjabi |
| Rumano |
| Ruso |
| Serbio cirílico |
| Serbio latín |
| Eslovaco |
| Esloveno |
| Español |
| Sueco |
| Tamil |
| Telugu |
| Tailandés |
| Turco |
| Ucraniano |
| Urdu |
| Vietnamita |

## <a name="query-matching-and-relevance"></a>Coincidencia y relevancia de las consultas
La respuesta a preguntas personalizada depende de los [analizadores de idioma de Azure Cognitive Search](/rest/api/searchservice/language-support) para proporcionar resultados.

Aunque las funcionalidades de Azure Cognitive Search están en el mismo nivel que los idiomas admitidos, QnA Maker tiene un clasificador adicional que está por encima de los resultados de búsqueda de Azure. En este modelo de clasificador, se usan características semánticas y basadas en palabras especiales en los siguientes idiomas.

|Idiomas con un clasificador adicional|
|--|
|Chino|
|Checo|
|Neerlandés|
|Inglés|
|Francés|
|Alemán|
|Húngaro|
|Italiano|
|Japonés|
|Coreano|
|Polaco|
|Portugués|
|Español|
|Sueco|

Esta clasificación adicional es un trabajo interno del clasificador de la respuesta a preguntas personalizada.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Selección de idioma](../index.yml)
