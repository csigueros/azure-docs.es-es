---
title: ¿Qué es Computer Vision?
titleSuffix: Azure Cognitive Services
description: El servicio Computer Vision proporciona acceso a algoritmos avanzados para procesar imágenes y devolver información.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 06/21/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: computer vision, computer vision applications, computer vision service
ms.openlocfilehash: c78f12d484cb1c0e4cda80867af67bc8af7f6c55
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460250"
---
# <a name="what-is-computer-vision"></a>¿Qué es Computer Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

El servicio Computer Vision de Azure proporciona acceso a algoritmos avanzados que procesan imágenes y devuelven información basada en las características visuales de interés. 

| Servicio|Descripción|
|---|---|
| [Reconocimiento óptico de caracteres (OCR)](overview-ocr.md)|El servicio de reconocimiento óptico de caracteres (OCR) extrae el texto de las imágenes. Puede usar la nueva Read API para extraer el texto impreso y manuscrito de imágenes y documentos. Usa modelos basados en aprendizaje profundo y funciona con texto en diversas superficies y fondos. Entre estos se incluyen documentos de la empresa, facturas, recibos, pósteres, tarjetas de presentación, cartas y pizarras. Las API de OCR admiten la extracción de texto impreso en [varios idiomas](./language-support.md). Siga nuestro [inicio rápido con OCR](quickstarts-sdk/client-library.md) para comenzar.|
|[Análisis de imágenes](overview-image-analysis.md)| El servicio Image Analysis extrae muchas características visuales de las imágenes, como objetos, caras, contenido para adultos y descripciones de texto generadas automáticamente. Siga los [artículos de inicio rápido de Image Analysis](quickstarts-sdk/image-analysis-client-library.md) para comenzar.|
| [Análisis espacial](intro-to-spatial-analysis-public-preview.md)| El servicio de análisis espacial analiza la presencia y el movimiento de personas en una fuente de vídeo y genera eventos a los que pueden responder otros sistemas. Instale el [contenedor de análisis espacial](spatial-analysis-container.md) para comenzar.|

## <a name="computer-vision-for-digital-asset-management"></a>Computer Vision para la administración de activos digitales

Computer Vision puede funcionar en muchos escenarios de administración de activos digitales (DAM). DAM es el proceso empresarial de organización, almacenamiento y recuperación de recursos multimedia enriquecidos, y la administración de los permisos y derechos digitales. Por ejemplo, es posible que una empresa desee agrupar e identificar imágenes basadas en logotipos, caras, objetos, colores visibles, etc. O bien, puede que desee [generar automáticamente leyendas para las imágenes](./Tutorials/storage-lab-tutorial.md) y adjuntar palabras clave para que admitan búsquedas. Para una solución DAM todo en uno que use Cognitive Services, Azure Cognitive Search e informes inteligentes, vea la [Guía del acelerador de la solución de minería del conocimiento](https://github.com/Azure-Samples/azure-search-knowledge-mining) en GitHub. Para ver otros ejemplos de DAM, consulte el repositorio [plantillas de la solución Computer Vision](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates).

## <a name="image-requirements"></a>Requisitos de imagen

Computer Vision puede analizar las imágenes que cumplan los requisitos siguientes:

- La imagen se debe presentar en formato JPEG, PNG, GIF o BMP
- El tamaño de archivo de la imagen debe ser inferior a 4 megabytes (MB)
- Las dimensiones de la imagen deben ser mayores que 50 x 50 píxeles
  - Para la API Read, las dimensiones de la imagen deben estar entre 50 x 50 y 10 000 x 10 000 píxeles.

## <a name="data-privacy-and-security"></a>Seguridad y privacidad de datos

Al igual que sucede con todas las instancias de Cognitive Services, los desarrolladores que usan el servicio Computer Vision deben estar al tanto de las directivas de Microsoft sobre los datos de clientes. Para más información, consulte la [página de Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) en Microsoft Trust Center.

## <a name="next-steps"></a>Pasos siguientes

Siga un inicio rápido para implementar y ejecutar un servicio en el lenguaje de desarrollo que prefiera.

* [Inicio rápido: Reconocimiento óptico de caracteres (OCR)](quickstarts-sdk/client-library.md)
* [Inicio rápido: Image Analysis](quickstarts-sdk/image-analysis-client-library.md)
* [Inicio rápido: Contenedor de análisis espacial](spatial-analysis-container.md)
