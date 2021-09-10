---
title: 'Preguntas más frecuentes: traducción de documentos'
titleSuffix: Azure Cognitive Services
description: Obtenga respuestas a las preguntas más frecuentes sobre la traducción de documentos en el servicio Translator de Azure Cognitive Services.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: lajanuar
ms.openlocfilehash: 18ce8c8032ca56ff0fa4dc039e862686c5a33d88
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114363558"
---
# <a name="document-translation-faq"></a>Preguntas más frecuentes sobre traducción de documentos

En este artículo se incluyen respuestas a preguntas frecuentes sobre traducción de documentos.

|Preguntas más frecuentes|
|:--|
|**¿Cuándo debo especificar el idioma de origen del documento en la solicitud?**<br/>Si se conoce el idioma del contenido del documento de origen, se recomienda especificar el idioma de origen en la solicitud para obtener una mejor traducción. Si el documento tiene contenido en varios idiomas o el idioma se desconoce, no especifique el idioma de origen en la solicitud. La traducción de documentos identifica automáticamente el idioma de cada segmento de texto y se traduce.|
|**¿Hasta qué punto se mantiene el diseño, la estructura y el formato?**<br/>Al traducir texto del idioma de origen al idioma de destino, la longitud total del texto traducido puede ser diferente a la del origen.  Esto podría dar lugar a un nuevo flujo de texto entre páginas. Es posible que las mismas fuentes no estén disponibles en el idioma de origen y de destino. En general, se aplica el mismo estilo de fuente en el idioma de destino para que el formato se parezca lo más posible al origen.|
|**¿Se traducirá el texto insertado en una imagen dentro de un documento?**<br/>No. El texto insertado en una imagen dentro de un documento no se traducirá.|
|**¿Traduce la traducción de documentos contenido de documentos digitalizados?**<br/>No. La traducción de documentos no traduce contenido de documentos digitalizados.|



