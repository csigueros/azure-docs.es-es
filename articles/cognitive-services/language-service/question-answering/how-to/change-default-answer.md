---
title: 'Obtención de una respuesta predeterminada: respuesta a preguntas personalizada'
description: Se devuelve la respuesta predeterminada cuando no hay ninguna coincidencia con la pregunta. Es posible que quiera cambiar la respuesta predeterminada estándar en la respuesta a preguntas personalizada.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
author: mrbullwinkle
ms:author: mbullwin
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 44f2ba3d3a1ae3e05cace797349a2680584a9aff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476573"
---
# <a name="change-default-answer-for-question-answering"></a>Cambio de la respuesta predeterminada en la respuesta a preguntas

La respuesta predeterminada para un proyecto está pensada para devolverse cuando no se encuentra una respuesta. Si usa una aplicación cliente, como [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna), es posible que también tenga una respuesta predeterminada independiente, lo que indica que ninguna respuesta ha alcanzado el umbral de puntuación.

## <a name="default-answer"></a>Respuesta predeterminada


|Respuesta predeterminada|Descripción de respuesta|
|--|--|
|Respuesta de KB si no se ha determinado ninguna respuesta|`No good match found in KB.`: si la API de respuesta a preguntas no encuentra ninguna respuesta coincidente para la pregunta, se muestra una respuesta de texto predeterminada. En la respuesta a preguntas, este texto se puede establecer en la pestaña **Settings** (Configuración) del proyecto. |

### <a name="client-application-integration"></a>Integración de aplicación cliente

En el caso de una aplicación cliente, como un bot con **Azure Bot Service**, puede elegir entre los siguientes escenarios comunes:

* Uso de la configuración del proyecto o de la base de conocimiento
* Usar otro texto en la aplicación cliente para distinguir cuándo se devuelve una respuesta, pero que no cumple el umbral de puntuación. Este texto puede ser texto estático almacenado en código, o se puede almacenar en la lista de configuración de la aplicación cliente.

## <a name="change-default-answer-in-language-studio"></a>Cambio de la respuesta predeterminada en Language Studio

Se devuelve la respuesta predeterminada de la base de conocimiento cuando la respuesta a preguntas no devuelve ningún resultado.

1. Inicie sesión en [Language Studio](https://language.azure.com) y seleccione su proyecto de la lista.
1. Seleccione **Settings** (Configuración) en la barra de navegación izquierda.
1. Cambie el valor de **Default answer when no answer found** (Respuesta predeterminada cuando no se encuentren resultados) y seleccione **Save** (Guardar).

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de la configuración del proyecto con un cuadro rojo alrededor de la respuesta predeterminada](../media/change-default-answer/settings.png)

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una base de conocimientos](manage-knowledge-base.md)
