---
title: Conexión de ExpressRoute a la puerta de enlace de red virtual
description: Pasos para conectar ExpressRoute a la puerta de enlace de red virtual.
ms.topic: include
ms.date: 12/08/2020
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 721a298deb1c3122a1781c5803c93492b43a4b59
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638222"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->


1. Solicite una clave de autorización de ExpressRoute:

   [!INCLUDE [request-authorization-key](request-authorization-key.md)]

1. Acceda a la puerta de enlace de red virtual que va a usar y seleccione **Conexiones** >  **+ Agregar**.

1. En la página **Agregar conexión**, proporcione valores para los campos y seleccione **Aceptar**. 

   | Campo | Valor |
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
