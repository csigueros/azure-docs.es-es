---
title: Creación de una extensión de red HCX
description: Aprenda a extender cualquier red de su entorno local a Azure VMware Solution.
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: 81e984975712e76033102c030093fc94abb2796f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456202"
---
# <a name="create-a-hcx-network-extension"></a>Creación de una extensión de red HCX

Este es un paso opcional para extender cualquier red de su entorno local a Azure VMware Solution.

1. En **Servicios**, seleccione **Extensión de red** > **Create a Network Extension** (Crear una extensión de red).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Captura de pantalla que muestra las selecciones para empezar a crear una extensión de red." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Seleccione cada una de las redes que quiere extender a Azure VMware Solution y, a continuación, seleccione **Next** (Siguiente).

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Captura de pantalla que muestra la selección de una red.":::

1. Escriba la dirección IP de puerta de enlace local para cada una de las redes que va a extender y, a continuación, seleccione **Enviar**.

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Captura de pantalla que muestra la entrada de una dirección IP de puerta de enlace.":::

   La extensión de red tardará unos minutos en finalizar. Cuando lo haga, verá que el estado cambia a **Extension complete** (Extensión completada).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Captura de pantalla que muestra el estado de extensión completada.":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha configurado la extensión de red HCX, también puede obtener información sobre:

- [Guía de VMware HCX Mobility Optimized Networking (MON)](vmware-hcx-mon-guidance.md)
