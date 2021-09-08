---
title: Guía de referencia de la API REST de traducción de documentos
titleSuffix: Azure Cognitive Services
description: Vea una lista de con vínculos a las API REST de traducción de documentos.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/21/2021
ms.author: lajanuar
ms.openlocfilehash: 38757efc32a90a74cdf73bc84af28b80f8402948
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112543904"
---
# <a name="document-translation-rest-api-reference-guide"></a>Guía de referencia de la API REST de traducción de documentos

La traducción de documentos es una característica basada en la nube del servicio Azure Translator y forma parte de la familia de Azure Cognitive Services de las API de REST. La API de traducción de documentos traduce documentos entre todos los [idiomas y dialectos admitidos](../../language-support.md) manteniendo la estructura del documento y el formato de los datos. Los métodos disponibles se enumeran en la tabla siguiente:

| Solicitud| Descripción|
|---------|--------------|
| [**Obtención de formatos de documento admitidos**](get-supported-document-formats.md)| Este método devuelve una lista de los formatos de documento admitidos.|
|[**Obtención de formatos de glosario admitidos**](get-supported-glossary-formats.md)|Este método devuelve una lista de los formatos de glosario admitidos.|
|[**Obtención de orígenes de almacenamiento admitidos**](get-supported-storage-sources.md)| Este método devuelve una lista de las opciones y orígenes de almacenamiento admitidos.|
|[**Inicio de traducción (POST)** ](start-translation.md)|Este método inicia un trabajo de traducción de documentos. |
|[**Obtención del estado de los documentos**](get-documents-status.md)|Este método devuelve el estado de todos los documentos de un trabajo de traducción.|
|[**Obtención del estado del documento**](get-document-status.md)| Este método devuelve el estado de un documento específico en un trabajo. |
|[**Obtención del estado de las traducciones**](get-translations-status.md)| Este método devuelve una lista de todas las solicitudes de traducción enviadas por un usuario y el estado de cada solicitud.|
|[**Obtención del estado de la traducción**](get-translation-status.md) | Este método devuelve un resumen del estado de una solicitud de traducción de documentos específica. Incluye el estado general de la solicitud y el estado de los documentos que se traducen como parte de esa solicitud.|
|[**Cancelación de la traducción (DELETE)** ](cancel-translation.md)| Este método cancela una traducción de documentos que se está procesando actualmente o está en cola. |

> [!div class="nextstepaction"]
> [Explore nuestras bibliotecas cliente y SDK para C# y Python.](../client-sdks.md)