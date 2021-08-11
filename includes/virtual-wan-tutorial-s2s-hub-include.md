---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: aacd6a780562476ea338b4c08795e14b6ba39760
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579202"
---
1. Busque la red de WAN virtual que ha creado. En la página de la red WAN virtual, en la sección **Conectividad**, seleccione **Centros de conectividad**.
1. En la página **Centros de conectividad**, seleccione **+ Nuevo centro de conectividad** para abrir la página **Crear centro de conectividad virtual**.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/basics.png" alt-text="Captura de pantalla que muestra el panel Crear centro de conectividad virtual con la pestaña Aspectos básicos seleccionada." border="false":::
1. En la página **Crear centro de conectividad virtual**, en la pestaña **Aspectos básicos** rellene los siguientes campos:

   * **Región** (anteriormente se conocía como "ubicación")
   * **Nombre**
   * **Espacio de direcciones privadas del centro de conectividad**: el espacio de direcciones mínimo para crear un centro de conectividad es /24. Si usa cualquier valor comprendido entre /25 y /32, se producirá un error durante el proceso de creación. No es necesario planear explícitamente el espacio de direcciones de subred para los servicios del centro de conectividad virtual. Como Azure Virtual WAN es un servicio administrado, crea las subredes adecuadas en el centro de conectividad virtual para las diferentes puertas de enlace y servicios (por ejemplo, puertas de enlace de VPN, puertas de enlace de ExpressRoute, puertas de enlace de VPN de punto a sitio del usuario, firewall, enrutamiento, etc.).
