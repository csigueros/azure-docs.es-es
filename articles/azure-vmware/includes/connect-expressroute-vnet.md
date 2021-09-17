---
title: Conexión de ExpressRoute a la puerta de enlace de red virtual
description: Pasos para conectar ExpressRoute a la puerta de enlace de red virtual.
ms.topic: include
ms.date: 12/08/2020
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 6dc579579e315ca24c940a0ed46bc51838aaed77
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122069564"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->


1. Solicite una clave de autorización de ExpressRoute:

   [!INCLUDE [request-authorization-key](request-authorization-key.md)]

1. Acceda a la puerta de enlace de red virtual que va a usar y seleccione **Conexiones** >  **+ Agregar**.

1. En la página **Agregar conexión**, proporcione valores para los campos y seleccione **Aceptar**. 

   | Campo | Value |
   | --- | --- |
   | **Nombre**  | Escriba un nombre para la conexión.  |
   | **Tipo de conexión**  | seleccione **ExpressRoute**.  |
   | **Canjear autorización**  | Asegúrese de que este cuadro está seleccionado.  |
   | **Puerta de enlace de red virtual** | La puerta de enlace de red virtual que tiene pensado usar.  |
   | **Clave de autorización**  | Pegue la clave de autorización que copió anteriormente. |
   | **URI de circuito del mismo nivel**  | Pegue el identificador de ExpressRoute que copió anteriormente.  |

   :::image type="content" source="../media/tutorial-configure-networking/add-connection.png" alt-text="Captura de pantalla en la que se muestra la página Agregar conexión para conectar ExpressRoute a la puerta de enlace de red virtual.":::

Se crea la conexión entre el circuito ExpressRoute y la red virtual.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Captura de pantalla en la que se muestra una conexión de puerta de enlace de red virtual correcta.":::
