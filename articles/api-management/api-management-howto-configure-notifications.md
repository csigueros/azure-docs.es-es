---
title: Configurar notificaciones y plantillas de correo electrónico
titleSuffix: Azure API Management
description: Obtenga información acerca de cómo configurar notificaciones y plantillas de correo electrónico en Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: ddea6f64aa4c3f78116e30ba8c9731acebfbb006
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734487"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Configuración de notificaciones y plantillas de correo electrónico en Azure API Management

Administración de API ofrece la posibilidad de configurar notificaciones de eventos específicos, así como plantillas de correo electrónico que se usan para comunicarse con los administradores y desarrolladores de una instancia de API Management. Este artículo muestra cómo configurar las notificaciones de los eventos disponibles y ofrece información general sobre la configuración de plantillas de correo electrónico que se usan para estos eventos.

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una instancia del servicio de API Management, complete el tutorial siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Configuración de notificaciones

1.  Seleccione su instancia de **API MANAGEMENT**.
2.  Haga clic en **Notificaciones** para ver las notificaciones disponibles.

    ![Notificaciones del publicador][api-management-publisher-notifications]

    Se puede configurar la siguiente lista de eventos para notificaciones.

    -   **Solicitudes de suscripción (se requiere aprobación)** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico sobre solicitudes de suscripción de productos de API que requieran aprobación.
    -   **Nuevas suscripciones** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico sobre nuevas suscripciones de productos de API.
    -   **Solicitudes de la galería de aplicaciones** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico cuando se envíen nuevas aplicaciones a la galería de aplicaciones.
    -   **CCO** : los destinatarios y usuarios de correo electrónico especificados recibirán copias carbón ocultas de todos los correos electrónicos enviados a los desarrolladores.
    -   **Nuevo problema o comentario** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico cuando se envíe un nuevo problema o comentario en el portal para desarrolladores.
    -   **Cerrar mensaje de cuenta** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico cuando se cierre una cuenta.
    -   **Aproximación al límite de cuota de suscripción** : los siguientes destinatarios y usuarios de correo electrónico recibirán notificaciones por correo electrónico cuando el uso de la suscripción se acerque a la cuota de uso.

        > [!NOTE]
        > Solo la directiva de [cuota por suscripción](api-management-access-restriction-policies.md#SetUsageQuota) desencadena las notificaciones. La directiva de [cuota por clave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) no genera notificaciones.

    En cada evento, se pueden especificar destinatarios con el cuadro de texto de dirección de correo electrónico o seleccionar usuarios de una lista.

3.  Para especificar las direcciones de correo electrónico a las que se van a enviar notificaciones, especifíquelas en el cuadro de texto de dirección de correo electrónico. Si tiene varias direcciones de correo electrónico, sepárelas con comas.

    ![Notification recipients][api-management-email-addresses]

4.  Presione **Agregar**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Configuración de plantillas de notificación

API Management proporciona plantillas de notificación para los mensajes de correo electrónico que se envían durante la administración y el uso del servicio. Se incluyen las siguientes plantillas de correo electrónico.

-   Envío de la galería de aplicaciones aprobado
-   Carta de despedida del desarrollador
-   Notificación de aproximación del límite de cuota del desarrollador
-   Invitación a un usuario
-   Nuevo comentario agregado a un problema
-   Nuevo problema recibido
-   Nueva suscripción activada
-   Confirmación de suscripción renovada
-   Rechazo de la solicitud de suscripción
-   Solicitud de suscripción recibida

Estas plantillas se pueden modificar tal como se desee.

Para ver y configurar las plantillas de correo electrónico para la instancia de API Management, haga clic en **Plantillas de notificaciones**.

![Plantillas de correo electrónico][api-management-email-templates]

Cada plantilla de correo electrónico tiene un asunto en texto sin formato y una definición del cuerpo en formato HTML. Cada elemento se puede personalizar según se desee.

![Email template editor][api-management-email-template]

La lista **Parámetros** contiene una lista de parámetros que, al insertarlos en el asunto o en el cuerpo, se reemplazarán por el valor designado cuando se envíe el correo electrónico. Para insertar un parámetro, sitúe el cursor en donde desee que vaya el parámetro y haga clic en la flecha a la izquierda del nombre del parámetro.

Para guardar los cambios efectuados en la plantilla de correo electrónico, haga clic en **Guardar** o, si desea cancelarlos, haga clic en **Descartar**.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
