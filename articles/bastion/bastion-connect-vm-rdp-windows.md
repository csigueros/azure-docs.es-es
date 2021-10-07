---
title: Conexión mediante RDP a una máquina virtual Windows
titleSuffix: Azure Bastion
description: Aprenda a usar Azure Bastion para conectarse a una máquina virtual Windows mediante RDP.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 1a1423a0587a64452671e74a210baa9d2ad16ea8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699556"
---
# <a name="create-an-rdp-connection-to-a-windows-vm-using-azure-bastion"></a>Creación de una conexión RDP a una máquina virtual Windows mediante Azure Bastion

En este artículo se muestra cómo crear de forma segura y sin problemas una conexión RDP a las máquinas virtuales Windows ubicadas en una red virtual de Azure directamente desde Azure Portal. Cuando se usa Azure Bastion, las máquinas virtuales no necesitan un cliente, un agente o software adicional. También puede conectarse a una máquina virtual Windows mediante SSH. Para obtener información, consulte [Creación de una conexión SSH a una máquina virtual Windows](bastion-connect-vm-ssh-windows.md).

Azure Bastion proporciona conectividad segura a todas las máquinas virtuales de la red virtual en la que se aprovisiona. El uso de Azure Bastion protege las máquinas virtuales frente a la exposición de los puertos de RDP/SSH al mundo exterior, al tiempo que ofrece acceso seguro mediante RDP/SSH. Para obtener más información, vea [¿Qué es Azure Bastion?](bastion-overview.md)

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, compruebe que se cumplen los criterios siguientes:

* Ya se ha instalado una red virtual con el host de Bastion.

  * Asegúrese de haber configurado un host de Azure Bastion para la red virtual donde está ubicada la máquina virtual. Cuando el servicio Bastion se aprovisiona e implementa en la red virtual, puede usarlo para conectarse a cualquier máquina virtual de la red virtual. 
  * Para configurar un host de Azure Bastion, consulte [Creación de un host de Bastion](tutorial-create-host-portal.md#createhost). Si tiene previsto configurar valores de puerto personalizados, asegúrese de seleccionar la SKU estándar al configurar Bastion.

* Una máquina virtual Windows en la red virtual.

### <a name="required-roles"></a>Roles necesarios

* Rol Lector en la máquina virtual.
* Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual.
* Rol Lector en el recurso de Azure Bastion.

### <a name="ports"></a>Puertos

Para conectarse a la máquina virtual Windows, debe tener abiertos los siguientes puertos en ella:

*   Puerto de entrada: RDP (3389) ***o***
*   Puerto de entrada: valor personalizado (tendrá que especificar este puerto personalizado al conectarse a la máquina virtual por medio de Azure Bastion)

> [!NOTE]
> Si quiere especificar un valor de puerto personalizado, Azure Bastion se debe configurar mediante la SKU estándar. La SKU básica no permite especificar puertos personalizados. La SKU estándar se encuentra actualmente en versión preliminar.
>

## <a name="connect"></a><a name="rdp"></a>Conexión

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Pasos siguientes

Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).
