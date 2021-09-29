---
title: Alertas de seguridad para entornos
description: En este artículo se muestra cómo ver las alertas de seguridad de un entorno en DevTest Labs y tomar las medidas adecuadas.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 437117e29ac09e52d2cd15740d60d942170b9c0d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654202"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertas de seguridad de entornos en Azure DevTest Labs
Como usuario del laboratorio, ahora puede ver las alertas de Azure Security Center para los entornos de laboratorio. Security Center recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar amenazas reales y reducir los falsos positivos. En Security Center, se muestra una lista de alertas de seguridad prioritarias, junto con la información que necesita para investigar rápidamente y recomendaciones para corregir un ataque. [Más información sobre las alertas de seguridad en Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Prerequisites
Actualmente, solo puede ver alertas de seguridad de los entornos de plataforma como servicio (PaaS) implementados en el laboratorio. Para probar o usar esta característica, [implemente un entorno en el laboratorio](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Visualización de alertas de seguridad de un entorno

1. En la página principal del laboratorio, seleccione **Security alerts** (Alertas de seguridad) en el menú de la izquierda. Verá el número de alertas de seguridad (alta, media y baja). Obtenga más información sobre [cómo se clasifican las alertas](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Alertas de seguridad: introducción](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Haga clic con el botón derecho en los tres puntos (...) de la última columna y seleccione **View security alerts** (Ver alertas de seguridad). 

    ![Captura de pantalla que muestra la página Alertas de seguridad con la opción "Ver las alertas de seguridad" seleccionada.](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Puede ver más detalles sobre las alertas y las recomendaciones de Advisor. Para más información, consulte [Administración y respuesta a las alertas de seguridad en Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Ver alertas de seguridad](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los entornos, consulte los siguientes artículos:

- [Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configuración y uso de entornos públicos](devtest-lab-configure-use-public-environments.md)
