---
title: 'Tutorial: Creación de un bot de preguntas más frecuentes con respuesta a preguntas y Azure Bot Service'
description: En este tutorial, va a crear un bot de preguntas más frecuentes sin escribir código con respuesta a preguntas y Azure Bot Service.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 3478ab7b9a9d1920bcc5dc61b108b80514f60aba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439070"
---
# <a name="tutorial-create-a-faq-bot"></a>Tutorial: Creación de un bot de preguntas más frecuentes

Cree un bot de preguntas frecuentes con respuesta a las preguntas personalizada y Azure [Bot Service](https://azure.microsoft.com/services/bot-service/) sin código.

En este tutorial aprenderá a:

<!-- green checkmark -->
> [!div class="checklist"]
> * Vincular un proyecto de respuesta a preguntas y una base de conocimiento a una instancia de Azure Bot Service
> * Implementar un bot
> * Probar el bot en Chat en web
> * Habilitar el bot en los canales admitidos

## <a name="create-and-publish-a-knowledge-base"></a>Crear y publicar una base de conocimiento

Siga el [artículo de introducción](../how-to/create-test-deploy.md). Una vez que se haya implementado correctamente la base de conocimiento, estará listo para iniciar este artículo.

## <a name="create-a-bot"></a>Creación de un bot

Después de implementar el proyecto y la base de conocimiento, puede crear un bot desde la página **Deploy knowledge base** (Implementar base de conocimiento):

* Puede crear varios bots rápidamente, todos ellos apuntando a la misma base de conocimiento para diferentes regiones o planes de precios para los bots individuales.

* Si realiza cambios en la base de conocimiento y vuelve a implementarla, no es necesario realizar ninguna acción más con el bot. Ya está configurado para funcionar con la base de conocimiento y funciona con todos los cambios futuros de la base de conocimiento. Cada vez que publique una base de conocimiento, todos los bots conectados a ella se actualizarán automáticamente.

1. En el portal de Language Studio, en la página **Deploy knowledge base** (Implementar base de conocimiento) de la respuesta a preguntas, seleccione **Create bot** (Crear bot).

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la interfaz de usuario con la opción para crear un bot en Azure.](../media/bot-service/create-bot-in-azure.png)

1. Se abre una nueva pestaña del explorador para Azure Portal, con la página de creación de Azure Bot Service. Configure Azure Bot Service.

    |Configuración |Valor|
    |----------|---------|
    | Bot handle (Identificador de bot)| Identificador único del bot. Este valor debe ser distinto del nombre de la aplicación. |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione un grupo de recursos existente o cree uno nuevo. |
    | Location | Seleccione la ubicación deseada. |
    | Plan de tarifa | Elegir plan de tarifa |
    |Nombre de la aplicación | Nombre de la aplicación de App Service del bot |
    |Lenguaje de SDK | C# o Node.js. Una vez creado el bot, puede descargar el código en el entorno de desarrollo local y continuar el proceso de desarrollo. |
    | Clave de autenticación de QnA | Esta clave se rellena automáticamente con el proyecto de respuesta a preguntas y la base de conocimiento implementados. |
    | Plan de App Service/ubicación | Este valor se rellena automáticamente, no cambie este valor. |

1. Una vez creado el bot, abra el recurso **Servicio de bots**.
1. En **Configuración**, seleccione **Probar en el Chat en web**.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la interfaz de usuario de Azure Bot Service que muestra "Chat en web de prueba".](../media/bot-service/test-in-web-chat.png)

1. En el aviso de chat **Escriba su mensaje**, escriba:

    `How do I setup my surface book?`

    El bot de chat muestra una respuesta de la base de conocimiento.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la respuesta del chat de prueba del bot que contiene una pregunta y una respuesta generada por el bot.](../media/bot-service/bot-chat.png)

## <a name="integrate-the-bot-with-channels"></a>Integración del bot con canales

Seleccione **Canales** en el recurso de Bot Service que ha creado. Puede activar el bot en otros [canales admitidos](/azure/bot-service/bot-service-manage-channels).

   >[!div class="mx-imgBorder"]
   >![Captura de pantalla de la integración con Teams con iconos de producto.](../media/bot-service/channels.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine los recursos de respuesta a preguntas y Bot Service asociados.

## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo para obtener información sobre cómo personalizar el bot de preguntas más frecuentes con avisos de varios turnos.
> [!div class="nextstepaction"]
> [Avisos de múltiples turnos](guided-conversations.md)
