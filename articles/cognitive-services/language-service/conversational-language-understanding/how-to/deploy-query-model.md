---
title: Cómo enviar un trabajo de Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Información sobre cómo enviar una solicitud de Conversational Language Understanding
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: e1f53678ef99fb5477700628e14127e5572da816
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091045"
---
# <a name="deploy-and-test-model"></a>Implementación y prueba del modelo

Después de [entrenar un modelo](./train-model.md) en el conjunto de datos, está listo para implementarlo. Después de implementarlo, podrá consultarlo para obtener predicciones. 

## <a name="deploy-model"></a>Implementación de un modelo

La implementación de un modelo consiste en hospedarlo y hacer que esté disponible para las predicciones a través de un punto de conexión. Solo se puede tener un modelo implementado por proyecto; la implementación de otro sobrescribirá el modelo implementado anteriormente.

Cuando se implementa un modelo, podrá probarlo directamente en el portal o llamando a la API asociada a él.

Solo tiene que seleccionar un modelo y hacer clic en Implementar modelo en la página Implementar modelo. 

:::image type="content" source="../media/deploy-model.png" alt-text="Captura de pantalla de la página de implementación del modelo en Language Studio" lightbox="../media/deploy-model.png":::

**Implementaciones de proyectos de flujo de trabajo de orquestación**

Al implementar un proyecto de flujo de trabajo de orquestación, aparecerá una ventana pequeña para confirmar la implementación y configurar parámetros de los servicios conectados.

Si va a conectar una o varias aplicaciones de LUIS, especifique el nombre de implementación y si usa la implementación de tipo de *ranura* o *versión*.       
* Una implementación de tipo *ranura* requiere elegir entre un espacio de producción y uno de ensayo.
* Una implementación de tipo *versión* requiere especificar la versión que se ha publicado.

No se requiere ninguna configuración para las respuestas a preguntas personalizadas y conexiones de CLU, ni intenciones desvinculadas.

Los proyectos de LUIS **deben publicarse** en el espacio configurado durante la implementación de orquestación, y las knowledge base de respuesta a preguntas personalizada también deben publicarse en espacios de producción.

:::image type="content" source="../media/deploy-connected-services.png" alt-text="Captura de pantalla de la pantalla de implementación de los proyectos de flujo de trabajo de orquestación" lightbox="../media/deploy-connected-services.png":::

## <a name="send-a-conversational-language-understanding-request"></a>Envío de una solicitud de Conversational Language Understanding

Una vez implementado el modelo, puede empezar a usar el modelo implementado para hacer predicciones. Fuera de la página de prueba del modelo, puede empezar a llamar al modelo implementado mediante el envío de solicitudes API al punto de conexión personalizado proporcionado. Esta solicitud de punto de conexión obtiene las predicciones de intención y entidad definidas en el modelo.

Para obtener la dirección URL completa del punto de conexión, vaya a la página **Implementar modelo**, seleccione el modelo implementado y haga clic en "Get prediction URL" (Obtener dirección URL de predicción).

:::image type="content" source="../media/prediction-url.png" alt-text="Captura de pantalla de la dirección URL y la solicitud de predicción" lightbox="../media/prediction-url.png":::

## <a name="api-response-for-a-conversations-project"></a>Respuesta de API de un proyecto de conversaciones

En un proyecto de conversaciones, se obtienen predicciones para las intenciones y las entidades existentes en el proyecto. 
- Las intenciones y entidades incluyen una puntuación de confianza de entre 0,0 y 1,0 asociada a la confianza del modelo en la predicción de un determinado elemento del proyecto. 
- La intención con la mayor puntuación se encuentra dentro de su propio parámetro.
- Solo las entidades predichas se mostrarán en la respuesta.
- Las entidades indican lo siguiente:
    - El texto de la entidad que se ha extraído
    - Su ubicación inicial, indicada mediante un valor de desplazamiento
    - La longitud del texto de la entidad, indicada mediante un valor de longitud

## <a name="api-response-for-an-orchestration-workflow-project"></a>Respuesta de API de un proyecto de flujo de trabajo de orquestación

Un proyecto de flujo de trabajo de orquestación regresa con la respuesta de la intención con la mayor puntuación y la respuesta del servicio al que está conectado.
- En la intención, el parámetro *targetKind* permite determinar el tipo de respuesta que devolvió la intención principal del orquestador (conversación, LUIS o QnA Maker).
- Recibirá la respuesta del servicio conectado en el parámetro *result*. 

Dentro de la solicitud, puede especificar parámetros adicionales para cada servicio conectado, en caso de que el orquestador enrute a ese servicio.
- Dentro de los parámetros del proyecto, puede especificar opcionalmente una consulta distinta en el servicio conectado. Si no especifica una consulta distinta, se usará la consulta original.
- El parámetro *direct target* permite omitir la decisión de enrutamiento del orquestador y dirigirse directamente a una intención conectada específica para forzar una respuesta.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción al reconocimiento del lenguaje conversacional](../overview.md)
