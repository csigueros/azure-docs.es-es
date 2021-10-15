---
title: Configuración de un plan de Azure DDoS Protection mediante Azure Firewall Manager
description: Obtenga información sobre cómo usar Azure Firewall Manager para configurar un plan estándar de Azure DDoS Protection
author: vhorne
ms.author: victorh
ms.service: firewall-manager
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 4ec6c08fab14c2c08ed719d616b84f1a4d744eb9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367603"
---
# <a name="configure-an-azure-ddos-protection-plan-using-azure-firewall-manager-preview"></a>Configuración de un plan de Azure DDoS Protection mediante Azure Firewall Manager (versión preliminar)

Azure Firewall Manager es una plataforma para administrar y proteger los recursos de red a escala. Puede asociar las redes virtuales a un plan de protección contra DDoS en Azure Firewall Manager.

> [!IMPORTANT]
> El uso de Azure Firewall Manager para configurar un plan de Azure DDoS Protection está actualmente en VERSIÓN PRELIMINAR.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general. 

> [!TIP]
> La versión estándar de DDoS Protection actualmente no admite redes WAN virtuales. Sin embargo, puede solucionar esta limitación si fuerza la tunelización del tráfico de Internet a una instancia de Azure Firewall en una red virtual que tenga un plan de DDoS Protection asociado.

En un solo inquilino, los planes de protección contra DDoS se pueden aplicar a redes virtuales en varias suscripciones. Para más información sobre los planes de protección contra DDoS, vea [Introducción a Protección contra DDoS de Azure estándar](../ddos-protection/ddos-protection-overview.md).

Para ver cómo funciona, creará una directiva de firewall y, a continuación, una red virtual protegida con una instancia de Azure Firewall. A continuación, creará un plan de DDoS Protection y lo asociará a la red virtual.

## <a name="create-a-firewall-policy"></a>Crear una directiva de firewall

Use Firewall Manager para crear una directiva de firewall.

1. En [Azure Portal](https://portal.azure.com), abra Firewall Manager.
1. Seleccione **Directivas de Azure Firewall**.
1. Seleccione **Crear una directiva de Azure Firewall**.
1. En **Grupo de recursos**, seleccione **DDoS-Test-rg**.
1. En **Detalles de directiva**, **Nombre**, escriba **fw-pol-01**.
1. En **Región**, seleccione **Oeste de EE. UU. 2**.
1. Seleccione **Revisar + crear**.
1. Seleccione **Crear**.


## <a name="create-a-secured-virtual-network"></a>Creación de un centro virtual protegido

Use Firewall Manager para crear una red virtual protegida.

1. Abra Firewall Manager.
1. Seleccione **Redes virtuales**.
1. Seleccione **Crear nueva red virtual protegida**.
1. En **Grupo de recursos**, seleccione **DDoS-Test-rg**.
1. En **Región**, seleccione **Oeste de EE. UU. 2**.
1. En **Nombre de la red virtual del centro**, escriba **Hub-vnet-01**.
1. En **Intervalo de direcciones**, escriba **10.0.0.0/16**.
1. Seleccione **Siguiente: Azure Firewall**.
1. En **Dirección IP pública**, seleccione **Agregar nuevo** y escriba **fw-pip** como nombre. Luego, seleccione **Aceptar**.
1. En **Espacio de direcciones de subred del firewall**, escriba **10.0.0.0/24**.
1. Seleccione **fw-pol-01** para **directiva de firewall**.
1. Seleccione **Siguiente: Review + create** (Revisar y crear).
1. Seleccione **Crear**.

## <a name="create-a-ddos-protection-plan"></a>Creación de un plan de protección contra DDoS

Cree un plan de DDoS Protection mediante Firewall Manager. Puede usar la página **Planes de DDoS Protection** para crear y administrar los planes de Azure DDoS Protection.

:::image type="content" source="media/configure-ddos/firewall-ddos.png" alt-text="Captura de pantalla de la página Planes de DDoS Protection de Firewall Manager":::.

1. Abra Firewall Manager.
1. Seleccione **Planes de DDoS Protection**.
1. Seleccione **Crear**.
1. En **Grupo de recursos**, seleccione **Crear nuevo**.
1. Escriba **DDos-Test-rg** como nombre del grupo de recursos.
1. En **Detalles de la instancia**, **Nombre**, escriba **DDoS-plan-01**.
1. En **Región**, seleccione **Oeste de EE. UU. 2**
1. Seleccione **Revisar + crear**.
1. Seleccione **Crear**.

## <a name="associate-a-ddos-protection-plan"></a>Asociación de un plan de DDoS Protection

Ahora puede asociar el plan de DDoS Protection a la red virtual protegida.

1. Abra Firewall Manager.
1. Seleccione **Redes virtuales**.
1. Active la casilla **Hub-vnet-01**.
1. Seleccione **Administrar seguridad**, **Add DDoS Protection Plan** (Agregar plan de DDoS Protection).
1. En **DDoS Protection Standard** (DDoS Protection estándar), seleccione **Habilitar**.
1. En **Plan de protección contra DDoS**, seleccione **DDoS-plan-01**.
1. Seleccione **Agregar**.
1. Cuando finalice la implementación, seleccione **Actualizar**.

Ahora debería ver que la red virtual tiene un plan de DDoS Protection asociado.

:::image type="content" source="media/configure-ddos/ddos-protection.png" alt-text="Captura de pantalla que muestra la red virtual con el plan de DDoS Protection Plan":::

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los planes de DDoS Protection, consulte:

- [Introducción a Protección contra DDoS de Azure estándar](../ddos-protection/ddos-protection-overview.md)