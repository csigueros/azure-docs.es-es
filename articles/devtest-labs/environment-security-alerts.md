---
title: Alertas de seguridad para entornos
description: En este artículo se muestra cómo ver las alertas de seguridad de un entorno en DevTest Labs y tomar las medidas adecuadas.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 57f1a0b240035d75746b9d23482446be71d1b1f9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398048"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertas de seguridad de entornos en Azure DevTest Labs
Como usuario de laboratorio, puede ver las alertas de Microsoft Defender for Cloud para sus laboratorios. Defender for Cloud recopila, analiza e integra automáticamente los datos de registro. Los datos proceden de recursos de Azure, la red y soluciones conectadas, como el firewall y la protección de punto de conexión. Defender for Cloud detecta amenazas reales y reduce los falsos positivos. Defender for Cloud:

- Enumera las alertas de seguridad prioritarias.
- Proporciona información para investigar rápidamente los problemas.
- Recomienda cómo corregir los ataques.

[Obtenga más información sobre las alertas de seguridad en Microsoft Defender for Cloud](../security-center//security-center-alerts-overview.md).


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
