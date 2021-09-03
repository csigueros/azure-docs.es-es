---
title: Configuración de la protección contra bots para el Firewall de aplicaciones web (WAF)
description: Más información sobre cómo configurar la protección contra bots para el Firewall de aplicaciones web (WAF) en Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: 12c9466a4bee6adb77a0e46e63767c2b22fcc01d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729425"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway"></a>Configuración de la protección contra bots para Web Application Firewall en Azure Application Gateway

En este artículo se muestra cómo configurar una regla de protección contra bots en el Firewall de aplicaciones web (WAF) para Application Gateway Azure Portal. 

Puede habilitar un conjunto de reglas de protección contra bots administrado para que WAF bloquee o registre las solicitudes de direcciones IP malintencionadas conocidas. Las direcciones IP proceden de la fuente Inteligencia sobre amenazas de Microsoft. Intelligent Security Graph impulsa la inteligencia sobre amenazas de Microsoft y lo utilizan numerosos servicios, incluido Azure Security Center.

## <a name="prerequisites"></a>Requisitos previos

Cree una directiva de WAF básica para Application Gateway siguiendo las instrucciones descritas en [Crear directivas de Firewall de aplicaciones web para Application Gateway](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Habilitación del conjunto de reglas de protección contra bots

1. En la página de la Directiva **básica** que creó anteriormente, en de **Configuración**, seleccione **Reglas**.  

2. En la página detalles, en la sección  **Administrar reglas** , en el menú desplegable, active la casilla de la regla de protección contra bots y luego seleccione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Protección contra bots](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las reglas personalizadas, consulte [Reglas personalizadas para el Firewall de aplicaciones web V2 en Azure Application Gateway](custom-waf-rules-overview.md).