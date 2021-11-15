---
title: Preguntas más frecuentes acerca de Azure NetApp Files | Microsoft Docs
description: Aquí se responden las preguntas frecuentes (P+F) sobre la administración de la capacidad de Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 1f28b7b590ef2a7a3894f9eff7eff7199167afc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269987"
---
# <a name="capacity-management-faqs-for-azure-netapp-files"></a>Preguntas frecuentes sobre la administración de la capacidad para Azure NetApp Files

En este artículo se responden las preguntas frecuentes (P+F) sobre la administración de la capacidad de Azure NetApp Files.

## <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>¿Cómo puedo supervisar el uso de grupo de capacidad y volumen de Azure NetApp Files? 

Azure NetApp Files proporciona métricas de uso de grupo de capacidad y volumen. También puede usar Azure Monitor para supervisar el uso de Azure NetApp Files. Consulte [Métricas de Azure NetApp Files](azure-netapp-files-metrics.md) para más información. 

## <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>¿Cómo puedo determinar si un directorio está llegando al tamaño límite?

Puede usar el comando `stat` desde un cliente para ver si un directorio está llegando al [límite de tamaño máximo](azure-netapp-files-resource-limits.md#resource-limits) de los metadatos del directorio (320 MB).
Consulte [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md#directory-limit) para el límite y el cálculo. 

## <a name="does-snapshot-space-count-towards-the-usable--provisioned-capacity-of-a-volume"></a>¿Se cuenta el espacio de instantáneas en la capacidad utilizable o aprovisionada de un volumen?

Sí, la [capacidad de instantáneas consumida](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots) cuenta para el espacio aprovisionado del volumen. En caso de que el volumen se llene, considere la posibilidad de tomar las siguientes medidas:

* [Cambie el tamaño del volumen](azure-netapp-files-resize-capacity-pools-or-volumes.md).
* [Quite las instantáneas más antiguas](snapshots-delete.md) para liberar espacio en el volumen de hospedaje. 

## <a name="does-azure-netapp-files-support-auto-grow-for-volumes-or-capacity-pools"></a>¿Azure NetApp Files admite el crecimiento automático en los volúmenes o grupos de capacidad?

No, los volúmenes y los grupos de capacidad de Azure NetApp Files no crecen automáticamente al llenarse. Vea [Modelo de costo de Azure NetApp Files](azure-netapp-files-cost-model.md).   

Puede usar la herramienta admitida por la comunidad [ANFCapacityManager de Logic Apps](https://github.com/ANFTechTeam/ANFCapacityManager) para administrar las reglas de alertas basadas en la capacidad. La herramienta puede aumentar automáticamente los tamaños de los volúmenes para evitar que se queden sin espacio.

## <a name="does-the-destination-volume-of-a-replication-count-towards-hard-volume-quota"></a>¿El volumen de destino de una replicación cuenta para la cuota de volumen estricta?  

No, el volumen de destino de una replicación no cuenta para la cuota de volumen estricta.

## <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>¿Puedo administrar Azure NetApp Files mediante Explorador de Azure Storage?

No. Explorador de Azure Storage no es compatible con Azure NetApp Files.

## <a name="next-steps"></a>Pasos siguientes  

- [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Preguntas frecuentes sobre redes](faq-networking.md)
- [Preguntas más frecuentes de seguridad](faq-security.md)
- [Preguntas más frecuentes sobre rendimiento](faq-performance.md)
- [Preguntas más frecuentes sobre NFS](faq-nfs.md)
- [Preguntas más frecuentes de SMB](faq-smb.md)
- [Preguntas frecuentes sobre migración y protección de datos](faq-data-migration-protection.md)
- [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](faq-backup.md)
- [Preguntas frecuentes de la resistencia de la aplicación](faq-application-resilience.md)
- [Preguntas más frecuentes sobre la integración](faq-integration.md)
