---
title: Implementaciones de configuración en Azure Virtual Network Manager (versión preliminar)
description: Obtenga información sobre cómo funcionan las implementaciones de configuración en Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: f7e192133bac66d9ec593350a43d0d005bb05933
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866684"
---
# <a name="configuration-deployments-in-azure-virtual-network-manager-preview"></a>Implementaciones de configuración en Azure Virtual Network Manager (versión preliminar)

En este artículo, obtendrá información sobre cómo se aplican las configuraciones a los recursos de red. También explorará cómo la actualización de una implementación de configuración es diferente para cada tipo de pertenencia. Después, describiremos en detalle el *estado de implementación* y el *modelo de estado objetivo*.

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deployment"></a>Implementación

La *implementación* es el método que usa Azure Virtual Network Manager para aplicar configuraciones a las redes virtuales en grupos de red. Los cambios realizados en los grupos de red, la conectividad y la configuración del administrador de seguridad no surtirán efecto a menos que se haya confirmado una implementación. Al confirmar una implementación, se seleccionan las regiones a las que se aplicará la configuración. Cuando se envíe una solicitud de implementación a Azure Virtual Network Manager, calculará el [estado objetivo](#goalstate) de los recursos de red y solicitará los cambios necesarios en la infraestructura. Los cambios pueden tardar entre 15 y 20 minutos en función del tamaño de la configuración.

## <a name="deployment-against-network-group-membership-types"></a><a name="deployment"></a>Implementación en los tipos de pertenencia a grupos de red

Cambiar la definición de un grupo de red no tendrá ningún impacto a menos que se implemente la configuración que usa este grupo de red. Por tanto, las actualizaciones de implementación son diferentes para los miembros del grupo estáticos y dinámicos de un grupo de red. Cuando tenga la pertenencia dinámica a grupos definida, como todas las redes virtuales cuyo nombre contiene "producción", Azure Virtual Network Manager determinará automáticamente si los miembros dinámicos cumplen los requisitos de la configuración y se ajustarán sin necesidad de volver a implementar la configuración. Esto se debe a que ya definió la condición de la pertenencia, y la definición no cambió. Sin embargo, si tiene redes virtuales que se agregan como miembros estáticos, deberá implementar de nuevo la configuración para que se apliquen los cambios. Por ejemplo, si agrega una nueva red virtual como miembro estático, deberá implementar la configuración de nuevo para que surta efecto.

## <a name="deployment-status"></a>Estado de implementación

Al confirmar una implementación de configuración, la API realiza una operación POST y no verá la finalización de la implementación posteriormente. Una vez realizada la solicitud de implementación, Azure Virtual Network Manager calculará el estado objetivo de las redes y solicitará a la infraestructura subyacente que realice los cambios. Puede ver el estado de la implementación en la página  *Implementación* de Virtual Network Manager.

:::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="Captura de pantalla de una implementación en curso en la lista de implementación.":::

## <a name="goal-state-model"></a><a name = "goalstate"></a> Modelo de estado objetivo

Al confirmar una implementación de configuraciones, describe el estado objetivo de la configuración que desea como resultado final. Por ejemplo, al confirmar unas configuraciones denominadas *Config1* y *Config2* en una región, se aplican estas dos configuraciones. Si decidiese confirmar unas configuraciones denominadas *Config1* y *Config3* en la misma región, se quitaría *Config2* y se agregaría *Config3*. Para quitar todas las configuraciones, implementaría una configuración **None** (Ninguna) en las regiones en las que ya no desea aplicar ninguna configuración.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [crear una instancia de Azure Virtual Network Manager](create-virtual-network-manager-portal.md).
