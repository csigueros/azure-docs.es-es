---
title: Configuración de los componentes de red NSX mediante Azure VMware Solution
description: Aprenda a usar Azure VMware Solution para configurar los segmentos de red NSX-T.
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 190ec1e93cb9fef795e47693f80e5e9296354b83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699705"
---
# <a name="configure-nsx-network-components-using-azure-vmware-solution"></a>Configuración de los componentes de red NSX mediante Azure VMware Solution

Una nube privada de Azure VMware Solution incluye NSX-T de manera predeterminada. La nube privada viene ya aprovisionada con una puerta de enlace NSX-T de nivel 0 en modo **activo-activo** y una puerta de enlace NSX-T predeterminada de nivel 1 en modo activo-en espera.  Estas puertas de enlace permiten conectar los segmentos (conmutadores lógicos) y proporcionan conectividad horizontal de derecha a izquierda y vertical de arriba abajo. 

Después de implementar Azure VMware Solution, puede configurar los objetos NSX-T necesarios en Azure Portal.  Presenta una vista simplificada de las operaciones NSX-T que un administrador de VMware necesita diariamente y está dirigida a los usuarios que no están familiarizados con NSX-T Manager.  

Tendrá cuatro opciones para configurar los componentes de NSX-T en la consola de Azure VMware Solution:

- **Segments** (Segmentos): cree segmentos que se muestren en NSX-T Manager y vCenter. Para más información, consulte [Uso de Azure Portal para agregar un segmento de NSX-T](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment).

- **DHCP**: cree un servidor DHCP o una retransmisión DHCP si planea usar DHCP.  Para más información, consulte [Uso de Azure Portal para crear un servidor DHCP o una retransmisión](configure-dhcp-azure-vmware-solution.md#use-the-azure-portal-to-create-a-dhcp-server-or-relay).

- **Port mirroring** (Creación de reflejo del puerto): cree la creación de reflejo del puerto para ayudar a solucionar los problemas de red. Para más información, consulte [Configuración de la creación de reflejo del puerto en Azure Portal](configure-port-mirroring-azure-vmware-solution.md).

- **DNS**: cree un reenviador DNS para enviar solicitudes DNS a un servidor DNS designado y resolverlas.  Para más información, consulte [Configuración de un reenviador DNS en Azure Portal](configure-dns-azure-vmware-solution.md).

>[!IMPORTANT]
>Seguirá teniendo acceso a la consola de NSX-T Manager, donde puede usar la configuración avanzada mencionada y otras características de NSX-T. 

