---
title: 'Solución de problemas: la VPN de sitio a sitio de Azure se desconecta intermitentemente'
description: Obtenga información acerca de cómo solucionar el problema en el que la conexión de la VPN de sitio a sitio se desconecta regularmente.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: 47a6649c154ed3fed5ba444a58bae93ac0398bbc
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676841"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Solución de problemas: la VPN de sitio a sitio de Azure se desconecta intermitentemente

Puede experimentar un problema en el que una conexión VPN de sitio a sitio de Microsoft Azure no sea estable y se desconecte regularmente. Este artículo proporciona pasos de solución de problemas para ayudarlo a identificar y resolver la causa del problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

### <a name="prerequisite-step"></a>Paso de requisito previo

Comprobación del tipo de puerta de enlace de red virtual de Azure:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Compruebe la página **Introducción** de la puerta de enlace de red virtual para el tipo de información.
    
    ![La información general de la puerta de enlace](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Paso 1: Comprobación de si el dispositivo VPN local está validado

1. Compruebe si está usando una [versión del sistema operativo y dispositivo VPN validada](vpn-gateway-about-vpn-devices.md#devicetable). Si el dispositivo VPN no está validado, tendrá que ponerse en contacto con el fabricante del dispositivo para ver si hay algún problema de compatibilidad.
2. Asegúrese de que el dispositivo VPN esté configurado correctamente. Para obtener más información, vea [Edición de ejemplos de configuración de dispositivos](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Paso 2: Comprobación de la configuración de la asociación de seguridad (para puertas de enlace de red virtual de Azure basadas en directivas)

1. Asegúrese de que la red virtual, las subredes y los intervalos en la definición de **Puerta de enlace de red local** en Microsoft Azure sean los mismos que la configuración en el dispositivo VPN local.
2. Compruebe que la configuración de la asociación de seguridad coincida.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Paso 3: Comprobación de los grupos de seguridad de red o las rutas definidas por el usuario en la subred de puerta de enlace

Una ruta definida por el usuario en la subred de puerta de enlace puede restringir un tipo de tráfico y permitir otro. Esto hace que parezca que la conexión VPN no es de confianza para un tipo de tráfico y que es buena para otro. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Paso 4: Comprobación de la configuración "un túnel de VPN por par de subred" (para puertas de enlace de red virtual basadas en directivas)

Asegúrese de que el dispositivo VPN local se haya establecido para tener **un túnel VPN por par de subred** para las puertas de enlace de red virtual basadas en directivas.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Paso 5: Comprobación de la limitación de la asociación de seguridad (para puertas de enlace de red virtual basadas en directivas)

La puerta de enlace de red virtual basada en directivas tiene un límite de 200 pares de la asociación de seguridad de subred. Si el número de subredes de red virtual de Azure multiplicado por el número de subredes locales es superior a 200, observará una desconexión esporádica de las subredes.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Paso 6: Comprobación de la dirección de la interfaz externa del dispositivo VPN local

Si la dirección IP accesible desde Internet del dispositivo VPN está incluida en la definición de **Espacio de direcciones de la puerta de enlace de red local** en Azure, es posible que sufra desconexiones esporádicas.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Paso 7: Comprobación de si el dispositivo VPN local cuenta con la característica Confidencialidad directa perfecta habilitada

La característica **Confidencialidad directa perfecta** puede provocar problemas de desconexión. Si el dispositivo VPN cuenta con la característica **Confidencialidad directa perfecta habilitada**, deshabilítela. A continuación, [actualice la directiva IPsec de puerta de enlace de red virtual](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de una conexión de sitio a sitio en una red virtual](./tutorial-site-to-site-portal.md)
- [Configuración de la directiva IPsec/IKE para conexiones VPN de sitio a sitio](vpn-gateway-ipsecikepolicy-rm-powershell.md)
