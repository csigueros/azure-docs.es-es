---
title: Conexión a una máquina virtual Linux mediante RDP
titleSuffix: Azure Bastion
description: Aprenda a usar Azure Bastion para conectarse a una máquina virtual Linux mediante RDP.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 9fbe16756bc178ed012af7f96e937614b0b3a855
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699717"
---
# <a name="create-an-rdp-connection-to-a-linux-vm-using-azure-bastion-preview"></a>Creación de una conexión RDP a una máquina virtual Linux mediante Azure Bastion (versión preliminar)

En este artículo se muestra cómo crear de forma segura y sin problemas una conexión RDP a las máquinas virtuales Linux ubicadas en una red virtual de Azure directamente desde Azure Portal. Cuando se usa Azure Bastion, las máquinas virtuales no necesitan un cliente, un agente o software adicional. También se puede conectar a una máquina virtual Linux mediante SSH. Para obtener información, vea [Creación de una conexión SSH a una máquina virtual Linux](bastion-connect-vm-ssh-linux.md).

Azure Bastion proporciona conectividad segura a todas las máquinas virtuales de la red virtual en la que se aprovisiona. El uso de Azure Bastion protege las máquinas virtuales frente a la exposición de los puertos de RDP/SSH al mundo exterior, al tiempo que ofrece acceso seguro mediante RDP/SSH. Para obtener más información, vea [¿Qué es Azure Bastion?](bastion-overview.md)

> [!NOTE]
> El uso de RDP para conectarse a una máquina virtual Linux requiere la SKU estándar de Azure Bastion, que actualmente está en versión preliminar.
>

Al usar Azure Bastion para conectarse a una máquina virtual Linux mediante RDP, debe usar el nombre de usuario y la contraseña para la autenticación.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, compruebe que se cumplen los criterios siguientes:

Asegúrese de haber configurado un host de Azure Bastion para la red virtual donde reside la máquina virtual. Para más información, consulte [Create an Azure Bastion host](tutorial-create-host-portal.md) (Creación de un host de Azure Bastion). Cuando el servicio Bastion se aprovisiona e implementa en la red virtual, puede usarlo para conectarse a cualquier máquina virtual de esta red virtual.

Para conectarse mediante RDP a una máquina virtual Linux, también debe asegurarse de que xrdp esté instalado y configurado en dicha máquina. Para obtener información sobre cómo hacerlo, consulte [Uso de xrdp con Linux](../virtual-machines/linux/use-remote-desktop.md).

### <a name="required-roles"></a>Roles necesarios

Para crear una conexión, se requieren los siguientes roles:
* Rol de lector en la máquina virtual
* Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual
* Rol de lector en el recurso de Azure Bastion

### <a name="ports"></a>Puertos

Para conectarse a la máquina virtual Linux mediante RDP, debe tener abiertos los siguientes puertos en la máquina virtual:
*   Puerto de entrada: RDP (3389) *o*
*   Puerto de entrada: valor personalizado (tendrá que especificar este puerto personalizado al conectarse a la máquina virtual por medio de Azure Bastion)

### <a name="supported-configurations"></a>Configuraciones admitidas

Actualmente, Azure Bastion solo admite la conexión a máquinas virtuales Linux mediante RDP a través de **xrdp**.

## <a name="connect"></a><a name="rdp"></a>Conexión

[!INCLUDE [Connect to a Linux VM using RDP](../../includes/bastion-vm-rdp-linux.md)]
 
## <a name="next-steps"></a>Pasos siguientes

Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).
