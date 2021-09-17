---
ms.author: cherylmc
author: cherylmc
ms.date: 07/29/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: d13072a03dce5aa3c7fd84ccf458b4f4fad112f1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778546"
---
1. En la página de su **red WAN virtual**, seleccione **Configuraciones de VPN de usuario**.
1. En la página **Configuraciones de VPN de usuario**, seleccione una configuración y, luego, elija **Descargar perfil de VPN de usuario de WAN virtual**.

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/download.png" alt-text="Captura de pantalla de descarga de perfil de VPN de usuario de WAN virtual.":::

   * Al descargar la configuración del nivel de WAN, se obtiene un perfil de VPN de usuario basado en Traffic Manager. 
   
   * Para más información sobre los perfiles globales o sobre perfiles basados en centros de conectividad, consulte el artículo sobre [perfiles de centros de conectividad](../articles/virtual-wan/global-hub-profile.md). Los escenarios de conmutación por error se simplifican con los perfiles globales.

   * Si, por alguna razón, alguno de los centros de conectividad no está disponible, la administración de tráfico integrada que proporciona el servicio garantiza la conectividad (a través de otro centro) a los recursos de Azure para los usuarios de punto a sitio. Siempre puede descargar una configuración de VPN específica del centro de conectividad. Para ello, vaya al centro de conectividad. En **VPN de usuario (punto a sitio)** , descargue el perfil de **VPN de usuario** del centro de conectividad virtual.
1. En la página **Descargar perfil de VPN de usuario de WAN virtual**, seleccione la opción que quiera en **Tipo de autenticación** y, luego, haga clic en **Generar y descargar perfil**. 

   Se genera un paquete de perfil (archivo zip) que contiene las opciones de configuración del cliente y que se descarga en el equipo.

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/generate.png" alt-text="Captura de pantalla de generación y descarga de perfil.":::

