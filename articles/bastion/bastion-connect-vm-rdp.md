---
title: Conexión a una máquina virtual Windows con Azure Bastion
description: Aprenda a usar Azure Bastion para conectarse a una máquina virtual de Azure que ejecuta Windows.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 06/21/2021
ms.author: cherylmc
ms.openlocfilehash: 5718958076d063d64e9d84626346b3a5491500b7
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112464834"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Conexión a una máquina virtual Windows con Azure Bastion

Con Azure Bastion, puede conectarse de forma segura y sin problemas a las máquinas virtuales en SSL directamente en Azure Portal. Cuando se usa Azure Bastion, las máquinas virtuales no necesitan un cliente, un agente o software adicional. En este artículo se muestra cómo conectarse a las máquinas virtuales Windows. Para obtener información sobre la conexión a una máquina virtual Linux, consulte el artículo [Conexión a una máquina virtual Linux](bastion-connect-vm-ssh.md).

Azure Bastion proporciona conectividad segura a todas las máquinas virtuales de la red virtual en la que se aprovisiona. El uso de Azure Bastion protege las máquinas virtuales frente a la exposición de los puertos de RDP/SSH al mundo exterior, al tiempo que ofrece acceso seguro mediante RDP/SSH. Para obtener más información, vea [¿Qué es Azure Bastion?](bastion-overview.md)

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, compruebe que se cumplen los criterios siguientes:

* Ya se ha instalado una red virtual con el host de Bastion.

   Asegúrese de haber configurado un host de Azure Bastion para la red virtual donde está ubicada la máquina virtual. Cuando el servicio Bastion se aprovisiona e implementa en la red virtual, puede usarlo para conectarse a cualquier máquina virtual de la red virtual. Para configurar un host de Azure Bastion, consulte [Creación de un host de Bastion](tutorial-create-host-portal.md#createhost).
* Una máquina virtual Windows en la red virtual.
* Los siguientes roles necesarios:
  * Rol Lector en la máquina virtual.
  * Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual.
  * Rol Lector en el recurso de Azure Bastion.
* Puertos: Para conectarse a la máquina virtual Windows, debe tener abiertos los siguientes puertos en ella:
  * Puertos de entrada: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Conexión

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Pasos siguientes

Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).
