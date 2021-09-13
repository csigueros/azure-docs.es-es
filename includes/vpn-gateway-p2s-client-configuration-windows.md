---
title: Archivo de inclusión
description: Archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/12/2021
ms.author: cherylmc
ms.openlocfilehash: b676e4c801b447291288fdd07ff64177f1201e6c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732695"
---
Puede utilizar el mismo paquete de configuración de cliente VPN en todos los equipos cliente Windows, siempre que la versión coincida con la arquitectura del cliente. Para la lista de sistemas operativos de cliente compatibles, consulte la sección de punto a sitio de las [preguntas más frecuentes sobre la puerta de enlace de VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Debe tener derechos de administrador en el equipo cliente de Windows desde el que desea conectarse.
>

### <a name="install-the-configuration-files"></a>Instalación de los archivos de configuración

1. Seleccione los archivos de configuración de cliente VPN que correspondan a la arquitectura del equipo Windows. Si la arquitectura de procesador es de 64 bits, elija el paquete del instalador "VpnClientSetupAmd64". En caso de que sea de 32 bits, elija el paquete del instalador "VpnClientSetupX86". 
1. Haga doble clic en el paquete para instalarlo. Si ve una ventana emergente de SmartScreen, haga clic en **Más información** y en **Ejecutar de todas formas**.

### <a name="verify-and-connect"></a>Comprobación y conexión

1. Compruebe que ha instalado un certificado de cliente en el equipo cliente. Es necesario un certificado de cliente para la autenticación al usar el tipo de autenticación de certificados nativo de Azure. Para ver el certificado de cliente, abra **Administrar certificados de usuario**. El certificado de cliente está instalado en **Usuario actual\Personal\Certificates\** .
1. Para conectarse, vaya a **Configuración de red** y haga clic en **VPN**. La conexión VPN muestra el nombre de la red virtual a la que se conecta.
