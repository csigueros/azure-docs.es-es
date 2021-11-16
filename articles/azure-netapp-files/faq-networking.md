---
title: Preguntas frecuentes sobre redes para Azure NetApp Files | Microsoft Docs
description: Aquí se responden las preguntas frecuentes (P+F) sobre las redes de Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 62dff22000c27125d278c06aa41228264150a8c0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269908"
---
# <a name="networking-faqs-for-azure-netapp-files"></a>Preguntas frecuentes sobre redes para Azure NetApp Files

En este artículo se responden algunas preguntas frecuentes sobre las redes de Azure NetApp Files. 

## <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>¿La ruta de acceso de datos de NFS o SMB pasa a través de Internet?  

No. La ruta de acceso de datos de NFS o SMB no pasa a través de Internet. Azure NetApp Files es un servicio nativo de Azure que está implementado en Azure Virtual Network (VNet) en donde el servicio está disponible. Azure NetApp Files usa una subred delegada y aprovisiona una interfaz de red directamente en la red virtual (VNet). 

Consulte [Directrices para el planeamiento de red de Azure NetApp Files](./azure-netapp-files-network-topologies.md) para una información más detallada.  

## <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>¿Puedo conectar una red virtual que ya haya creado con el servicio Azure NetApp Files?

Sí, puede conectar redes virtuales que haya creado al servicio. 

Consulte [Directrices para el planeamiento de red de Azure NetApp Files](./azure-netapp-files-network-topologies.md) para una información más detallada.  

## <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>¿Puedo montar un volumen NFS de Azure NetApp Files mediante el nombre FQDN de DNS?

Sí que puede, si crea las entradas DNS necesarias. Azure NetApp Files proporciona la dirección IP de servicio para el volumen aprovisionado. 

> [!NOTE] 
> Azure NetApp Files puede implementar direcciones IP adicionales para el servicio según sea necesario.  Puede que sea necesario actualizar las entradas DNS periódicamente.

## <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>¿Puedo establecer o seleccionar mi propia dirección IP para un volumen de Azure NetApp Files?  

No. La asignación de direcciones IP a volúmenes de Azure NetApp Files es dinámica. No se admiten las asignaciones de IP estáticas. 

## <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>¿Admite Azure NetApp Files la red virtual de pila dual (IPv4 e IPv6)?

No, actualmente Azure NetApp Files no admite la red virtual de pila dual (IPv4 e IPv6).  

## <a name="is-the-number-of-the-ip-addresses-using-azure-vmware-solutions-for-guest-os-mounts-limited-to-1000"></a>¿Está limitado a 1000 el número de direcciones IP que usan Azure VMware Solutions para los montajes de sistemas operativos invitados?

No. Azure VMware Solutions está detrás de una puerta de enlace de ER, lo que hace que se comporte de forma similar a los sistemas locales. El número de "hosts" e "invitados" de AVS no es visible para Azure NetApp Files, y el [límite de 1000 direcciones IP](azure-netapp-files-resource-limits.md#resource-limits) no es aplicable.
 
## <a name="next-steps"></a>Pasos siguientes  

- [Preguntas más frecuentes acerca de Microsoft Azure ExpressRoute](../expressroute/expressroute-faqs.md)
- [Preguntas más frecuentes acerca de Azure Virtual Network](../virtual-network/virtual-networks-faq.md)
- [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Preguntas más frecuentes de seguridad](faq-security.md)
- [Preguntas más frecuentes sobre rendimiento](faq-performance.md)
- [Preguntas más frecuentes sobre NFS](faq-nfs.md)
- [Preguntas más frecuentes de SMB](faq-smb.md)
- [Preguntas más frecuentes sobre la administración de la capacidad](faq-capacity-management.md)
- [Preguntas frecuentes sobre migración y protección de datos](faq-data-migration-protection.md)
- [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](faq-backup.md)
- [Preguntas frecuentes de la resistencia de la aplicación](faq-application-resilience.md)
- [Preguntas más frecuentes sobre la integración](faq-integration.md)