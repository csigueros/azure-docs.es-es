---
title: Características de seguridad mejorada de Microsoft Defender for Cloud
description: Aprenda a habilitar las características de seguridad mejorada de Microsoft Defender for Cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f194a0326f4caa90fc57bfd9af2991a9f4e1fbeb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017567"
---
# <a name="quickstart-set-up-microsoft-defender-for-cloud"></a>Inicio rápido: Configuración de Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud proporciona administración unificada de la seguridad y protección contra amenazas para cargas de trabajo híbridas y de varias nubes. Aunque las características gratis solo ofrecen una seguridad limitada para los recursos de Azure, al habilitar las características de seguridad mejorada, se amplían estas funcionalidades tanto a un entorno local como a otras nubes. Defender for Cloud le ayuda a encontrar y corregir los puntos vulnerables de seguridad, aplicar controles de acceso y de aplicación para bloquear actividades malintencionadas, detectar amenazas mediante análisis e inteligencia, y responder rápidamente en caso de ataque. Puede probar las características de seguridad mejorada sin costo alguno. Para más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).

Esta sección del inicio rápido le guía por todos los pasos recomendados para habilitar Microsoft Defender for Cloud y las características de seguridad mejorada. Cuando haya completado todos los pasos de inicio rápido:

> [!div class="checklist"]
> * Defender for Cloud estará habilitado en las suscripciones de Azure
> * Las [características de seguridad mejorada](enhanced-security-features-overview.md) estarán habilitados en las suscripciones de Azure
> * La recopilación automática de datos estará configurada
> * [Las notificaciones por correo electrónico](configure-email-notifications.md) de alertas de seguridad estarán configuradas
> * Las máquinas híbridas y de varias nubes conectadas a Azure

## <a name="prerequisites"></a>Prerrequisitos
Para empezar a trabajar con Defender for Cloud, necesita una suscripción a Microsoft Azure. Si no tiene ninguna suscripción, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para habilitar las características de seguridad mejorada en una suscripción, debe tener asignados los roles Propietario de la suscripción, Colaborador de la suscripción o Administrador de seguridad.

## <a name="enable-defender-for-cloud-on-your-azure-subscription"></a>Habilitación de Defender for Cloud en una suscripción de Azure

> [!TIP]
> Para habilitar Defender for Cloud en todas las suscripciones de un grupo de administración, consulte el artículo sobre la [habilitación de Defender for Cloud en varias suscripciones de Azure](onboard-management-group.md).

1. Inicie sesión en [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. En el menú del portal, seleccione **Defender for Cloud**. 

    Se abre la página de información general de Defender for Cloud

    :::image type="content" source="./media/get-started/overview.png" alt-text="Panel de información general de Defender for Cloud" lightbox="./media/get-started/overview.png":::

    **Defender for Cloud - Información general** proporciona una vista unificada de la situación de seguridad de las cargas de trabajo de la nube híbrida, lo que sirve de ayuda para detectar y evaluar la seguridad de las cargas de trabajo e identificar y mitigar riesgos. Para obtener más información, diríjase a la [página de información general de Microsoft Defender for Cloud](overview-page.md).

    Defender for Cloud habilita de forma automática y sin costo alguno cualquiera de las suscripciones de Azure que no fueran incorporadas previamente por usted o por otro usuario de la suscripción.

Puede ver la lista de suscripciones y filtrarla, si hace clic en el elemento de menú **Suscripciones**. Defender for Cloud ajustará la pantalla para que refleje la posición de seguridad de las suscripciones seleccionadas. 

A los pocos minutos de iniciar Defender for Cloud la primera vez, es posible que vea lo siguiente:

- **Recomendaciones** de formas de mejorar la seguridad de los recursos de Azure.
- Un inventario de los recursos que Defender for Cloud está evaluando, junto con la postura de seguridad de cada uno.

Para sacar el máximo partido de Defender for Cloud, continúe con los pasos siguientes de la sección de inicio rápido.



## <a name="next-steps"></a>Pasos siguientes
En este inicio rápido ha habilitado Defender for Cloud. El siguiente paso consiste en habilitar las características de seguridad mejorada para la administración de seguridad unificada y protección contra amenazas en las cargas de trabajo de la nube híbrida.

> [!div class="nextstepaction"]
> [Inicio rápido: Habilitación de las características de seguridad mejorada](enable-enhanced-security.md)
