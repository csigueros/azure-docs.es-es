---
title: Creación de knowledge bases en varios idiomas
description: En este tutorial aprenderá a crear knowledge bases con varios idiomas.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 41b15b70434c2515349074088d3b049de7482b1a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235273"
---
# <a name="create-knowledge-bases-in-multiple-languages"></a>Creación de knowledge bases en varios idiomas

Este tutorial le guiará por el proceso de creación de knowledge bases en varios idiomas. Usamos la dirección URL [Preguntas frecuentes del Lápiz para Surface](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)para crear las knowledge bases en alemán e inglés. A continuación, las publicamos y usamos [GenerateAnswerAPI](/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Runtime/GenerateAnswer) para consultarlas y obtener respuestas a las preguntas frecuentes en el idioma deseado.

## <a name="create-knowledge-base-in-german"></a>Creación de la knowledge base en alemán

Para poder crear una knowledge base en más de un idioma, el valor del idioma debe establecerse en la creación de la primera knowledge base (KB) del servicio QnA.

> [!NOTE]
> La opción para habilitar la adición de knowledge bases en varios idiomas a un servicio solo está disponible como parte de la respuesta a preguntas personalizada, que es una característica de Text Analytics.
>
> Si usa la versión de disponibilidad general de QnA Maker, es necesario crear un recurso de QnA Maker independiente para cada idioma distinto.

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la interfaz de usuario para conectar el servicio QnA a su knowledge base con la opción para agregar knowledge bases en varios idiomas seleccionada]( ../media/multiple-languages/add-knowledge-bases.png) ]( ../media/multiple-languages/add-knowledge-bases.png#lightbox)

En el **paso 2**: habilite "Add knowledge bases in multiple languages to this service" (Agregar knowledge bases en varios idiomas a este servicio) y elija **Alemán** como idioma de la KB en la lista desplegable "Idioma".
Rellene los detalles pertinentes en los pasos 3 y 4 y, por último, seleccione **Create your KB** (Crear la KB).
 
En este paso, QnA Maker lee el documento y extrae pares de pregunta y respuesta de la dirección URL de origen para crear la knowledge base en alemán. Se abre la página de la knowledge base, donde podemos editar el contenido.

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la interfaz de usuario con preguntas y respuestas en alemán]( ../media/multiple-languages/german.png) ]( ../media/multiple-languages/german.png#lightbox)

## <a name="create-knowledge-base-in-english"></a>Creación de la knowledge base en inglés

Ahora se repiten los pasos anteriores con cambios específicos del idioma en los pasos 2 y 4 para crear la knowledge base en inglés:
1.  **Paso 2**: Elija como idioma el **Inglés**.
2.  **Paso 4**: Seleccione el archivo de origen en el idioma seleccionado para crear una knowledge base en inglés.
Una vez creada knowledge base, podemos ver los pares de pregunta y respuesta pertinentes generados por QnA Maker en inglés, como se muestra a continuación:

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la respuesta de la API REST]( ../media/multiple-languages/english.png) ]( ../media/multiple-languages/english.png#lightbox)

## <a name="publish-and-query-knowledge-base"></a>Publicación y consulta de la knowledge base

Ahora estamos listos para publicar las dos knowledge bases y consultarlas en el idioma deseado mediante [GenerateAnswerAPI](/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Runtime/GenerateAnswer). Una vez publicada la knowledge base, se muestra la página siguiente, que proporciona detalles para consultar la knowledge base.

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la interfaz de usuario con preguntas y respuestas en inglés]( ../media/multiple-languages/postman.png) ]( ../media/multiple-languages/postman.png#lightbox)

El idioma de la consulta de usuario entrante se puede detectar con la [API Detección de idioma](../../text-analytics/how-tos/text-analytics-how-to-language-detection.md), y el usuario puede llamar al punto de conexión y la knowledge base adecuados según el idioma detectado.