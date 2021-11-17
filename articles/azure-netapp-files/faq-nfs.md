---
title: Preguntas frecuentes sobre NFS para Azure NetApp Files | Microsoft Docs
description: Aquí se responden las preguntas frecuentes (P+F) sobre el protocolo NFS de Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/19/2021
ms.openlocfilehash: e9c38f573a613debbb9f3e3c6b1da00ab23f92f1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269869"
---
# <a name="nfs-faqs-for-azure-netapp-files"></a>Preguntas frecuentes sobre NFS para Azure NetApp Files

En este artículo se responden las preguntas frecuentes (P+F) sobre el protocolo NFS de Azure NetApp Files.

## <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Quiero tener un volumen montado automáticamente cuando se inicia o se reinicia una máquina virtual de Azure.  ¿Cómo tengo que configurar mi host para los volúmenes persistentes de NFS?

Para que un volumen NFS se monte automáticamente al inicio o reinicio de una máquina virtual, agregue una entrada al archivo `/etc/fstab` en el host. 

Consulte [Montaje o desmontaje de un volumen para máquinas virtuales Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para consultar los detalles.  

## <a name="what-nfs-version-does-azure-netapp-files-support"></a>¿Qué versión de NFS admite Azure NetApp Files?

Azure NetApp Files admite NFSv3 y NFSv4.1. Se pueden [crear volúmenes](azure-netapp-files-create-volumes.md) con cualquier versión de NFS. 

## <a name="how-do-i-enable-root-squashing"></a>¿Cómo se puede habilitar root squashing?

El usuario puede especificar si la cuenta raíz tendrá o no acceso al volumen por medio de la directiva de exportación del volumen. Para más información, consulte [Configuración de la directiva de exportación para un volumen NFS](azure-netapp-files-configure-export-policy.md).

## <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>¿Puedo usar la misma ruta de acceso de archivo (token de creación de volumen) para varios volúmenes?

Sí, puede hacerlo. Sin embargo, la ruta de acceso al archivo debe ser única dentro de cada subred.     

## <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Cuando intento acceder a volúmenes NFS a través de un cliente de Windows, ¿por qué el cliente tarda mucho tiempo en buscar carpetas y subcarpetas?

Asegúrese de que `CaseSensitiveLookup` está habilitado en el cliente de Windows para acelerar la búsqueda de carpetas y subcarpetas:

1. Use el siguiente comando de PowerShell para habilitar CaseSensitiveLookup:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Monte el volumen en el servidor de Windows.   
    Ejemplo:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

## <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>¿Cómo admite Azure NetApp Files el bloqueo de archivos de NFSv4.1? 

En el caso de los clientes de NFSv4.1, Azure NetApp Files admite el mecanismo de bloqueo de archivos de NFSv4.1 que mantiene el estado de todos los bloqueos de archivos en un modelo basado en concesión. 

Por RFC 3530, Azure NetApp Files define un período de concesión único para todos los estados de un cliente NFS. Si el cliente no renueva su concesión dentro del período definido, el servidor liberará todos los estados asociados a la concesión del cliente.  

Por ejemplo, si un cliente que monta un volumen deja de responder o se bloquea más allá de los tiempos de espera, se liberarán los bloqueos. El cliente puede renovar su concesión de forma explícita o implícita realizando operaciones como leer un archivo.   

Un período de gracia define un período de procesamiento especial en el que los clientes pueden intentar reclamar su estado de bloqueo durante una recuperación del servidor. El tiempo de espera predeterminado para las concesiones es de 30 segundos con un período de gracia de 45 segundos. Transcurrido ese tiempo, se liberará la concesión del cliente. 

## <a name="next-steps"></a>Pasos siguientes  

- [Preguntas más frecuentes acerca de Microsoft Azure ExpressRoute](../expressroute/expressroute-faqs.md)
- [Preguntas más frecuentes acerca de Azure Virtual Network](../virtual-network/virtual-networks-faq.md)
- [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Preguntas más frecuentes sobre el rendimiento de SMB para Azure NetApp Files](azure-netapp-files-smb-performance.md)
- [Preguntas frecuentes sobre redes](faq-networking.md)
- [Preguntas más frecuentes de seguridad](faq-security.md)
- [Preguntas más frecuentes sobre rendimiento](faq-performance.md)
- [Preguntas más frecuentes de SMB](faq-smb.md)
- [Preguntas más frecuentes sobre la administración de la capacidad](faq-capacity-management.md)
- [Preguntas frecuentes sobre migración y protección de datos](faq-data-migration-protection.md)
- [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](faq-backup.md)
- [Preguntas frecuentes de la resistencia de la aplicación](faq-application-resilience.md)
- [Preguntas más frecuentes sobre la integración](faq-integration.md)