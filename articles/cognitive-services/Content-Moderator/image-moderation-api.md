---
title: 'Moderación de imágenes: Content Moderator'
titleSuffix: Azure Cognitive Services
description: Use la moderación de imágenes asistida por máquina de Content Moderator y la Herramienta de revisión humana para moderar imágenes con contenido para adultos y explícitas.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 63273fc1d4da85d34bdad22f2c83890dbd137f4e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458444"
---
# <a name="learn-image-moderation-concepts"></a>Información sobre los conceptos de moderación de imágenes

Use la moderación de imágenes asistida por máquina de Content Moderator y la [herramienta de revisión](Review-Tool-User-Guide/human-in-the-loop.md) para moderar imágenes con contenido para adultos y subido de tono. Digitalice imágenes por su contenido de texto y extraiga dicho texto y detecte caras. Puede comparar las imágenes con las de listas personalizadas y, posteriormente, realizar las acciones necesarias.

## <a name="evaluating-for-adult-and-racy-content"></a>Evaluación de contenido para adultos y subido de tono

La operación **Evaluate** (Evaluar) devuelve una puntuación de confianza entre 0 y 1. También devuelve datos booleanos que equivalen a true o false. Estos valores predicen si la imagen tiene contenido potencial para adultos o subido de tono. Al llamar a la API con la imagen (archivo o dirección URL), la respuesta que se devuelve incluye la siguiente información:

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - `isImageAdultClassified` representa la posible presencia de imágenes que se pueden considerar sexualmente explícitas o para adultos en ciertas situaciones.
> - `isImageRacyClassified` representa la posible presencia de imágenes que se pueden considerar sexualmente sugerentes o para adultos en ciertas situaciones.
> - Las puntuaciones oscilan entre 0 y 1. Cuanto más alta sea la puntuación, mayor será la predicción del modelo sobre la aplicabilidad de la categoría. Esta versión preliminar usa un modelo estadístico, en lugar de resultados codificados manualmente. Se recomienda realizar pruebas con contenido propio para determinar cómo se alinea cada categoría en función de sus propios requisitos.
> - Los valores booleanos son true o false según los umbrales de puntuación internos. Los clientes deben evaluar si quieren usar este valor o decidir si desean umbrales personalizados basados en sus directivas de contenido.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Detección de texto con reconocimiento óptico de caracteres (OCR)

La operación **Optical Character Recognition (OCR)** (Reconocimiento óptico de caracteres) predice la presencia de contenido de texto en una imagen y lo extrae para moderarlo, entre otros usos. Puede especificar el idioma. Si no especifica un idioma, el valor predeterminado de la detección es inglés.

La respuesta incluye la siguiente información:
- El texto original.
- Los elementos de texto detectados con sus puntuaciones de confianza.

Extracción de ejemplo:

```json
"TextDetection": {
    "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
    },
    .........
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
},
```

## <a name="detecting-faces"></a>Detección de caras

La detección de caras ayuda a detectar datos personales, como las caras, en las imágenes. Detecte posibles caras y el número de caras posibles en cada imagen.

Una respuesta incluye esta información:

- Número de caras
- Lista de ubicaciones de las caras detectados

Extracción de ejemplo:

```json
"FaceDetection": {
    ......
    "result": true,
    "count": 2,
    "advancedInfo": [
        .....
    ],
    "faces": [
        {
            "bottom": 598,
            "left": 44,
            "right": 268,
            "top": 374
        },
        {
            "bottom": 620,
            "left": 308,
            "right": 532,
            "top": 396
        }
    ]
}
```

## <a name="creating-and-managing-custom-lists"></a>Creación y administración de listas personalizadas

En muchas de comunidades en línea, cuando los usuarios cargan imágenes u otro tipo de contenido, los elementos ofensivos se pueden compartir varias veces en los siguientes días, semanas y meses. Los costos del reiterado examen y filtrado de la misma imagen, o incluso versiones con pequeñas modificaciones de la misma, desde varios lugares pueden ser elevados y, además, son operaciones propensas a errores.

En lugar de moderar la misma imagen varias veces, agregue las imágenes ofensivas a su lista personalizada de contenido bloqueado. De esa forma, el sistema de moderación de contenido compara las imágenes entrantes con las de las listas personalizadas y detiene cualquier procesamiento posterior.

> [!NOTE]
> Hay un límite máximo de **5 listas de imágenes** y cada una de ellas **no debe superar las 10 000 imágenes**.
>

Content Moderator proporciona una completa [API de administración de listas de imágenes](try-image-list-api.md) con operaciones de administración de listas de imágenes personalizadas. Comience con la [consola de API de listas de imágenes](try-image-list-api.md) y use los ejemplos de código de la API REST. Consulte también la [guía de inicio rápido de .NET de listas de imágenes](image-lists-quickstart-dotnet.md) si está familiarizado con C# y Visual Studio.

## <a name="matching-against-your-custom-lists"></a>Coincidencia con listas personalizadas

La operación Match (Comparar) permite la coincidencia aproximada de las imágenes entrantes con las de cualquiera de sus listas personalizadas, que se crean y administran mediante las operaciones de List (Lista).

Si se encuentra una coincidencia, la operación devuelve el identificador y las etiquetas de moderación de la imagen coincidente. La respuesta incluye esta información:

- Puntuación de coincidencia (entre 0 y 1)
- Imagen coincidente
- Etiquetas de imagen (se asignan en la moderación anterior)
- Etiquetas de imagen

Extracción de ejemplo:

```json
{
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
}
```

## <a name="review-tool"></a>Herramienta de revisión

Para casos especiales, utilice la [Herramienta de revisión](Review-Tool-User-Guide/human-in-the-loop.md) de Content Moderator y su API para exponer los resultados de la moderación y el contenido de la revisión a los moderadores humanos. Ellos son los que revisan las etiquetas de asignadas por la máquina y confirman sus decisiones finales.

![Revisión de imagen para moderadores humanos](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Pasos siguientes

Use la versión de prueba de la [consola de Image Moderation API](try-image-api.md) y los ejemplos de código de la API REST. Consulte también [Revisiones, flujos de trabajo y trabajos](./review-api.md) para aprender a configurar revisiones humanas.
