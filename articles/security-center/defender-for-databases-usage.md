---
title: Configuración y respuesta a las alertas de Azure Defender para bases de datos relacionales de código abierto
description: Aprenda a configurar Azure Defender para bases de datos relacionales de código abierto a fin de detectar actividades anómalas en la base de datos que indican posibles amenazas de seguridad.
author: memildin
ms.author: memildin
ms.date: 06/17/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1300a8ad11d7091085c95205b435a2d3fb077cd3
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369976"
---
# <a name="enable-azure-defender-for-open-source-relational-databases-and-respond-to-alerts"></a>Habilitación de Azure Defender para bases de datos relacionales de código abierto y respuesta a alertas

Azure Defender detecta actividades anómalas que indican intentos inusuales y potencialmente peligrosos de acceso o vulneración de seguridad de las bases de datos para los servicios siguientes:

- [Azure Database para PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Para obtener alertas del plan de Azure Defender, primero tendrá que habilitarlo como se [muestra a continuación](#enable-azure-defender).

Obtenga más información sobre este plan de Azure Defender en [Introducción a Azure Defender para bases de datos relacionales de código abierto](defender-for-databases-introduction.md).

## <a name="enable-azure-defender"></a>Habilitación de Azure Defender

1. En [Azure Portal](https://portal.azure.com), abra la página de configuración del servidor de bases de datos que quiere proteger.

1. En el menú Seguridad de la izquierda, seleccione **Security Center**.

1. Si Azure Defender no está habilitado, verá un botón como se muestra en la captura de pantalla siguiente. Seleccione **Habilitar Azure Defender para [tipo de base de datos]** (por ejemplo, "Azure Defender para MySQL") y seleccione **Guardar**.

    :::image type="content" source="media/defender-for-databases-usage/enable-defender-for-mysql.png" alt-text="Habilitación de Azure Defender para MySQL." lightbox="media/defender-for-databases-usage/enable-defender-for-mysql.png":::

    > [!TIP]
    > Esta página del portal será la misma independientemente del tipo de base de datos (PostgreSQL, MySQL o MariaDB).

## <a name="respond-to-security-alerts"></a>Responder a alertas de seguridad

Cuando Azure Defender está habilitado en la base de datos, detecta actividades anómalas y genera alertas. Estas alertas están disponibles desde varias ubicaciones, entre las que se incluyen:

- En Azure Portal:
    - **Página de alertas de seguridad Azure Defender**: muestra las alertas de todos los recursos protegidos por Azure Defender en las suscripciones que tiene permisos para ver.
    - Página **Security Center** del recurso: muestra alertas y recomendaciones para un recurso específico, como se ha mostrado antes en [Habilitación de Azure Defender](#enable-azure-defender).
- En la Bandeja de entrada del usuario de la organización que se haya [designado para recibir alertas por correo electrónico](security-center-provide-security-contact-details.md).  

> [!TIP]
> Un icono dinámico en el [panel de información general de Azure Security Center](overview-page.md) realiza el seguimiento del estado de las amenazas activas para todos los recursos, incluidas las bases de datos. Seleccione el icono para abrir la página de alertas de Azure Defender y obtener información general de las amenazas activas detectadas en las bases de datos.
>
> Para obtener pasos detallados y el método recomendado para responder a las alertas de Azure Defender, vea [Respuesta a una alerta de seguridad](tutorial-security-incident.md#respond-to-a-security-alert).


### <a name="respond-to-email-notifications-of-security-alerts"></a>Respuesta a notificaciones por correo electrónico de alertas de seguridad

Azure Defender envía notificaciones por correo electrónico cuando detecta actividades anómalas en la base de datos. El correo electrónico incluye detalles del evento de seguridad sospechoso, como la naturaleza de las actividades anómalas, el nombre de la base de datos, del servidor o de la aplicación, y la hora del evento. El correo electrónico también proporciona información sobre las posibles causas y las medidas recomendadas para investigar y mitigar las amenazas potenciales para la base de datos.

1. Seleccione el vínculo **View the full alert** (Ver la alerta completa) del correo electrónico para abrir Azure Portal y mostrar la página de alertas de Azure Security Center, en la que se proporciona información general sobre las amenazas activas detectadas en la base de datos.
    
    :::image type="content" source="media/defender-for-databases-usage/suspected-brute-force-attack-notification-email.png" alt-text="Notificación por correo electrónico de Azure Defender de sobre un posible ataque por fuerza bruta.":::

    Vea las amenazas activas en el nivel de suscripción desde las páginas del portal Security Center:

    :::image type="content" source="media/defender-for-databases-usage/db-alerts-page.png" alt-text="Las amenazas activas en una o varias suscripciones se muestran en Azure Security Center." lightbox="media/defender-for-databases-usage/db-alerts-page.png":::

1. Para obtener detalles adicionales y las acciones recomendadas para investigar la amenaza actual y corregir las amenazas futuras, seleccione una alerta específica.
    
    :::image type="content" source="media/defender-for-databases-usage/specific-alert-details.png" alt-text="Detalles de una alerta específica." lightbox="media/defender-for-databases-usage/specific-alert-details.png":::


> [!TIP]
> Para obtener un tutorial detallado sobre cómo controlar las alertas, vea [Tutorial: Análisis de las alertas de seguridad, investigación y respuesta a estas](tutorial-security-incident.md).


## <a name="next-steps"></a>Pasos siguientes

- [Creación de respuestas automáticas a alertas y recomendaciones con automatización del flujo de trabajo](workflow-automation.md)
- [Transmisión de alertas a una solución SIEM, SOAR o ITSM](export-to-siem.md)
- [Eliminación de alertas de Azure Defender](alerts-suppression-rules.md)