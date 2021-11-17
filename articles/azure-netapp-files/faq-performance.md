---
title: Preguntas frecuentes sobre el rendimiento para Azure NetApp Files | Microsoft Docs
description: Aquí se responden las preguntas frecuentes (P+F) sobre el rendimiento de Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: e7b489a2f9960b39fcb8035889b47468c2af0478
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269951"
---
# <a name="performance-faqs-for-azure-netapp-files"></a>Preguntas frecuentes sobre el rendimiento para Azure NetApp Files

En este artículo se responden las preguntas frecuentes (P+F) sobre el rendimiento de Azure NetApp Files.

## <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>¿Qué debo hacer para optimizar o ajustar el rendimiento de Azure NetApp Files?

Puede realizar las siguientes acciones según los requisitos de rendimiento: 
- Asegúrese de que la máquina virtual tiene el tamaño apropiado.
- Habilite redes aceleradas para la máquina virtual.
- Seleccione el nivel de servicio deseado y el tamaño de grupo de capacidad.
- Cree un volumen con el tamaño de cuota deseada para la capacidad y el rendimiento.

## <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>¿Cómo puedo convertir los niveles de servicio basado en el rendimiento de Azure NetApp Files a IOPS?

Puede convertir MB/s a IOPS mediante el uso de la siguiente fórmula:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

## <a name="how-do-i-change-the-service-level-of-a-volume"></a>¿Cómo puedo cambiar el nivel de servicio de un volumen?

Puede cambiar el nivel de servicio de un volumen existente al moverlo a otro grupo de capacidad que use el [nivel de servicio](azure-netapp-files-service-levels.md) que quiere para dicho volumen. Consulte [Cambio dinámico del nivel de servicio de un volumen](dynamic-change-volume-service-level.md). 

## <a name="how-do-i-monitor-azure-netapp-files-performance"></a>¿Cómo se puede supervisar el rendimiento de Azure NetApp Files?

Azure NetApp Files proporciona métricas de rendimiento del volumen. También puede usar Azure Monitor para supervisar las métricas de uso de Azure NetApp Files.  Consulte [Métricas de Azure NetApp Files](azure-netapp-files-metrics.md) para obtener la lista de métricas de rendimiento de Azure NetApp Files.

## <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>¿Cuál es el impacto en el rendimiento de Kerberos en NFSv4.1?

Consulte [Impacto en el rendimiento de Kerberos en los volúmenes de NFSv4.1](performance-impact-kerberos.md) para obtener información sobre las opciones de seguridad de NFSv4.1, los vectores de rendimiento que se hayan probado y el impacto esperado en el rendimiento. 

## <a name="does-azure-netapp-files-support-smb-direct"></a>¿Azure NetApp Files admite SMB directo?

No, Azure NetApp Files no admite SMB directo. 

## <a name="is-nic-teaming-supported-in-azure"></a>¿Se admite la formación de equipos NIC en Azure?

La formación de equipos NIC no se admite en Azure. Aunque se admiten varias interfaces de red en las máquinas virtuales de Azure, representan una construcción lógica más que física. Como tal, no proporcionan tolerancia a errores.  Además, el ancho de banda disponible para una máquina virtual de Azure se calcula para el propio equipo y no para ninguna interfaz de red individual.

## <a name="are-jumbo-frames-supported"></a>¿Se admiten tramas gigantes?

No se admiten tramas gigantes con máquinas virtuales de Azure.

## <a name="next-steps"></a>Pasos siguientes  

- [Impacto en el rendimiento de Kerberos en los volúmenes NFSv4.1](performance-impact-kerberos.md)
- [Consideraciones sobre el rendimiento de Azure NetApp Files](azure-netapp-files-performance-considerations.md    )
- [Banco de pruebas de rendimiento de recomendaciones de pruebas para Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md )
- [Bancos de pruebas de rendimiento para Linux](performance-benchmarks-linux.md)
- [Impacto en el rendimiento de Kerberos en los volúmenes NFSv4.1](performance-impact-kerberos.md)
- [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Preguntas frecuentes sobre redes](faq-networking.md)
- [Preguntas más frecuentes de seguridad](faq-security.md)
- [Preguntas más frecuentes sobre NFS](faq-nfs.md)
- [Preguntas más frecuentes de SMB](faq-smb.md)
- [Preguntas más frecuentes sobre la administración de la capacidad](faq-capacity-management.md)
- [Preguntas frecuentes sobre migración y protección de datos](faq-data-migration-protection.md)
- [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](faq-backup.md)
- [Preguntas frecuentes de la resistencia de la aplicación](faq-application-resilience.md)
- [Preguntas más frecuentes sobre la integración](faq-integration.md)