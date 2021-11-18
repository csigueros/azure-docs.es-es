---
title: Alertas de seguridad para entornos
description: En este artículo se muestra cómo ver las alertas de seguridad de un entorno en DevTest Labs y tomar las medidas adecuadas.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0dccf45f32d6eaf272a464198edd0c5043dbc364
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325170"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertas de seguridad de entornos en Azure DevTest Labs
Como usuario de laboratorio, ahora puede ver las alertas de Microsoft Defender for Cloud para sus entornos de laboratorio. Defender for Cloud recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar amenazas reales y reducir los falsos positivos. En Defender for Cloud, se muestra una lista de alertas de seguridad prioritarias, junto con la información que necesita para investigar rápidamente el problema y recomendaciones para corregir un ataque. [Obtenga más información sobre las alertas de seguridad en Microsoft Defender for Cloud](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Prerequisites
Actualmente, solo puede ver alertas de seguridad de los entornos de plataforma como servicio (PaaS) implementados en el laboratorio. Para probar o usar esta característica, [implemente un entorno en el laboratorio](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Visualización de alertas de seguridad de un entorno

1. En la página principal del laboratorio, seleccione **Security alerts** (Alertas de seguridad) en el menú de la izquierda. Verá el número de alertas de seguridad (alta, media y baja). Obtenga más información sobre [cómo se clasifican las alertas](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Alertas de seguridad: introducción](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Haga clic con el botón derecho en los tres puntos (...) de la última columna y seleccione **View security alerts** (Ver alertas de seguridad). 

    ![Captura de pantalla que muestra la página Alertas de seguridad con la opción "Ver las alertas de seguridad" seleccionada.](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Puede ver más detalles sobre las alertas y las recomendaciones de Advisor. Obtenga más información sobre cómo [administrar y responder a alertas de seguridad en Microsoft Defender for Cloud](../security-center/security-center-managing-and-responding-alerts.md).

    ![Ver alertas de seguridad](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los entornos, consulte los siguientes artículos:

- [Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configuración y uso de entornos públicos](devtest-lab-configure-use-public-environments.md)
