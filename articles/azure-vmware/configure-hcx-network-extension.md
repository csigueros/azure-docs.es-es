---
title: Creación de una extensión de red HCX
description: Aprenda a extender cualquier red de su entorno local a Azure VMware Solution.
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: fd58285ab7e2999c758e818020e39799a19f6c53
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837890"
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


