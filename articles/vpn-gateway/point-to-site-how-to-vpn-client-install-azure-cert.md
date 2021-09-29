---
title: instalación de un certificado de cliente de punto a sitio
titleSuffix: Azure VPN Gateway
description: 'Obtenga información sobre cómo instalar certificados de cliente para la autenticación de certificados de conexiones de punto a sitio: Windows, Mac y Linux.'
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2021
ms.author: cherylmc
ms.openlocfilehash: 917f60440d98924e5339f29fb99587eacf40b415
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766262"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalación de un certificado de cliente para conexiones de punto a sitio con autenticación de certificados

Cuando se configura una instancia de VPN Gateway de P2S para requerir la autenticación del certificado, cada equipo debe tener un certificado de cliente instalado localmente. Este artículo le ayuda a instalar un certificado de cliente localmente en un equipo cliente. También puede usar [Intune](/mem/intune/configuration/vpn-settings-configure) para instalar determinados certificados y perfiles de cliente VPN.

Si desea generar un certificado de cliente a partir de un certificado raíz autofirmado, consulte uno de los siguientes artículos:

* [Generación de certificados: PowerShell](vpn-gateway-certificates-point-to-site.md)
* [Generación de certificados: MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [Generación de certificados: Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Los clientes VPN de Mac son compatibles únicamente con el [modelo de implementación de Resource Manager](../azure-resource-manager/management/deployment-models.md). No son compatibles con el modelo de implementación clásica.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

El certificado de cliente de Linux se instala en el cliente como parte de la configuración del cliente. Vea [Client configuration - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) (Configuración de cliente: Linux) para obtener instrucciones.

## <a name="next-steps"></a>Pasos siguientes

Continúe con los pasos de configuración de punto a sitio en [Creación e instalación de los archivos de configuración de cliente de VPN](point-to-site-vpn-client-configuration-azure-cert.md).