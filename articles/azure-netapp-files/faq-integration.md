---
title: Preguntas frecuentes sobre integración para Azure NetApp Files | Microsoft Docs
description: Aquí se responden las preguntas frecuentes (FAQ) sobre el uso de otros productos o servicios con Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: c48a35b4578488fdab5fc55cdca4e19d97a003d3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269911"
---
# <a name="integration-faqs-for-azure-netapp-files"></a>Preguntas frecuentes sobre integración para Azure NetApp Files

En este artículo se responden las preguntas frecuentes (FAQ) sobre el uso de otros productos o servicios con Azure NetApp Files.  

## <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>¿Puedo usar volúmenes NFS o SMB de Azure NetApp Files con Azure VMware Solution (AVS)?

Puede montar volúmenes NFS de Azure NetApp Files en máquinas virtuales Windows o Linux de AVS. Puede asignar recursos compartidos de SMB de Azure NetApp Files en máquinas virtuales Windows de AVS. Para más información, consulte [Azure NetApp Files con Azure VMware Solution]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

## <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>¿Qué regiones se admiten para usar volúmenes NFS o SMB de Azure NetApp Files con Azure VMware Solution (AVS)?

El uso de volúmenes NFS o SMB de Azure NetApp Files (ANF) con AVS para *montajes de sistemas operativos invitados* se admite en [todas las regiones habilitadas para AVS y ANF](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware,netapp).

## <a name="does-azure-netapp-files-work-with-azure-policy"></a>¿Azure NetApp Files funciona con Azure Policy?

Sí. Azure NetApp Files es un servicio de primera entidad. Se adhiere totalmente a los estándares del proveedor de recursos de Azure. Por lo tanto, Azure NetApp Files se puede integrar en Azure Policy a través de *definiciones de directiva personalizadas*. Para obtener información sobre cómo implementar directivas personalizadas para Azure NetApp Files, consulte [Azure Policy ya disponible para Azure NetApp Files](https://techcommunity.microsoft.com/t5/azure/azure-policy-now-available-for-azure-netapp-files/m-p/2282258) en la comunidad tecnológica de Microsoft. 

## <a name="which-unicode-character-encoding-is-supported-by-azure-netapp-files-for-the-creation-and-display-of-file-and-directory-names"></a>¿Qué codificación de caracteres Unicode admite Azure NetApp Files para la creación y la visualización de nombres de archivo y directorio?   

Azure NetApp Files solo admite nombres de archivo y directorio codificados con el formato de codificación de caracteres Unicode UTF-8 para volúmenes NFS y SMB.

Si intenta crear archivos o directorios con nombres que usan caracteres adicionales o pares suplentes, como caracteres no regulares y emojis que no son compatibles con UTF-8, se producirá un error en la operación. En este caso, un error de un cliente Windows podría ser: "El nombre de archivo especificado no es válido o es demasiado largo. Especifique otro nombre de archivo". 

## <a name="next-steps"></a>Pasos siguientes  

- [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Preguntas frecuentes sobre redes](faq-networking.md)
- [Preguntas más frecuentes de seguridad](faq-security.md)
- [Preguntas más frecuentes sobre rendimiento](faq-performance.md)
- [Preguntas más frecuentes sobre NFS](faq-nfs.md)
- [Preguntas más frecuentes de SMB](faq-smb.md)
- [Preguntas más frecuentes sobre la administración de la capacidad](faq-capacity-management.md)
- [Preguntas frecuentes sobre migración y protección de datos](faq-data-migration-protection.md)
- [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](faq-backup.md)
- [Preguntas frecuentes de la resistencia de la aplicación](faq-application-resilience.md)
