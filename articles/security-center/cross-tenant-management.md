---
title: Administración entre inquilinos en Microsoft Defender para Cloud | Microsoft Docs
description: Aprenda a configurar la administración entre inquilinos para administrar la posición de seguridad de varios inquilinos en Defender for Cloud mediante Azure Lighthouse.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2021
ms.author: memildin
ms.openlocfilehash: 939fabdd58ddc8d20d8e17b98fdc1626d15b295b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445262"
---
# <a name="cross-tenant-management-in-defender-for-cloud"></a>Administración entre inquilinos en Defender para la nube

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

La administración entre inquilinos le permite ver y administrar la posición de seguridad de varios inquilinos en Defender for Cloud mediante [Azure Lighthouse](../lighthouse/overview.md). Administre varios inquilinos de manera eficaz, desde una sola vista, sin que sea necesario que inicie sesión en el directorio de cada inquilino.

- Los proveedores de servicios pueden administrar la posición de seguridad de los recursos, para varios clientes, desde su propio inquilino.

- Los equipos de seguridad de las organizaciones con varios inquilinos pueden ver y administrar su posición de seguridad desde una ubicación única.

## <a name="set-up-cross-tenant-management"></a>Configuración de la administración entre inquilinos

La [administración de recursos delegados de Azure](../lighthouse/concepts/architecture.md) es uno de los componentes clave de Azure Lighthouse. Para configurar la administración entre inquilinos, delegue el acceso a los recursos de los inquilinos administrados a su propio inquilino mediante estas instrucciones de la documentación de Azure Lighthouse: [Incorporación de un cliente a Azure Lighthouse](../lighthouse/how-to/onboard-customer.md).


## <a name="how-does-cross-tenant-management-work-in-defender-for-cloud"></a>¿Cómo funciona la administración entre inquilinos en Defender for Cloud?

Puede revisar y administrar suscripciones en varios inquilinos del mismo modo que administra varias suscripciones en un mismo inquilino.

En la barra de menús superior, haga clic en el icono de filtro y seleccione las suscripciones, del directorio de cada inquilino, que quiere ver.

  ![Filtrado de inquilinos.](./media/cross-tenant-management/cross-tenant-filter.png)

Las vistas y acciones son básicamente las mismas. Estos son algunos ejemplos:

- **Administrar directivas de seguridad**: desde una vista, administre la posición de seguridad de muchos recursos con las [directivas](tutorial-security-policy.md), haga acciones con las recomendaciones de seguridad y recopile y administre los datos relacionados con la seguridad.
- **Mejorar la puntuación de seguridad y la posición de cumplimiento**: la visibilidad entre inquilinos le permite ver la posición de seguridad general de todos los inquilinos y dónde y cómo mejorar más la [puntuación segura](secure-score-security-controls.md) y la [posición de cumplimiento](regulatory-compliance-dashboard.md) de cada uno de ellos.
- **Corrección de recomendaciones**: supervise y corrija una [recomendación](review-security-recommendations.md) para varios recursos de varios inquilinos de una sola vez. Luego, puede abordar de inmediato las vulnerabilidades que presentan el riesgo más alto en todos los inquilinos.
- **Administrar alertas**: detecte [alertas](alerts-overview.md) en los distintos inquilinos. Tome medidas en los recursos que no cumplen con los [pasos de corrección](managing-and-responding-alerts.md) accionables.

- **Administrar las características de protección en la nube avanzada y mucho más**: administre los distintos servicios de protección contra amenazas, como el [acceso a VM Just-in-Time (JIT)](just-in-time-access-usage.md), la [protección de red adaptable](adaptive-network-hardening.md), los [controles de aplicaciones adaptables](adaptive-application-controls.md), etc.
 
## <a name="next-steps"></a>Pasos siguientes
En este artículo se explica cómo funciona la administración entre inquilinos en Defender for Cloud. Para saber cómo puede simplificar Azure Lighthouse la administración entre inquilinos en empresas que usan varios inquilinos de Azure AD, consulte [Azure Lighthouse en escenarios empresariales](../lighthouse/concepts/enterprise.md).