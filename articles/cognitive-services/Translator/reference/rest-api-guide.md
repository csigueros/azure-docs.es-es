---
title: Guía de referencia de la API REST de traducción de texto
titleSuffix: Azure Cognitive Services
description: Vea una lista de con vínculos a las API REST de traducción de texto.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/10/2021
ms.author: lajanuar
ms.openlocfilehash: f3188f303d7e2945341267c9ea4598682e4d7964
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426254"
---
# <a name="text-translation-rest-api"></a>API REST de traducción de texto

La traducción de texto es una característica basada en la nube del servicio Azure Translator y forma parte de la familia de Azure Cognitive Services de las API REST. La API de traducción de texto traduce texto entre pares de idiomas en todos los [idiomas y dialectos admitidos](../../language-support.md). Los métodos disponibles se enumeran en la tabla siguiente:

| Solicitud| Método| Descripción|
|---------|--------------|---------|
| [**languages**](v3-0-languages.md) | **GET** | Devuelve el conjunto de idiomas admitidos actualmente por los métodos de **traducción**, **transliteración** y **diccionario**. Esta solicitud no requiere encabezados de autenticación y no necesita un recurso Translator para ver el conjunto de idiomas admitido.|
|[**translate**](v3-0-translate.md) | **POST**| Traduce el texto de idioma de origen especificado en el texto del idioma de destino.|
|[**transliterar**](v3-0-transliterate.md) |  **POST** | Asigna el alfabeto o script de idioma de origen a un alfabeto o script del idioma de destino.
|[**detectar**](v3-0-detect.md) | **POST** | Identifica el idioma de origen. |
|[**breakSentence**](v3-0-break-sentence.md) | **POST** | Devuelve una matriz de enteros que representa la longitud de las oraciones en un texto de origen. |
| [**dictionary/lookup**](v3-0-dictionary-lookup.md) | **POST** | Devuelve alternativas para traducciones de palabras individuales. |
| [**dictionary/examples**](v3-0-dictionary-lookup.md) | **POST** | Devuelve cómo se usa un término en contexto. |

> [!div class="nextstepaction"]
> [Creación de un recurso Translator en Azure Portal.](../translator-how-to-signup.md)

> [!div class="nextstepaction"]
> [Inicio rápido: API REST y el lenguaje de programación](../quickstart-translator.md)
