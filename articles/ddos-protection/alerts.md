---
title: Visualización y configuración de alertas de DDoS Protection para Azure DDoS Protection Standard
description: Aprenda a visualizar y configurar alertas de DDoS Protection para Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 1ea079802a2628b8a698e108f6f689ff5e58f08b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750293"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Visualización y configuración de alertas de protección contra DDoS

La versión estándar de Azure DDoS Protection proporciona información detallada y visualización de ataques con DDoS Attack Analytics. Los clientes que protegen sus redes virtuales frente a ataques DDoS disponen de visibilidad detallada sobre el tráfico de ataques y las acciones llevadas a cabo para mitigarlos mediante informes de mitigación de ataques y registros de flujo de mitigación. La telemetría enriquecida se expone a través de Azure Monitor, incluidas las métricas detalladas mientras dure un ataque DDoS. Las alertas se pueden configurar para cualquiera de las métricas de Azure Monitor que expone DDoS Protection. El registro se puede integrar con [Azure Sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection), Splunk (Azure Event Hubs), OMS Log Analytics y Azure Storage para realizar análisis avanzados con la interfaz de Azure Monitor Diagnostics.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar alertas mediante Azure Monitor
> * Configurar alertas mediante el portal
> * Ver alertas en Azure Security Center
> * Validar y probar las alertas

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- Antes realizar los pasos de este tutorial, primero debe crear un [plan de protección de Azure DDoS Standard](manage-ddos-protection.md) y DDoS Protection Standard debe estar habilitado en una red virtual.
- DDoS supervisa las direcciones IP públicas asignadas a los recursos dentro de una red virtual. Si no tiene ningún recurso con direcciones IP públicas en la red virtual, primero debe crear un recurso con una dirección IP pública. Puede supervisar la dirección IP pública de todos los recursos implementados a través de Resource Manager (no clásico) que aparecen en [Red virtual para servicios de Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (incluidas las instancias de Azure Load Balancer donde las máquinas virtuales de back-end se encuentran en la red virtual), excepto para entornos de Azure App Service. Para continuar con este tutorial, puede crear rápidamente una máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).     

## <a name="configure-alerts-through-azure-monitor"></a>Configurar alertas mediante Azure Monitor

