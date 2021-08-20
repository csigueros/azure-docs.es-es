---
title: Adición de un segmento de red de NSX-T
description: Pasos para agregar un segmento de red de NSX-T para Azure VMware Solution en NSX-T Manager.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: ad02977e422a3bdbe0158dd92761ae91a6260fd5
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229331"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->

1. In NSX-T Manager, seleccione **Networking** > **Segments** (Redes > Segmentos) y, a continuación, seleccione **Add Segment** (Agregar segmento). 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Captura de pantalla que muestra cómo agregar un nuevo segmento en NSX-T Manager.":::

1. Escriba un nombre para el segmento.

1. Seleccione la puerta de enlace de nivel 1 (TNTxx-T1) para **Connected Gateway** (Puerta de enlace conectada) y deje el campo **Type** (Tipo) como Flexible.

1. Seleccione la superposición preconfigurada **Transport Zone** (Zona de transporte) (TNTxx-OVERLAY-TZ) y, a continuación, seleccione **Set Subnets** (Establecer subredes). 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Captura de pantalla que muestra los detalles de los segmentos para agregar un nuevo segmento de red de NSX-T.":::

1. Escriba la dirección IP de la puerta de enlace y, luego, seleccione **Add** (Agregar). 

   >[!IMPORTANT]
   >La dirección IP debe estar en un bloque de direcciones RFC1918 que no se superponga, lo que garantiza la conexión a las máquinas virtuales en el nuevo segmento.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Captura de pantalla que muestra la dirección IP de la puerta de enlace para el nuevo segmento.":::

1. Seleccione **Apply** (Aplicar) y, a continuación, seleccione **Save** (Guardar).

1. Seleccione **No** para rechazar la opción para continuar con la configuración del segmento. 


