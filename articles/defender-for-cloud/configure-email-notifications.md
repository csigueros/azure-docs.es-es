---
title: Configuración de notificaciones de correo electrónico para alertas de Microsoft Defender for Cloud
description: Aprenda a configurar los correos electrónicos de alertas de seguridad de Microsoft Defender for Cloud.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 63123cf7dc7257578b4ddcf3afaf272f42d599d5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525796"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Configuración de notificaciones de alertas de seguridad por correo electrónico 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Las alertas de seguridad deben llegar a las personas adecuadas de la organización. De manera predeterminada, Microsoft Defender for Cloud envía mensajes de correo electrónico a los propietarios de la suscripción cada vez que se desencadena una alerta de gravedad alta para su suscripción. En esta página se explica cómo personalizar estas notificaciones.

Use la página de configuración **Notificaciones de correo electrónico** de Defender for Cloud para definir las preferencias de los correos electrónicos de notificación, entre las que se incluyen:

- ***A quién* se debe notificar**: se pueden enviar mensajes de correo electrónico a usuarios individuales o a cualquier persona con un rol de Azure especificado para una suscripción. 
- ***Qué* se les debe notificar**: modifique los niveles de gravedad para los que Defender for Cloud debe enviar notificaciones.

Para evitar la fatiga por alertas, Defender for Cloud limita el volumen de correos electrónicos salientes. Para cada suscripción, Defender for Cloud envía:

- un máximo de un correo electrónico cada **6 horas** (4 correos electrónicos al día) para alertas de **gravedad alta**.
- un máximo de un correo electrónico cada **12 horas** (2 correos electrónicos al día) para alertas de **gravedad media**.
- un máximo de un correo electrónico cada **24 horas** (1 correo electrónico al día) para alertas de **gravedad baja**.

:::image type="content" source="./media/configure-email-notifications/email-notification-settings.png" alt-text="Configuración de los detalles de los contactos que recibirán correos electrónicos sobre las alertas de seguridad" :::
 
## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|Gratuito|
|Roles y permisos necesarios:|**Administrador de seguridad**<br>**Propietario de la suscripción** |
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Nacional (Azure Government, Azure China 21Vianet)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>Personalización de las notificaciones de alertas de seguridad por correo electrónico mediante el portal<a name="email"></a>
Puede enviar notificaciones por correo electrónico a individuos o a todos los usuarios con roles de Azure específicos.

1. En el área **Configuración del entorno** de Defender for Cloud, seleccione la suscripción correspondiente y **Notificaciones de correo electrónico**.

1. Defina los destinatarios de las notificaciones con una o ambas de estas opciones:

    - En la lista desplegable, seleccione entre los roles disponibles.
    - Escriba direcciones de correo electrónico específicas, separadas por comas. No hay ningún límite en el número de direcciones de correo electrónico que se pueden escribir.

1. Seleccione **Guardar** para aplicar la información de contacto de seguridad a su suscripción.

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>Personalización de las notificaciones de alertas por correo electrónico mediante la API
También puede administrar las notificaciones por correo electrónico mediante la API REST proporcionada. Para información completa, consulte la [documentación de la API SecurityContacts](/rest/api/securitycenter/securitycontacts).

Este es un cuerpo de solicitud de ejemplo para la solicitud PUT al crear una configuración de contacto de seguridad:

Identificador URI: `https://management.azure.com/subscriptions/<SubscriptionId>/providers/Microsoft.Security/securityContacts/default?api-version=2020-01-01-preview`

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "Medium"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>Consulte también
Para más información sobre las alertas de seguridad, consulte las siguientes páginas:

- [Alertas de seguridad: una guía de referencia](alerts-reference.md): obtenga información sobre las alertas de seguridad que puede ver en el módulo de protección contra amenazas de Microsoft Defender for Cloud
- [Administración y respuesta a las alertas de seguridad en Microsoft Defender for Cloud](managing-and-responding-alerts.md): aprenda a administrar y responder a alertas de seguridad
- [Automatización de flujos de trabajo](workflow-automation.md): automatice respuestas a alertas con lógica de notificación personalizada
