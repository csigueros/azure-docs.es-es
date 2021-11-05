---
title: Habilitación de las protecciones de cargas de trabajo integradas de Microsoft Defender for Cloud
description: Aprenda a habilitar las características de seguridad mejorada para ampliar las protecciones de Microsoft Defender for Cloud a los recursos híbridos y de varias nubes.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 06/07/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 44acbc3ace06af2e741b3daf5c6afe9a50a2efe0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030473"
---
# <a name="quickstart-enable-enhanced-security-features"></a>Inicio rápido: Habilite las características de seguridad mejorada

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Para obtener información sobre las ventajas de las características de seguridad mejorada, consulte [Características de seguridad mejorada de Microsoft Defender for Cloud](enhanced-security-features-overview.md).

## <a name="prerequisites"></a>Prerrequisitos

Para los inicios rápidos y tutoriales de Defender for Cloud, debe habilitar las características de seguridad mejorada. 

Puede proteger toda una suscripción de Azure con características de seguridad mejorada y las protecciones las heredará todos los recursos de la suscripción.

Hay disponible una versión de evaluación gratuita de 30 días. Para obtener información sobre los precios en la moneda de su elección y según su región, consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-enhanced-security-features-from-the-azure-portal"></a>Habilitación de características de seguridad mejorada desde Azure Portal

Para habilitar todas las características de protección contra amenazas de Cloud, debe habilitar las características de seguridad mejorada en la suscripción que contenga las cargas de trabajo aplicables. La habilitación en el nivel de área de trabajo no habilita el acceso a VM Just-In-Time, los controles de aplicación adaptables ni las detecciones de red para los recursos de Azure. Además, los únicos planes de Microsoft Defender disponibles en el nivel de área de trabajo son Microsoft Defender para servidores y Microsoft Defender para servidores SQL en máquinas.

- Puede habilitar **Microsoft Defender para cuentas de Storage** en el nivel de suscripción o de recurso.
- Puede habilitar **Microsoft Defender para SQL** en el nivel de suscripción o de recurso.
- Solo puede habilitar **Microsoft Defender para bases de datos relacionales de código abierto** en el nivel de recurso.

### <a name="to-enable-enhanced-security-features-on-your-subscriptions-and-workspaces"></a>Para habilitar las características de seguridad mejorada en las suscripciones y áreas de trabajo:

- Para habilitar las características de seguridad mejorada en una suscripción:

    1. En el menú principal de Defender for Cloud, seleccione **Environment settings** (Configuración del entorno).
    1. Seleccione la suscripción o el área de trabajo que desea proteger.
    1. Seleccione **Enable all Microsoft Defender for Cloud plans** (Habilitar todos los planes de Microsoft Defender for Cloud) para realizar la actualización.
    1. Seleccione **Guardar**.

    > [!TIP]
    > Observará que cada plan de Microsoft Defender tiene un precio independiente y se puede activar o desactivar de forma independiente. Por ejemplo, puede que desee desactivar Defender para App Service en suscripciones que no tengan un plan de Azure App Service asociado. 

    :::image type="content" source="./media/enhanced-security-features-overview/pricing-tier-page.png" alt-text="Página de precios de Defender for Cloud en el portal":::

- Para habilitar la seguridad mejorada en varias suscripciones o áreas de trabajo:

    1. En el menú de Defender for Cloud, seleccione **Introducción**.

        La pestaña **Actualizar** muestra las suscripciones y áreas de trabajo aptas para la incorporación.

        :::image type="content" source="./media/enable-enhanced-security/get-started-upgrade-tab.png" alt-text="Pestaña Actualizar de la página de introducción."::: 

    1. En la lista **Select subscriptions and workspaces to protect with Microsoft Defender for Cloud** (Seleccionar las suscripciones y áreas de trabajo que se protegen con Microsoft Defender), seleccione las suscripciones y áreas de trabajo que desea actualizar y seleccione **Upgrade** (Actualizar) para habilitar todas las características de Microsoft Defender for Cloud.

       - Si selecciona suscripciones y áreas de trabajo que no sean válidas para la evaluación, el paso siguiente las actualizará y se iniciarán los cargos.
       - Si selecciona un área de trabajo apta para una evaluación gratuita, el siguiente paso comenzará una evaluación gratuita.

        :::image type="content" source="./media/enable-enhanced-security/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Actualizar todas las áreas de trabajo y suscripciones seleccionadas desde la página de introducción.":::


## <a name="disable-enhanced-security-features"></a>Deshabilitación de las características de seguridad mejorada

Si necesita deshabilitar las características de seguridad mejorada de una suscripción, el procedimiento es el mismo, pero hay que seleccionar **Seguridad mejorada desactivada**:
 
1. En el menú de Defender for Cloud, seleccione **Environment settings** (Configuración del entorno).
1. Seleccione la suscripción correspondiente.
1. Seleccione **Defender plans** (Planes de Defender) y, después, **Enhanced security off**  (Seguridad mejorada desactivada).

    :::image type="content" source="./media/enable-enhanced-security/disable-plans.png" alt-text="Habilite o deshabilite las características de seguridad mejoradas de Defender for Cloud.":::

1. Seleccione **Guardar**.

> [!NOTE]
> Después de deshabilitar las características de seguridad mejorada (tanto si deshabilita un plan único como todos los planes a la vez) la recopilación de datos podrá continuar durante un breve período de tiempo. 

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha habilitado las características de seguridad mejorada, habilite los agentes y extensiones necesarios para realizar la recopilación de datos automática, como se describe en [Configuración del aprovisionamiento automático de agentes y extensiones](enable-data-collection.md).
