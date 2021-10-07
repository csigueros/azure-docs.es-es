---
title: Emparejamiento de VNet y arquitectura de Azure Bastion
description: Aprenderá cómo se pueden usar conjuntamente el emparejamiento de VNet y Azure Bastion para conectarse a las máquinas virtuales.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: 4e5f474f08116cea39b565e76a9736b3e94eec9f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668799"
---
# <a name="vnet-peering-and-azure-bastion"></a>Emparejamiento de VNet y Azure Bastion

Azure Bastion y el emparejamiento de VNet se pueden usar juntos. Cuando se configura el emparejamiento de VNet, no es necesario implementar Azure Bastion en cada red virtual emparejada. Esto significa que si tiene un host de Azure Bastion configurado en una red virtual (VNet), se puede usar para conectarse a las máquinas virtuales implementadas en una red virtual emparejada sin necesidad de implementar un host de Bastion adicional. Para más información sobre el emparejamiento de redes virtuales, consulte [Acerca del emparejamiento de redes virtuales](../virtual-network/virtual-network-peering-overview.md).

Azure Bastion funciona con los siguientes tipos de emparejamiento:

* **Emparejamiento de red virtual:** conecte redes virtuales que se encuentren en la misma región de Azure.
* **Emparejamiento global de redes virtuales**: conexión de redes virtuales en distintas regiones de Azure.

## <a name="architecture"></a>Architecture

Cuando se configura el emparejamiento de VNet, Azure Bastion se puede implementar en topologías de concentrador y radio o de malla completa. La implementación de Azure Bastion se realiza por red virtual, no por suscripción o cuenta, ni por máquina virtual.

Una vez que haya aprovisionado el servicio Azure Bastion en su red virtual, la experiencia RDP/SSH estará disponible para todas las máquinas virtuales de la misma red virtual, así como las redes virtuales emparejadas. Esto significa que puede consolidar la implementación de Bastion en una sola red virtual y seguir atendiendo a las máquinas virtuales implementadas en una red virtual emparejada, centralizando la implementación global.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagrama de diseño y arquitectura":::

Esta ilustración muestra la arquitectura de una implementación de Azure Bastion en un modelo de concentrador y radio. En este diagrama puede ver la siguiente configuración:

* El host de Bastion se implementa en la red virtual de concentrador centralizada.
* Se implementa el grupo de seguridad de red centralizado (NSG).
* No se requiere ninguna dirección IP pública en la máquina virtual de Azure.

**Pasos:**

1. Conexión a Azure Portal con cualquier explorador HTML5.
2. Asegúrese de que tiene acceso de **lectura** tanto a la máquina virtual de destino como a la red virtual emparejada. Además, compruebe en IAM que el usuario tiene acceso de lectura a los recursos siguientes:
   * Rol Lector en la máquina virtual.
   * Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual.
   * Rol Lector en el recurso de Azure Bastion.
   * Rol Lector en la red virtual (no es necesario si no hay ninguna red virtual emparejada).
3. Para ver Bastion en el menú desplegable **Conectar**, debe seleccionar la suscripción a la que tiene acceso en **Suscripción > Suscripción global**.
4. Selección de la máquina virtual a la que conectarse.
5. Azure Bastion se detecta sin problemas en la red virtual emparejada.
6. Con un solo clic, la sesión RDP/SSH se abre en el explorador.

Para obtener más información sobre cómo conectarse a una máquina virtual a través de Azure Bastion, consulte:

   * [Conexión a una máquina virtual Windows: RDP](bastion-connect-vm-rdp-windows.md)
   * [Conexión a una máquina virtual Windows: SSH](bastion-connect-vm-ssh-windows.md)
   * [Conexión a una máquina virtual Linux: SSH](bastion-connect-vm-ssh-linux.md)
   * [Conexión a una máquina virtual Linux: RDP](bastion-connect-vm-rdp-linux.md)

## <a name="faq"></a>Preguntas más frecuentes

Para obtener las preguntas más frecuentes, vea las [preguntas más frecuentes](bastion-faq.md#peering) sobre emparejamiento de red virtual de Bastion.

## <a name="next-steps"></a>Pasos siguientes

Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).
