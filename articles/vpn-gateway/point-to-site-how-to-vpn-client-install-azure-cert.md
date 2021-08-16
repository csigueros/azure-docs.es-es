---
title: instalación de un certificado de cliente de punto a sitio
titleSuffix: Azure VPN Gateway
description: 'Obtenga información sobre cómo instalar certificados de cliente para la autenticación de certificados de conexiones de punto a sitio: Windows, Mac y Linux.'
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/03/2021
ms.author: cherylmc
ms.openlocfilehash: d4ab1009b38d250a39455a9e8a470cd7f1156793
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527122"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalación de un certificado de cliente para conexiones de punto a sitio con autenticación de certificados

Cuando se configura una puerta de enlace de VPN P2S para requerir autenticación de certificado, cada cliente debe tener un certificado de cliente instalado localmente. Puede generar un certificado de cliente a partir de un certificado raíz autofirmado o a partir de un certificado raíz que se ha generado mediante una solución de una entidad de certificación empresarial. 

Cuando se genera un certificado de cliente, el certificado normalmente se instala automáticamente en el equipo cliente desde el que se generó. Si desea conectarse a la red virtual desde otro equipo cliente, debe instalar un certificado de cliente en el equipo desde el que se va a conectar. Esto se suma a la configuración del cliente VPN en ese equipo.

Puede usar varios métodos para generar y exportar certificados autofirmados. Para más información, vea los siguientes artículos:

* [PowerShell](vpn-gateway-certificates-point-to-site.md)
* [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Los clientes VPN de Mac son compatibles únicamente con el modelo de implementación de Resource Manager. No son compatibles con el modelo de implementación clásica.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

El certificado de cliente de Linux se instala en el cliente como parte de la configuración del cliente. Vea [Client configuration - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) (Configuración de cliente: Linux) para obtener instrucciones.

## <a name="next-steps"></a>Pasos siguientes

Continúe con los pasos de configuración de punto a sitio en [Creación e instalación de los archivos de configuración de cliente de VPN](point-to-site-vpn-client-configuration-azure-cert.md).
