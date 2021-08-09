---
title: 'Trabajo remoto mediante Bastion: Azure Bastion'
description: Aprenda a usar Azure Bastion para habilitar el trabajo remoto debido a la pandemia de la COVID-19.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 60709bf337c945d1ece7ecc5453350b0e126b046
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540356"
---
# <a name="working-remotely-using-azure-bastion"></a>Trabajo remoto mediante Azure Bastion

Azure Bastion desempeña un papel fundamental en la compatibilidad con escenarios de trabajo remoto, ya que permite a los usuarios con conectividad a Internet acceder a máquinas virtuales de Azure. En concreto, permite a los administradores de TI administrar sus aplicaciones que se ejecutan en Azure en cualquier momento y desde cualquier lugar del mundo.

>[!NOTE]
>En este artículo se describe cómo puede aprovechar Azure Bastion, Azure, la red de Microsoft y el ecosistema de asociados de Azure para trabajar de forma remota y mitigar los problemas de red que experimenta debido a la crisis de la COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Acceso seguro a las máquinas virtuales

En concreto, Azure Bastion proporciona conectividad RDP/SSH segura y sin problemas a las máquinas virtuales dentro de la red virtual de Azure, directamente en Azure Portal, sin usar una dirección IP pública. Para obtener más información sobre la arquitectura de Azure Bastion y las características clave, consulte [Qué es Azure Bastion](bastion-overview.md).

Azure Bastion se implementa en cada red virtual, lo que significa que las empresas pueden configurar y administrar una instancia de Azure Bastion para admitir rápidamente el acceso de usuarios remotos a máquinas virtuales dentro de una red virtual de Azure. Para obtener instrucciones sobre cómo crear y administrar Azure Bastion, consulte [Creación de un host bastión](./tutorial-create-host-portal.md).

## <a name="next-steps"></a>Pasos siguientes

* Configure Azure Bastion con [Azure Portal](./tutorial-create-host-portal.md), [PowerShell](bastion-create-host-powershell.md) o la CLI de Azure.

* Para más información, lea las [P+F sobre Bastion](bastion-faq.md).