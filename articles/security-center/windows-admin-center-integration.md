---
title: Protección de servidores Windows Admin Center con Microsoft Defender for Cloud
description: En este artículo se explica cómo integrar Microsoft Defender for Cloud con Windows Admin Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c1c5af62c723d207b9eb5bdfc3c38fa57e9b4e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055946"
---
# <a name="protect-windows-admin-center-resources-with-microsoft-defender-for-cloud"></a>Protección de recursos de Windows Admin Center con Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Windows Admin Center es una herramienta de administración para los servidores de Windows. Es una ubicación única para que los administradores del sistema obtengan acceso a la mayoría de las herramientas de administración que se usan con más frecuencia. Desde Windows Admin Center, puede incorporar directamente sus servidores locales en Microsoft Defender for Cloud. Asimismo, puede ver un resumen de las recomendaciones y alertas de seguridad directamente en Windows Admin Center.

> [!NOTE]
> La suscripción de Azure y el área de trabajo de Log Analytics asociada deben tener habilitado las características de seguridad mejoradas de Microsoft Defender for Cloud para poder habilitar la integración de Windows Admin Center.
Las características de seguridad mejoradas son gratuitas durante los primeros 30 días si no las ha usado previamente en la suscripción y el área de trabajo. Para obtener información sobre los precios en la moneda de su elección y según su región, consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).
>

Cuando haya incorporado correctamente un servidor de Windows Admin Center a Microsoft Defender for Cloud, puede hacer lo siguiente:

* Ver alertas y recomendaciones de seguridad en la extensión de Security Center de Windows Admin Center.
* Ver la postura de seguridad y recuperar información detallada adicional de los servidores administrados de Windows Admin Center en Defender for Cloud, mediante Azure Portal (o a través de una API).

Al combinar estas dos herramientas, Defender for Cloud se convierte en el único panel transparente que es útil para ver toda la información de seguridad, sea cual sea el objetivo: proteger los servidores locales administrados en Windows Admin Center, las VM y cualquier carga de trabajo de PaaS adicional.

## <a name="onboard-windows-admin-center-managed-servers-into-defender-for-cloud"></a>Incorporación de los servidores administrados de Windows Admin Center en Defender Cloud

1. En Windows Admin Center, seleccione uno de los servidores y, en el panel de **herramientas**, seleccione la extensión de Azure Security Center:

    ![Extensión de Azure Security Center en Windows Admin Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Si el servidor ya se ha incorporado a Defender for Cloud, no aparecerá la ventana de configuración.

1. Haga clic en **Sign in to Azure and set up** (Iniciar sesión en Azure y configurar).
    ![Incorporación de la extensión de Windows Admin Center a Defender for Cloud.](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Siga las instrucciones para conectar el servidor a Defender for Cloud. Una vez que haya escrito los detalles necesarios y los haya confirmado, Defender for Cloud realiza los cambios de configuración necesarios para asegurarse de que se cumplen todas las condiciones siguientes:
    * Se registra una puerta de enlace de Azure.
    * El servidor tiene un área de trabajo que notificar y una suscripción asociada.
    * La solución Log Analytics de Defender for Cloud está habilitada en el área de trabajo. Esta solución proporciona características de Azure Defender para *todos* los servidores y las máquinas virtuales que informan a esta área de trabajo.
    * Microsoft Defender para servidores está habilitado en la suscripción.
    * El agente de Log Analytics se instala en el servidor y se configura para que informe al área de trabajo seleccionada. Si el servidor ya informa a otra área de trabajo, se configura para que informe al área de trabajo que se acaba de seleccionar también.

    > [!NOTE]
    > Es posible que las recomendaciones tarden un rato en aparecer después de incorporarlas. De hecho, en función de la actividad del servidor, es posible que no reciba *ninguna* alerta. Para generar alertas de prueba y así comprobar que las alertas funcionan correctamente, siga las instrucciones descritas en el [procedimiento de validación de alertas](alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Visualización de las recomendaciones y alertas de seguridad en Windows Admin Center

Una vez incorporada, puede ver las alertas y las recomendaciones directamente en el área de Azure Security Center que está en Windows Admin Center. Haga clic en una recomendación o una alerta para verla en Azure Portal. Una vez allí, obtendrá información adicional y aprenderá a solucionar los problemas que tenga.

[![Recomendaciones y alertas de Defender for Cloud, tal como se ven en Windows Admin Center.](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-defender-for-cloud"></a>Visualización de las recomendaciones y alertas de seguridad para servidores administrados de Windows Admin Center en Defender for Cloud
De Microsoft Defender for Cloud:

* Para ver recomendaciones de seguridad para todos los servidores de Windows Admin Center, abra el [inventario de recursos](asset-inventory.md) y filtre por el tipo de máquina que quiere investigar. Seleccione la pestaña **Máquinas virtuales y equipos**.

* Para ver las alertas de seguridad de todos los servidores de Windows Admin Center, abra la opción de **alertas de seguridad**. Haga clic en **Filtrar** y asegúrese de que **solo** esté seleccionada la opción "No es de Azure":

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filtrar las alertas de seguridad de los servidores administrados de Windows Admin Center." lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::
