---
title: 'Obtención de una respuesta predeterminada: respuesta a preguntas personalizada'
description: Se devuelve la respuesta predeterminada cuando no hay ninguna coincidencia con la pregunta. Es posible que quiera cambiar la respuesta predeterminada estándar en la respuesta a preguntas personalizada.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 94925a2a6cadc80a576470fc1d0a585bd4bb4a01
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093329"
---
# <a name="change-default-answer-for-question-answering"></a>Cambio de la respuesta predeterminada en la respuesta a preguntas

La respuesta predeterminada para un proyecto está pensada para devolverse cuando no se encuentra una respuesta. Si usa una aplicación cliente, como [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna), es posible que también tenga una respuesta predeterminada independiente, lo que indica que ninguna respuesta ha alcanzado el umbral de puntuación.

## <a name="types-of-default-answer"></a>Tipos de respuesta predeterminada

Hay dos tipos de respuesta predeterminada en la knowledge base. Es importante entender cómo y cuándo se devuelve cada una desde una consulta de predicción:

|Tipos de respuestas predeterminadas|Descripción de respuesta|
|--|--|
|Respuesta de KB si no se ha determinado ninguna respuesta|`No good match found in KB.`: si la API de respuesta a preguntas no encuentra ninguna respuesta coincidente para la pregunta, se muestra una respuesta de texto predeterminada. En la respuesta a preguntas personalizada, este texto se puede establecer en la pestaña **Settings** (Configuración) del proyecto. |
|Texto de instrucciones de solicitud de seguimiento|Al usar una solicitud de seguimiento en un flujo de conversación, es posible que no necesite una respuesta en el par de pregunta y respuesta porque quiere que el usuario seleccione en las solicitudes de seguimiento. En este caso, establezca un texto concreto al determinar el texto de respuesta predeterminado, que se devuelve con cada predicción de las solicitudes de seguimiento. El texto tiene que mostrarse como texto de instrucciones para la selección de solicitudes de seguimiento. Un ejemplo de este texto de respuesta predeterminado es `Please select from the following choices`. Esta configuración se explica en las siguientes secciones de este documento. También se puede establecer como parte de una definición de base de conocimiento con `defaultAnswerUsedForExtraction` mediante la API REST.|

### <a name="client-application-integration"></a>Integración de aplicación cliente

En el caso de una aplicación cliente, como un bot con **Azure Bot Service**, puede elegir entre los siguientes escenarios comunes:

* Uso de la configuración del proyecto o de la base de conocimiento
* Usar otro texto en la aplicación cliente para distinguir cuándo se devuelve una respuesta, pero que no cumple el umbral de puntuación. Este texto puede ser texto estático almacenado en código, o se puede almacenar en la lista de configuración de la aplicación cliente.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Establecimiento de la respuesta predeterminada de la solicitud de seguimiento al crear la knowledge base

Al crear una nueva base de conocimiento, el texto de respuesta predeterminado es uno de los valores de configuración. Si decide no establecerlo durante el proceso de creación, puede cambiarlo más adelante con el siguiente procedimiento.

## <a name="change-follow-up-prompts-default-answer-in-language-studio"></a>Cambio de la respuesta predeterminada de la solicitud de seguimiento en Language Studio

Se devuelve la respuesta predeterminada de la base de conocimiento cuando la respuesta a preguntas no devuelve ningún resultado.

1. Inicie sesión en [Language Studio](https://language.azure.com) y seleccione su proyecto de la lista.
1. Seleccione **Settings** (Configuración) en la barra de navegación izquierda.
1. Cambie el valor de **Default answer when no answer found** (Respuesta predeterminada cuando no se encuentren resultados) y seleccione **Save** (Guardar).

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de la configuración del proyecto con un cuadro rojo alrededor de la respuesta predeterminada](../media/change-default-answer/settings.png)

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una base de conocimientos](manage-knowledge-base.md)
