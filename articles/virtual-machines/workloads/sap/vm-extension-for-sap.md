---
title: Implementación de la extensión de máquina virtual de Azure para soluciones de SAP | Microsoft Docs
description: Aprenda a implementar la extensión de máquina virtual para SAP.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: bc85e68ed643e8a39f22343cfb939ab803fcdaef
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067387"
---
# <a name="implement-the-azure-vm-extension-for-sap-solutions"></a>Implementación de la extensión de máquina virtual de Azure para soluciones de SAP

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[sap-resources]:vm-extension-for-sap.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos SAP)
[new-monitoring]:vm-extension-for-sap.md#38d9f33f-d0af-4b8f-8134-f1f97d656fb6 (Nueva versión de la extensión de máquina virtual de Azure para SAP)
[std-extension]:vm-extension-for-sap-standard.md (Versión estándar de la extensión de máquina virtual de Azure para soluciones de SAP)
[new-extension]:vm-extension-for-sap-new.md (Nueva versión de la extensión de máquina virtual de Azure para soluciones de SAP)
[azure-ps]:/powershell/azure/
[azure-cli]:/cli/azure/install-classic-cli
[azure-cli-2]:/cli/azure/install-azure-cli

[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Escenarios de implementación de máquinas virtuales para SAP en Microsoft Azure)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solución de supervisión de Azure para SAP)

> [!NOTE]
> Instrucción de soporte técnico general: la compatibilidad con la extensión de Azure para SAP se proporciona a través de los canales de soporte técnico de SAP. Si necesita ayuda con la extensión de máquina virtual de Azure para soluciones de SAP, abra un caso de soporte técnico con Soporte técnico de SAP.
 
Cuando la máquina virtual esté preparada tal y como se describe en [Escenarios de implementación de máquinas virtuales para SAP en Azure][deployment-guide-3], el Agente de máquina virtual de Azure se instala en la máquina virtual. El siguiente paso es implementar la extensión de Azure para SAP, que está disponible en el repositorio de extensiones de Azure, en los centros de datos globales de Azure. Para más información, consulte [Planeación e implementación de Azure Virtual Machines para SAP en NetWeaver][planning-guide-9.1].
 
Para asegurarse de que SAP es compatible con el entorno, habilite la extensión de máquina virtual de Azure para soluciones de SAP tal y como se describe en Configuración de la extensión de Azure para SAP.

## <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos de SAP

Cuando se configura la implementación de software de SAP, se necesitan los siguientes recursos SAP:

* Nota de SAP [1928533], que incluye:
  * La lista de tamaños de máquina virtual de Azure que se admiten para la implementación del software de SAP
  * Información importante sobre la capacidad para los tamaños de máquina virtual de Azure
  * Software de SAP admitido y combinaciones de sistema operativo y base de datos
  * Versión del kernel de SAP requerida para Windows y Linux en Microsoft Azure

* La nota de SAP [2015553] enumera los requisitos previos para las implementaciones de software de SAP admitidas por SAP en Azure.
* La nota de SAP [2178632] contiene información detallada sobre todas las métricas de supervisión notificadas para SAP en Azure.
* La nota de SAP [1409604] incluye la versión de SAP Host Agent necesaria para Windows en Azure.
* La nota de SAP [2191498] incluye la versión de SAP Host Agent necesaria para Linux en Azure.
* La nota de SAP [2243692] incluye información acerca de las licencias de SAP en Linux en Azure.
* La nota de SAP [1999351] contiene más soluciones de problemas de la extensión de Azure para SAP.
* Cmdlets de PowerShell específicos de SAP que forman parte de [Azure PowerShell][azure-ps].
* Comandos de la CLI de Azure específicos de SAP que forman parte de la [CLI de Azure][azure-cli-2].
 
## <a name="differences-between-the-two-versions-of-the-azure-vm-extension-for-sap-solutions"></a>Diferencias entre las dos versiones de la extensión de máquina virtual de Azure para soluciones de SAP

Hay dos versiones de la extensión de VM para SAP. Compruebe los requisitos previos de SAP y las versiones mínimas del kernel de SAP y de SAP Host Agent requeridas, en los recursos que se indican en [Recursos de SAP][sap-resources].

### <a name="standard-version-of-vm-extension-for-sap"></a>Versión estándar de la extensión de máquina virtual para SAP

Esta versión es la extensión de máquina virtual estándar actual para SAP. Hay algunas excepciones en las que Microsoft recomienda instalar la nueva extensión de máquina virtual para SAP. Además, al abrir un caso de soporte técnico, el soporte técnico de SAP puede solicitar la instalación de la nueva extensión de máquina virtual. Para más información sobre cuándo usar la nueva versión de la extensión de máquina virtual para SAP, consulte el capítulo [Nueva versión de la extensión de máquina virtual para SAP][new-monitoring].
 
### <a name="new-version-of-vm-extension-for-sap"></a><a name="38d9f33f-d0af-4b8f-8134-f1f97d656fb6"></a>Nueva versión de la extensión de máquina virtual para SAP

Esta versión es la nueva extensión de máquina virtual de Azure para soluciones de SAP. Con más mejoras y nuevas ofertas de Azure, se ha creado la nueva extensión para poder supervisar todos los recursos de Azure de una máquina virtual. Esta extensión necesita acceso a Internet a la dirección URL "management.azure.com". Admite opciones de almacenamiento adicionales, por ejemplo, discos estándar y sistemas operativos. Elija la nueva versión de la extensión de máquina virtual si se aplica una de las siguientes opciones:
 
* Quiere instalar la extensión de máquina virtual con Terraform, plantillas de Azure Resource Manager o con otros medios distintos de la CLI de Azure o Azure PowerShell.
* Quiere instalar la extensión en SUSE SLES 15 o superior.
* Quiere instalar la extensión en Red Hat Enterprise Linux 8.1 o posterior.
* Quiere usar un disco Ultra de Azure o Managed Disks estándar
* El servicio de soporte técnico de Microsoft o SAP le pide que instale la nueva extensión.
 
## <a name="recommendation"></a>Recomendación

Actualmente se recomienda usar la versión estándar de la extensión para cada instalación en la que no se aplique ninguno de los casos de uso de la nueva versión de la extensión. Actualmente estamos trabajando en la mejora de la nueva versión de la extensión de máquina virtual para poder convertirla en la versión predeterminada y en desuso de la versión estándar de la extensión. Durante este tiempo, puede usar la nueva versión. Sin embargo, debe asegurarse de que la extensión de máquina virtual puede acceder a management.azure.com.
 
> [!NOTE]
> Asegúrese de desinstalar la extensión de máquina virtual antes de cambiar entre las dos versiones.

## <a name="next-steps"></a>Pasos siguientes
* [Versión estándar de la extensión de máquina virtual de Azure para soluciones de SAP][std-extension]
* [Nueva versión de la extensión de máquina virtual de Azure para soluciones de SAP][new-extension]

