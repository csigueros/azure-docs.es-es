---
title: 'Tutorial: Incorporación de un segmento de red en Azure VMware Solution'
description: Aprenda a crear un segmento de red para usarlo en máquinas virtuales en vCenter.
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: 04681c5d55536e3ee1cc0b12d2ebc94f19b3873f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464447"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Tutorial: Incorporación de un segmento de red en Azure VMware Solution 

Después de implementar Azure VMware Solution, puede configurar un segmento de red NSX-T desde NSX-T Manager o Azure Portal.  Una vez configurados, los segmentos son visibles en Azure VMware Solution, NSX-T Manager y vCenter.  NSX-T viene ya aprovisionado de forma predeterminada con una puerta de enlace NSX-T de nivel 0 en modo **activo-activo** y una puerta de enlace NSX-T predeterminada de nivel 1 en modo **activo-en espera**.  Estas puertas de enlace permiten conectar los segmentos (conmutadores lógicos) y proporcionan conectividad horizontal de derecha a izquierda y vertical de arriba abajo. 

>[!TIP]
>Azure Portal presenta una vista simplificada de las operaciones NSX-T que un administrador de VMware necesita con regularidad y que está dirigida a los usuarios que no están familiarizados con NSX-T Manager. 


En este tutorial aprenderá a:

> [!div class="checklist"]
> * Agregar segmentos de red mediante NSX-T Manager o Azure Portal
> * Comprobar el nuevo segmento de red 

## <a name="prerequisites"></a>Requisitos previos

Una nube privada de Azure VMware Solution con acceso a las interfaces de vCenter y NSX-T Manager. Para obtener más información, consulte el tutorial de [configuración de redes](tutorial-configure-networking.md).

## <a name="use-nsx-t-manager-to-add-network-segment"></a>Uso de NSX-T Manager para agregar un segmento de red 

Las máquinas virtuales creadas en vCenter se colocan en los segmentos de red creados en NSX-T y son visibles en vCenter.

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="use-azure-portal-to-add-an-nsx-t-segment"></a>Uso de Azure Portal para agregar un segmento de NSX-T

[!INCLUDE [create-nsxt-segment-azure-portal-steps](includes/create-nsxt-segment-azure-portal-steps.md)]


## <a name="verify-the-new-network-segment"></a>Comprobación del nuevo segmento de red

Compruebe la presencia del nuevo segmento de red. En este ejemplo, **ls01** es el nuevo segmento de red.

1. En NSX-T Manager, seleccione **Networking** > **Segments** (Redes > Segmentos). 

    :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Captura de pantalla que muestra la confirmación, y el estado del nuevo segmento de red está presente en NSX-T.":::

1. En vCenter, seleccione **Networking** > **SDDC-Datacenter** (Redes > SDDC: centro de datos).

    :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Captura de pantalla que muestra la confirmación de que el nuevo segmento de red está presente en vCenter.":::

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un segmento de red de NSX-T que se usa para las máquinas virtuales en vCenter. 

Ahora puede: 

- [Configurar y administrar DHCP para Azure VMware Solution](configure-dhcp-azure-vmware-solution.md)
- [Crear una biblioteca de contenido para implementar máquinas virtuales en Azure VMware Solution](deploy-vm-content-library.md) 
- [Emparejamiento de entornos locales con una nube privada](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
