---
title: 'Tutorial: Depuración de API en Azure API Management mediante el seguimiento de solicitudes'
description: Siga los pasos de este tutorial para habilitar los pasos de seguimiento e inspección del procesamiento de solicitudes en Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: danlep
ms.openlocfilehash: 59d095b45b10ad6ed093107cc8d4589e0915161b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643315"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Tutorial: Depuración de las API con el seguimiento de solicitudes

En este tutorial se describe cómo inspeccionar (realizar un seguimiento) del procesamiento de solicitudes en Azure API Management para ayudarle a depurar y solucionar los problemas de la API. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Realizar el seguimiento de una llamada de ejemplo
> * Revisar los pasos del procesamiento de solicitudes

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API Inspector":::

## <a name="prerequisites"></a>Requisitos previos

+ Conocer la [terminología de API Management de Azure](api-management-terminology.md).
+ Complete el siguiente inicio rápido: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Realice el siguiente tutorial: [Importación y publicación de la primera API](import-and-publish.md).

## <a name="verify-allow-tracing-setting"></a>Comprobación del valor Permitir seguimiento 

Se debe habilitar el valor **Permitir seguimiento** para la suscripción que se usa para la API. Si usa la suscripción integrada de acceso a todo, este valor está habilitado de forma predeterminada. Para comprobarlo en el portal, vaya a la instancia de API Management y seleccione **Suscripciones**.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Permitir seguimiento de la suscripción":::

## <a name="trace-a-call"></a>Realizar el seguimiento de una llamada

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a la instancia de API Management.
1. Seleccione **API**.
1. Seleccione **Demo Conference API** en la lista de API.
1. Seleccione la pestaña **Prueba**.
1. Seleccione la operación **GetSpeakers**.
1. Confirme que el encabezado de la solicitud HTTP incluye **Ocp-Apim-Trace: True** y un valor válido para **Ocp-Apim-Subscription-Key**. Si no es así, seleccione **+ Agregar encabezado** para agregar el encabezado.
1. Seleccione **Enviar** para realizar una llamada API.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="Configuración del seguimiento de API":::

> [!TIP]
> Si **Ocp-Apim-Subscription-Key** no se rellena automáticamente en la solicitud HTTP, puede recuperarla en el portal. Seleccione **Suscripciones** y abra el menú contextual ( **...** ) de la suscripción. Seleccione **Mostrar u ocultar claves**. También puede regenerar claves si es necesario. Después, agregue una clave al encabezado.

## <a name="review-trace-information"></a>Revisión de la información de seguimiento

1. Una vez finalizada la llamada, vaya a la pestaña **Seguimiento** en **Respuesta HTTP**.
1. Seleccione cualquiera de los siguientes vínculos para saltar a la información de seguimiento detallada: **entrada**, **back-end**, **salida**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Revisión del seguimiento de respuestas":::

    * **Entrada**: muestra la solicitud original API Management recibida del autor de llamada y las directivas aplicadas a la solicitud. Por ejemplo, si agregó directivas en [Tutorial: Transformación y protección de una API](transform-api.md), aparecerán aquí.

    * **Back-end**: muestra las solicitudes que API Management envió al back-end de la API y la respuesta que recibió.

    * **Salida**: muestra todas las directivas que se aplican a la respuesta antes de enviarla de vuelta al autor de llamada.

    > [!TIP]
    > Cada paso muestra también el tiempo transcurrido desde que API Management recibe la solicitud.

1. En la pestaña **Mensaje**, el encabezado **ocp-apim-trace-location** muestra la ubicación de los datos de seguimiento almacenados en Azure Blob Storage. Si es necesario, vaya a esta ubicación para recuperar el seguimiento.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Ubicación de seguimiento en Azure Storage":::
## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Realizar el seguimiento de una llamada de ejemplo
> * Revisar los pasos del procesamiento de solicitudes

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Uso de revisiones](api-management-get-started-revise-api.md)
