---
author: cherylmc
ms.author: cherylmc
ms.date: 06/29/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: f0585ca5bc2a76cb7f969f8a599ee98152b872cf
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108244"
---
Al conectarse a una máquina virtual mediante Azure Bastion, no necesita una dirección IP pública para la máquina virtual. Si no usa la dirección IP pública para nada más, puede desasociarla de la máquina virtual. Para desasociar una dirección IP pública de la máquina virtual, siga estos pasos:

1. Vaya a la máquina virtual y seleccione **Redes**. Seleccione la **IP pública de NIC** para abrir la página de dirección IP pública.

   :::image type="content" source="./media/bastion-remove-ip/networking.png" alt-text="Captura de pantalla de la página Redes.":::

1. En la página **Dirección IP pública** de la máquina virtual, seleccione **Desasociar**.

   :::image type="content" source="./media/bastion-remove-ip/disassociate.png" alt-text="Captura de pantalla de la dirección IP pública para la máquina virtual.":::

1. Seleccione **Sí** para desasociar la dirección IP de la interfaz de red.

   :::image type="content" source="./media/bastion-remove-ip/disassociate-yes.png" alt-text="Captura de pantalla de la desasociación de la dirección IP pública.":::

1. Después de desasociar la dirección IP, puede eliminar el recurso de dirección IP pública. Para eliminar el recurso de dirección IP pública, vaya al grupo de recursos y busque el recurso de dirección IP que desea eliminar. A continuación, seleccione **Eliminar** para eliminar el recurso.

   :::image type="content" source="./media/bastion-remove-ip/delete-resource.png" alt-text="Captura de pantalla de la eliminación del recurso de dirección IP pública.":::