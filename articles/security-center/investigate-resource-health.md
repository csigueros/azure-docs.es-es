---
title: 'Tutorial: Investigación del estado de los recursos con Microsoft Defender for Cloud'
description: 'Tutorial: Aprenda a investigar el estado de los recursos con Microsoft Defender for Cloud.'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f156b8f1102e55ca56085aec7b63a1fdb2716450
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075829"
---
# <a name="tutorial-investigate-the-health-of-your-resources"></a>Tutorial: Investigación del estado de los recursos

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!NOTE]
> La página de estado de los recursos que se describe en este tutorial es una versión preliminar.
> 
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] | |Precios:|**Microsoft Defender para DNS** se factura como se muestra en [la página de precios](https://azure.microsoft.com/pricing/details/security-center/).

La página de estado de los recursos proporciona una vista de instantánea del estado general de un único recurso. Puede revisar información detallada sobre el recurso y todas las recomendaciones que se aplican a ese recurso. Además, si usa alguno de [los planes de protección avanzados de Microsoft Defender](defender-for-cloud-introduction.md), también puede ver alertas de seguridad pendientes para ese recurso específico.

Esta única página, actualmente en versión preliminar, en las páginas del portal de Defender for Cloud muestra:

1. **Información sobre los recursos**: el grupo de recursos y la suscripción a la que está asociado, la ubicación geográfica, etc.
1. **Característica de seguridad aplicada**: muestra si un plan de Microsoft Defender está habilitado para el recurso o no.
1. **Recuentos de recomendaciones y alertas pendientes**: el número de recomendaciones de seguridad y alertas pendientes de Defender for Cloud.
1. **Recomendaciones y alertas prácticas:** en dos pestañas se incluyen las recomendaciones y alertas que son aplicables al recurso.

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="Página de estado de los recursos de Defender for Cloud que muestra la información de estado de una máquina virtual":::

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Acceder a la página de estado de los recursos de todos los tipos de recursos.
> * Evaluar los problemas de seguridad pendientes de un recurso.
> * Mejorar la posición de seguridad del recurso.

## <a name="prerequisites"></a>Prerrequisitos

Para recorrer las características descritas en este tutorial:

- Una suscripción a Azure: si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
- Para aplicar recomendaciones de seguridad, debe haber iniciado sesión con una cuenta que tenga los permisos pertinentes (Colaborador del grupo de recursos, Propietario del grupo de recursos, Colaborador de la suscripción o Propietario de la suscripción).
- Para descartar las alertas, debe haber iniciado sesión con una cuenta que tenga los permisos pertinentes (Administrador de seguridad, Colaborador de la suscripción o Propietario de la suscripción).

##  <a name="access-the-health-information-for-a-resource"></a>Acceso a la información sobre el estado de un recurso

> [!TIP]
> En las capturas de pantalla siguientes, vamos a mostrar una máquina virtual, pero la página de estado de los recursos puede mostrar los detalles de todos los tipos de recursos. 

Para abrir la página de estado de un recurso:

1. Seleccione cualquier recurso de la [página de inventario de recursos](asset-inventory.md).

    :::image type="content" source="media/investigate-resource-health/inventory-select-resource.png" alt-text="Seleccione un recurso del inventario para ver la página de estado de los recursos." lightbox="./media/investigate-resource-health/inventory-select-resource.png":::

1. Use el panel izquierdo de la página de estado de los recursos para obtener información general sobre la suscripción, el estado y la supervisión del recurso. También puede ver si las características de seguridad mejoradas están habilitadas para el recurso:

    :::image type="content" source="media/investigate-resource-health/resource-health-left-pane.png" alt-text="El panel izquierdo de la página de estado de los recursos de Microsoft Defender for Cloud muestra información sobre la suscripción, el estado y la supervisión del recurso. También incluye el número total de recomendaciones de seguridad y alertas pendientes.":::

1. Use las dos pestañas del panel derecho para revisar las listas de recomendaciones de seguridad y alertas que son aplicables a este recurso:

    :::image type="content" source="media/investigate-resource-health/resource-health-right-pane.png" alt-text="El panel derecho de la página de estado de los recursos de Microsoft Defender for Cloud tiene dos pestañas: una para recomendaciones y otra de alertas." lightbox="./media/investigate-resource-health/resource-health-right-pane.png":::

    > [!NOTE]
    > Microsoft Defender for Cloud usa los términos "correcto" e "incorrecto" para describir el estado de seguridad de un recurso. Estos términos están relacionados con si el recurso es compatible con una [recomendación de seguridad](security-policy-concept.md#what-is-a-security-recommendation) específica.
    >
    > En la captura de pantalla anterior, puede ver que las recomendaciones se enumeran incluso si este recurso tiene el estado "correcto". Una ventaja de la página de estado es que se enumeran todas las recomendaciones para que pueda obtener una perspectiva completa del estado de los recursos. 


## <a name="evaluate-the-outstanding-security-issues-for-a-resource"></a>Evaluar los problemas de seguridad pendientes de un recurso.

En la página de estado de los recursos se enumeran las recomendaciones por las que el estado del recurso es "incorrecto" y las alertas que están activas. 

- Para asegurarse de que el recurso está protegido según las directivas aplicadas a las suscripciones, corrija los problemas descritos en las recomendaciones:
    1. En el panel derecho, seleccione una recomendación.
    1. Continúe como se indica en la pantalla.

        > [!TIP]
        > Las instrucciones de corrección de problemas que se han sugerido en las recomendaciones de seguridad difieren de cada una de las recomendaciones de Defender for Cloud.
        >
        > Para decidir qué recomendaciones se resuelven en primer lugar, examine la gravedad de cada una de ellas y su [posible impacto en la puntuación de seguridad](secure-score-security-controls.md#security-controls-and-their-recommendations).

- Para investigar una alerta de seguridad:
    1. En el panel derecho, seleccione una alerta.
    1. Siga las instrucciones de [Responder a alertas de seguridad](managing-and-responding-alerts.md#respond-to-security-alerts).


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a usar la página de estado de los recursos de Defender for Cloud.

Para más información, consulte los artículos relacionados:

- [Responder a alertas de seguridad](managing-and-responding-alerts.md#respond-to-security-alerts)
- [Examen de las recomendaciones de seguridad](review-security-recommendations.md)
