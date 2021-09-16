---
title: Movimiento de recursos de Azure App Service
description: Use Azure Resource Manager para trasladar recursos de App Service a un nuevo grupo de recursos o a una nueva suscripción.
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 34f86cfd3a3822b77992f5d1ae77afaf14fdc7ab
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251462"
---
# <a name="move-guidance-for-app-service-resources"></a>Orientaciones para el traslado de recursos de App Service

En este artículo se describen los pasos para trasladar recursos de App Service. Hay requisitos específicos para trasladar recursos de App Service a una nueva suscripción.

## <a name="move-across-subscriptions"></a>Traslado entre suscripciones

Al trasladar una instancia de Web App entre suscripciones, se aplican las guías siguientes:

- El movimiento de un recurso a un nuevo grupo de recursos o a una nueva suscripción es un cambio de metadatos que no debería afectar en absoluto al funcionamiento del recurso. Por ejemplo, la dirección IP de entrada de un servicio de aplicaciones no cambia al mover el servicio.
- El grupo de recursos de destino no debe tener ningún recurso de App Service. Entre los recursos de App Service se incluyen:
    - Web Apps
    - Planes de App Service
    - Certificados TLS/SSL cargados o importados
    - Entornos de App Service
- Todos los recursos de App Service del grupo de recursos se deben mover conjuntamente.
- Las instancias de App Service Environment no se pueden mover a un nuevo grupo de recursos ni suscripción. Sin embargo, puede mover una aplicación web y un plan de App Service a una nueva suscripción sin mover la instancia de App Service Environment. Después del movimiento, la aplicación web ya no se hospeda en App Service Environment.
- Puede trasladar un certificado enlazado a una web sin eliminar los enlaces TLS, siempre y cuando el certificado se mueva con el resto de recursos del grupo. Sin embargo, no puede mover un certificado gratuito administrado por App Service. Para ese escenario, consulte [Movimiento con certificados administrados gratuitos](#move-with-free-managed-certificates).
- Los recursos de App Service solo se pueden mover del grupo de recursos en el que se crearon originalmente. Si un recurso de App Service ya no está en su grupo de recursos original, muévalo a su grupo de recursos original. A continuación, mueva el recurso entre las suscripciones. Para obtener ayuda con la búsqueda del grupo de recursos original, consulte la siguiente sección.

## <a name="find-original-resource-group"></a>Búsqueda del grupo de recursos original

Si no recuerda el grupo de recursos original, se puede encontrar a través de un diagnóstico. Para la aplicación web, seleccione **Diagnosticar y solucionar problemas**. A continuación, seleccione **Configuración y administración**.

![Selección de diagnósticos](./media/app-service-move-limitations/select-diagnostics.png)

Seleccione **Opciones de migración**.

![Selección de opciones de migración](./media/app-service-move-limitations/select-migration.png)

Seleccione la opción de los pasos recomendados para trasladar la aplicación web.

![Selección de pasos recomendados](./media/app-service-move-limitations/recommended-steps.png)

Puede ver las acciones recomendadas que deben realizarse antes de trasladar los recursos. La información incluye el grupo de recursos original de la aplicación web.

![Captura de pantalla que muestra los pasos recomendados para mover recursos web de Microsoft.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-hidden-resource-types-in-portal"></a>Movimiento de tipos de recursos ocultos en el portal

Al usar el portal para mover sus recursos de App Service, es posible que aparezca un error que indica que no ha movido todos los recursos. Si aparece este error, compruebe si hay tipos de recursos que el portal no haya mostrado. Seleccione **Mostrar tipos ocultos**. A continuación, seleccione todos los recursos que desee mover.

:::image type="content" source="./media/app-service-move-limitations/show-hidden-types.png" alt-text="Visualización de los tipos ocultos":::

## <a name="move-with-free-managed-certificates"></a>Movimiento con certificados administrados gratuitos

No se puede mover un certificado gratuito administrado por App Service. En su lugar, debe eliminar el certificado administrado y volverlo a crear después de mover la aplicación web. Para obtener instrucciones sobre cómo eliminar el certificado, use la herramienta **Operaciones de migración**.

Si su certificado administrado gratuito de App Service se crea en un grupo de recursos inesperado, pruebe a mover el plan de App Service de vuelta a su grupo de recursos original. A continuación, vuelva a crear el certificado administrado gratuito. El problema se habrá solucionado.

## <a name="move-support"></a>Compatibilidad con el movimiento

Para determinar qué recursos de App Service se pueden mover, consulte el estado de compatibilidad con el traslado de:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Pasos siguientes

Para ver los comandos para trasladar recursos, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../move-resource-group-and-subscription.md).
