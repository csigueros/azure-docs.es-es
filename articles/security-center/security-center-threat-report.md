---
title: Informe de inteligencia frente a amenazas de Azure Security Center | Microsoft Docs
description: Esta página le ayuda a usar los informes inteligentes de amenazas de Azure Security Center durante una investigación para más información sobre alertas de seguridad
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: b2a867c99e79b2ddfe9053589a1ebac9ab569490
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237028"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Informe de inteligencia frente a amenazas de Azure Security Center

En esta página se explica cómo los informes de inteligencia frente a amenazas de Azure Security Center pueden ayudarle a aprender más sobre una amenaza que ha desencadenado una alerta de seguridad.


## <a name="what-is-a-threat-intelligence-report"></a>¿Qué es un informe de inteligencia frente a amenazas?

La protección contra amenazas de Security Center supervisa la información de seguridad de sus recursos de Azure, la red y las soluciones de asociados relacionadas. Después, analiza estos datos (a menudo, relacionando la información de diferentes orígenes) para identificar las amenazas. Para más información, consulte [Cómo detecta y responde Azure Security Center a las amenazas](security-center-alerts-overview.md#detect-threats).

Cuando Security Center identifica una amenaza, desencadena una [alerta de seguridad](security-center-managing-and-responding-alerts.md), que contiene información detallada sobre el evento, junto con sugerencias para remediarlo. Para ayudar a los equipos de respuesta a incidentes a investigar y solucionar las amenazas, Security Center proporciona informes de inteligencia de amenazas que contienen información sobre las amenazas detectadas. El informe incluye información como la siguiente:

* Identidad o asociaciones del atacante (si esta información está disponible)
* Objetivos de los atacantes
* Campañas de ataques históricas y actuales (si esta información está disponible)
* Tácticas, herramientas y procedimientos de los atacantes
* Indicadores asociados de peligro (IoC), como direcciones URL y hash de archivo
* Victimología, que es el predominio geográfico y del sector para ayudarle a determinar si sus recursos de Azure están en peligro
* Información de corrección y mitigación

> [!NOTE]
> La cantidad de información de cualquier informe determinado variará; el nivel de detalle se basa en la actividad y el predominio del malware.

Security Center tiene tres tipos de informes de amenazas, que pueden variar según el ataque. Los informes disponibles son:

* **Informe de grupo de actividad**: proporciona información detallada sobre los atacantes, sus objetivos y las tácticas que emplean.
* **Informe de campaña**: se centra en los detalles de campañas de ataque específicas.
* **Informe de resumen de amenazas**: cubre todos los elementos de los dos informes anteriores.

Este tipo de información resulta útil durante los procesos de respuesta a incidentes, en los que hay una investigación en curso para comprender el origen del ataque, las motivaciones del atacante y lo que se debe hacer para mitigar este problema en el futuro.



## <a name="how-to-access-the-threat-intelligence-report"></a>¿Cómo acceder al informe de inteligencia frente a amenazas?

1. En la barra lateral de Security Center, abra la página **Alertas de seguridad**.
1. Seleccione una alerta. 
    Se abre la página de detalles de las alertas con más información sobre la alerta. A continuación se muestra la página de detalles de la alerta **Se detectaron indicadores de ransomware**.

    [![Página de detalles de la alerta Se detectaron indicadores de ransomware.](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Seleccione el vínculo al informe y se abrirá un archivo PDF en el explorador predeterminado.

    [![Página de detalles de la alerta Acción potencialmente no segura.](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    También puede descargar el informe en formato PDF. 

    >[!TIP]
    > La cantidad de información disponible para cada alerta de seguridad variará según el tipo de alerta.



## <a name="next-steps"></a>Pasos siguientes

En esta página se explica cómo abrir los informes de inteligencia sobre amenazas al investigar alertas de seguridad. Para obtener información relacionada, consulte las páginas siguientes:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md). Aprenda a administrar y responder a las alertas de seguridad.
* [Control de incidentes de seguridad en Azure Security Center](security-center-incident.md)