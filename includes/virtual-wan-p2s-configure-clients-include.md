---
ms.author: cherylmc
author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 06b4d9f2830bfd9599aee30c68235d3078eb32fa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734622"
---
**OpenVPN**

* [Configuración de un cliente de OpenVPN para Azure virtual WAN](../articles/virtual-wan/howto-openvpn-clients.md)
* [Autenticación de Azure AD - Windows 10](../articles/virtual-wan/openvpn-azure-ad-client.md)
* [Autenticación de Azure AD - macOS](../articles/virtual-wan/openvpn-azure-ad-client-mac.md)

**IKEv2**

1. Seleccione los archivos de configuración de cliente VPN que correspondan a la arquitectura del equipo Windows. Si la arquitectura de procesador es de 64 bits, elija el paquete del instalador "VpnClientSetupAmd64". En caso de que sea de 32 bits, elija el paquete del instalador "VpnClientSetupX86".

1. Haga doble clic en el paquete para instalarlo. Si ve una ventana emergente de SmartScreen, seleccione **Más información** y, después, **Ejecutar de todas formas**.

1. En el equipo cliente, vaya a **Configuración de red** y haga clic en **VPN**. La conexión VPN muestra el nombre de la red virtual a la que se conecta.

1. Antes de intentar conectarse, compruebe que ha instalado un certificado de cliente en el equipo cliente. Es necesario un certificado de cliente para la autenticación al usar el tipo de autenticación de certificados nativo de Azure. Para más información acerca de cómo generar certificados, consulte [Generación de certificados](../articles/virtual-wan/certificates-point-to-site.md). Para obtener información acerca de cómo instalar un certificado de cliente, consulte [Instalación de certificados de cliente](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).
