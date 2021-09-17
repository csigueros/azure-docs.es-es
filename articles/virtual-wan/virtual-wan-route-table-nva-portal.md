---
title: 'Virtual WAN: Creación de una tabla de rutas de concentrador virtual en NVA: Azure portal'
description: Tabla de rutas de concentrador virtual de Virtual WAN para dirigir el tráfico a un dispositivo virtual de red con el portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: e57f419daeb112be0925158109697bb2a9b399e8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779984"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Cree una tabla de rutas de concentrador de Virtual WAN para dispositivos virtuales de red: Azure portal

En este artículo se muestra cómo dirigir el tráfico desde una rama (sitio local) conectada al centro de conectividad Virtual WAN hacia una red virtual de radio a través de una Aplicación virtual de red (NVA).

![Diagrama de Virtual WAN](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Antes de empezar

Compruebe que se cumplen los criterios siguientes:

*  Tiene una aplicación virtual de red (NVA). Un dispositivo virtual de red es un software de terceros de su elección que normalmente se aprovisiona en Azure Marketplace en una red virtual.

    * Debe asignarse una dirección IP privada a la interfaz de red NVA.

    * El NVA no se implementa en el concentrador virtual. Se debe implementar en una red virtual independiente.

    *  La red virtual de NVA puede tener una o varias redes virtuales conectadas. En este artículo, nos referimos a la red virtual de NVA como "red virtual de radio indirecta". Estas redes virtuales pueden conectarse a la red virtual de NVA mediante el emparejamiento de VNET. Los vínculos de emparejamiento de VNET se representan mediante flechas negras en la ilustración anterior entre VNet 1, VNet 2 y NVA VNet.
*  Ha creado dos redes virtuales. Se usarán como redes virtuales de radio.

    * Los espacios de direcciones de red virtual de radio son: VNet1: 10.0.2.0/24 y VNet2: 10.0.3.0/24. Si necesita información sobre cómo crear una red virtual, consulte [Creación de una red virtual](../virtual-network/quick-create-portal.md).

    * Asegúrese de que no haya ninguna puerta de enlace de red virtual en ninguna de las redes virtuales.

    * Las redes virtuales no requieren ninguna subred de puerta de enlace.

## <a name="1-sign-in"></a><a name="signin"></a>1. Iniciar sesión

Desde un explorador, navegue al [Portal de Azure](https://portal.azure.com) e inicie sesión con su cuenta de Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Creación de una instancia de Virtual WAN

Cree una WAN virtual. Puede usar los siguientes valores de ejemplo o sustituirlos los suyos propios:

* **Nombre de Virtual WAN:** myVirtualWAN
* **Grupo de recursos:** TestRG
* **Ubicación:** Oeste de EE. UU.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Crear un concentrador

Cree el concentrador. Puede usar los siguientes valores de ejemplo o sustituirlos los suyos propios:

* **Ubicación:** Oeste de EE. UU.
* **Nombre:** westushub
* **Espacio de direcciones privadas del concentrador:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-empty-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Creación y aplicación de una tabla de rutas de concentrador

Actualice el concentrador con una tabla de rutas de concentrador. Use los valores de ejemplo siguientes:

* **Espacios de direcciones de red virtual de radio:** (VNet1 y VNet2) 10.0.2.0/24 y 10.0.3.0/24
* **Dirección IP privada de interfaz con red NVA de la red perimetral:** 10.0.4.5

1. Navegue a la instancia de Virtual WAN.
2. Haga clic en el concentrador para el que desea crear una tabla de rutas.
3. Haga clic en los puntos suspensivos **...** y luego, en **Editar concentrador virtual**.
4. En la página **Editar concentrador virtual**, desplácese hacia abajo y marque la casilla **Usar tabla para el enrutamiento**.
5. En la columna **Si el prefijo de destino es**, agregue los espacios de direcciones. En la columna **Enviar al próximo salto**, agregue la dirección IP privada de interfaz con red NVA de la red perimetral.

   > [!NOTE]
   > La red NVA de la red perimetral es aplicable al centro local.
   
6. Haga clic en **Confirmar** para actualizar el recurso del centro de conectividad con la configuración de la tabla de rutas.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Creación de las conexiones de red virtual

Cree una conexión de red virtual desde cada red virtual de radio indirecta (VNet1 y VNet2) al centro de conectividad. Estas conexiones de red virtual se representan mediante flechas azules en la ilustración anterior. Después, cree una conexión de red virtual desde la red virtual de NVA al centro de conectividad (flecha negra de la ilustración).

 En este paso se pueden usar los siguientes valores:

| Nombre de la red virtual| Nombre de conexión|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Repita el procedimiento siguiente en todas las redes virtuales que quiera conectar.

1. En la página de la red WAN virtual, haga clic en **Conexiones de red virtual**.
2. En la página de conexión de red virtual, haga clic en **+ Agregar conexión**.
3. En la página **Agregar conexión**, rellene los campos siguientes:

    * **Nombre de la conexión**: asigne un nombre a la conexión.
    * **Centros**: seleccione el concentrador que desea asociar a esta conexión.
    * **Suscripción**: compruebe la suscripción.
    * **Red virtual**: seleccione la red virtual que quiere conectar con este concentrador. La red virtual no puede tener una puerta de enlace de red virtual ya existente.
4. Haga clic en **Aceptar** para crear la conexión.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Virtual WAN, consulte la página [Introducción a Virtual WAN](virtual-wan-about.md).
