---
title: Incorporación de un segmento de red de NSX-T en Azure VMware Solution
description: Pasos para agregar un segmento de red de NSX-T para Azure VMware Solution en Azure Portal.
ms.topic: include
ms.date: 07/16/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 812266ef6c7ed937c21c1c78c2c9b2aa5ff1043d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638189"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->


>[!NOTE]
>Si tiene previsto usar DHCP, deberá [configurar un servidor DHCP o una retransmisión DHCP](../configure-dhcp-azure-vmware-solution.md) antes de configurar un segmento de red NSX-T. 

1. En la nube privada de Azure VMware Solution, en **Workload Networking** (Red de la carga de trabajo), seleccione **Segments** > **Add** (Segmentos > Agregar). 

2. Proporcione los detalles del nuevo segmento lógico y seleccione **Aceptar**.

   :::image type="content" source="../media/networking/add-new-nsxt-segment.png" alt-text="Captura de pantalla que muestra cómo agregar un nuevo segmento NSX-T en Azure Portal.":::

   - **Segment name** (Nombre del segmento): el nombre del segmento que está visible en vCenter.

   - **Subnet gateway** (Puerta de enlace de subred): dirección IP de la puerta de enlace de la subred del segmento con una máscara de subred. Las máquinas virtuales están conectadas a un segmento lógico y todas las máquinas virtuales que se conectan a este segmento pertenecen a la misma subred.  Además, todas las máquinas virtuales conectadas a este segmento lógico deben llevar una dirección IP del mismo segmento.

   - **DHCP** (opcional): intervalos DHCP para un segmento lógico. Para consumir DHCP en segmentos, debe configurar un [servidor DHCP o una retransmisión DHCP](../configure-dhcp-azure-vmware-solution.md).    

   >[!NOTE]
   >**Connected gateway** (Puerta de enlace conectada): se selecciona de manera predeterminada y es de solo lectura.  Muestra la puerta de enlace de nivel 1 y el tipo de información del segmento. 
   >
   >- **T1**: nombre de la puerta de enlace de nivel 1 en NSX-T Manager. Una nube privada viene ya aprovisionada con una puerta de enlace NSX-T de nivel 0 en modo activo-activo y una puerta de enlace NSX-T predeterminada de nivel 1 en modo activo-en espera.  Los segmentos creados a través de la consola de Azure VMware Solution solo se conectan a la puerta de enlace de nivel 1 predeterminada, y las cargas de trabajo de estos segmentos obtienen la conectividad horizontal de derecha a izquierda y vertical de arriba abajo. Solo puede crear más puertas de enlace de nivel 1 mediante NSX-T Manager. Las puertas de enlace de nivel 1 creadas desde la consola de NSX-T Manager no son visibles en la consola de Azure VMware Solution. 
   >
   >- **Type** (Tipo): segmento de superposición compatible con Azure VMware Solution.

El segmento ahora está visible en Azure VMware Solution, NSX-T Manager y vCenter.