Con estas plantillas, podrá configurar alertas para todas las direcciones IP públicas en las que haya habilitado el registro de diagnóstico. Por lo tanto, para poder usar estas plantillas de alerta, primero necesitará un área de trabajo de Log Analytics con la configuración de diagnóstico habilitada. Consulte [Visualización y configuración del registro de diagnósticos de DDoS](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Regla de alerta de Azure Monitor

Esta [regla de alertas de Azure Monitor](https://aka.ms/DDOSmitigationstatus) ejecutará una consulta simple para detectar cuándo se está produciendo una mitigación de DDoS activa. Esto es indicativo de un posible ataque. Los grupos de acciones se pueden usar para invocar acciones como resultado de la alerta.

[![Implementación en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Regla de alerta de Azure Monitor con la aplicación lógica

En esta [plantilla](https://aka.ms/ddosalert) se implementan los componentes necesarios de una alerta de mitigación de DDoS enriquecida. Regla de alerta, grupo de acciones y aplicación lógica de Azure Monitor. El resultado del proceso es una alerta por correo electrónico con detalles sobre la dirección IP víctima del ataque, incluida información sobre el recurso asociado a la dirección IP. El propietario del recurso se agrega como destinatario del correo electrónico, junto con el equipo de seguridad. También se realiza una prueba de disponibilidad de la aplicación básica y los resultados se incluyen en la alerta por correo electrónico.

[![Implementación en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Configurar alertas mediante el portal

Puede seleccionar cualquiera de las métricas de protección contra DDoS disponibles para que le avisen cuando hay una mitigación activa durante un ataque mediante la configuración de alertas de Azure Monitor. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya al plan de DDoS Protection.
2. En **Supervisión**, seleccione **Métricas**.
3. En la barra de navegación gris, seleccione **Nueva regla de alertas**. 
4. Escriba o seleccione sus propios valores, o bien escriba los valores de ejemplo siguientes, acepte el resto de los valores predeterminados y, luego, seleccione **Crear regla de alertas**:

    |Configuración                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    | Ámbito                   | Seleccione **Seleccionar recurso**. </br> Seleccione la **Suscripción** que contiene la dirección IP pública que desea registrar, seleccione **Dirección IP pública** para **Tipo de recurso** y, a continuación, seleccione la dirección IP pública específica para la que desea registrar métricas. </br> Seleccione **Listo**. | 
    | Condición | Seleccione **Select Condition** (Seleccionar condición). </br> En nombre de señal, seleccione **Bajo ataque DDoS o no**. </br> En **Operador**, seleccione **Es mayor o igual que**. </br> En **Tipo de agregación**, seleccione **Máximo**. </br> En **Valor del umbral**, escriba *1*. En la métrica **Bajo ataque DDoS o no**, **0** significa que no se le está atacando, mientras que **1** significa que se le está atacando. </br> Seleccione **Listo**. | 
    | Actions | Seleccione **Add actions groups** (Agregar grupos de acciones). </br> Seleccione **Crear grupo de acciones**. </br> En **Notificaciones**, en **Tipo de notificación**, seleccione **Email/SMS message/Push/Voice** (Correo electrónico/mensaje SMS/notificación push/ mensaje de voz). </br> Under **Name**, enter _MyUnderAttackEmailAlert_. </br> Haga clic en el botón Editar y, a continuación, seleccione **Correo electrónico** y tantas opciones como necesite de las siguientes y, a continuación, seleccione **Aceptar**. </br> Seleccione **Revisar + crear**. | 
    | Detalles de la regla de alertas | En **Nombre de regla de alerta**, escriba _MyDdosAlert_. |

Dentro de unos pocos minutos después de la detección del ataque, debería recibir un correo electrónico con métricas de Azure Monitor que tiene un aspecto similar a la imagen siguiente:

![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)

Puede aprender más sobre [configurar webhooks](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [aplicaciones lógicas](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para la creación de alertas.

## <a name="view-alerts-in-azure-security-center"></a>Ver alertas en Azure Security Center

Azure Security Center proporciona una lista de [alertas de seguridad](../security-center/security-center-managing-and-responding-alerts.md), que cuentan con información para ayudarle a investigar y solucionar problemas. Gracias a esta característica, obtendrá una vista unificada de las alertas, incluyendo las alertas relacionadas con los ataques DDoS y las acciones que se tomaron para mitigar el ataque a corto plazo.
Hay dos alertas específicas que verá en cualquier mitigación y detección de ataques DDoS:

- **Ataque DDoS detectado para IP pública**: Esta alerta se genera cuando el servicio de protección contra DDoS detecta que una de sus direcciones IP públicas es el objetivo de un ataque DDoS.
- **Ataque DDoS mitigado para IP pública**: Esta alerta se genera cuando se ha mitigado un ataque a la dirección IP pública.
Para ver las alertas, abra el **Centro de seguridad** en Azure Portal. En **Protección contra amenazas**, seleccione **Alertas de seguridad**. La siguiente captura de pantalla muestra un ejemplo de las alertas de ataques DDoS.

![Alerta de ataque DDoS en Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Las alertas incluyen información general sobre la dirección IP pública que está bajo ataque, información de inteligencia geográfica y de amenazas y los pasos para solucionar el problema.

## <a name="validate-and-test"></a>Validación y prueba

Para simular un ataque de DDoS para validar las alertas, consulte [Validación de la detección de DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

- Configurar alertas mediante Azure Monitor
- Configurar alertas mediante el portal
- Ver alertas en Azure Security Center
- Validar y probar las alertas

Para obtener información sobre cómo probar y simular un ataque DDoS, consulte la guía de pruebas de simulación:

> [!div class="nextstepaction"]
> [Pruebas mediante simulaciones](test-through-simulations.md)