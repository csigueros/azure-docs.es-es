---
title: Preguntas frecuentes sobre la seguridad para Azure NetApp Files | Microsoft Docs
description: Aquí se responden las preguntas frecuentes (P+F) sobre la seguridad de Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.custom: references_regions
ms.openlocfilehash: d1794516cfd6c97411d27122749a4260bcbe8358
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270109"
---
# <a name="security-faqs-for-azure-netapp-files"></a>Preguntas frecuentes sobre la seguridad para Azure NetApp Files

En este artículo se responden las preguntas frecuentes (P+F) sobre la seguridad de Azure NetApp Files.

## <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>¿Se puede cifrar el tráfico de red entre la máquina virtual de Azure y el almacenamiento?

El tráfico de datos de Azure NetApp Files es seguro por diseño, porque no proporciona ningún punto de conexión público y el tráfico de datos permanece dentro de la red virtual propiedad del cliente. Los datos en tránsito no se cifran de forma predeterminada. Sin embargo, el tráfico de datos desde una máquina virtual de Azure (que ejecuta un cliente SMB o NFS) a Azure NetApp Files es tan seguro como cualquier otro tráfico de máquina virtual a máquina virtual de Azure. 

El protocolo NFSv3 no admite cifrado, por lo que estos datos en tránsito no se pueden cifrar. Sin embargo, existe la opción de habilitar el cifrado de datos en tránsito de NFSv4.1 y SMB3. El tráfico de datos entre los clientes de NFSv 4.1 y los volúmenes de Azure NetApp Files se puede cifrar mediante Kerberos con el cifrado AES-256. Para más información, consulte [Configuración del cifrado Kerberos de NFSv4.1 para Azure NetApp Files](configure-kerberos-encryption.md). El tráfico de datos entre los clientes de SMB3 y los volúmenes de Azure NetApp Files se puede cifrar mediante el algoritmo AES-CCM en SMB 3.0 y el algoritmo AES-GCM en conexiones SMB 3.1.1. Para más información, consulte [Creación de un volumen SMB para Azure NetApp Files](azure-netapp-files-create-volumes-smb.md). 

## <a name="can-the-storage-be-encrypted-at-rest"></a>¿Se puede cifrar el almacenamiento en reposo?

Todos los volúmenes de Azure NetApp Files se cifran mediante el estándar FIPS 140-2. El servicio Azure NetApp Files administra todas las claves. 

## <a name="is-azure-netapp-files-cross-region-replication-traffic-encrypted"></a>¿El tráfico de replicación entre regiones de Azure NetApp Files está cifrado?

La replicación entre regiones de Azure NetApp Files usa cifrado TLS 1.2 AES-256 GCM para cifrar todos los datos transferidos entre el volumen de origen y el de destino. Este cifrado es adicional al [cifrado MACSec de Azure](../security/fundamentals/encryption-overview.md) que está activado de manera predeterminada para todo el tráfico de Azure, incluida la replicación entre regiones de Azure NetApp Files. 

## <a name="how-are-encryption-keys-managed"></a>¿Cómo se administran las claves de cifrado? 

La administración de claves para Azure NetApp Files se controla mediante el servicio. Se genera una clave de cifrado de datos XTS-AES-256 única para cada volumen. Una jerarquía de claves de cifrado se usa para cifrar y proteger todas las claves de volumen. Estas claves de cifrado nunca se muestran ni se notifican en un formato sin cifrar. Las claves de cifrado se eliminan inmediatamente cuando se suprime un volumen.

La compatibilidad con las claves administradas por el cliente (Bring Your Own Key) mediante Azure Dedicated HSM está disponible de manera controlada en las regiones Este de EE. UU., Centro-sur de EE. UU., Oeste de EE. UU 2. y Centro y US Gov Virginia. Puede solicitar acceso en [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . Las solicitudes se aprueban a medida que la capacidad va estando disponible.

## <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>¿Puedo configurar las reglas de directivas de exportación NFS para controlar el acceso al destino de montaje del servicio Azure NetApp Files?

Sí, puede configurar hasta cinco reglas en una sola directiva de exportación NFS.

## <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>¿Puedo usar RBAC de Azure con Azure NetApp Files?

Sí, Azure NetApp Files admite las características de RBAC de Azure. Junto con los roles de Azure integrados, puede [crear roles personalizados](../role-based-access-control/custom-roles.md) para Azure NetApp Files. 

Para obtener una lista completa de los permisos de Azure NetApp Files, consulte las operaciones del proveedor de recursos de Azure para [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp).

## <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>¿Se admiten los registros de actividad de Azure en Azure NetApp Files?

Azure NetApp Files es un servicio nativo de Azure. Todas las API PUT, POST y DELETE se registran en Azure NetApp Files. Por ejemplo, los registros muestran actividades como quién creó la instantánea, quién modificó el volumen, etc.

Para obtener una lista completa de las operaciones de API, consulte [API REST de Azure NetApp Files](/rest/api/netapp/).

## <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>¿Puedo usar directivas de Azure con Azure NetApp Files?

Sí, puede crear [directivas de Azure personalizadas](../governance/policy/tutorials/create-custom-policy-definition.md). 

Sin embargo, no puede crear directivas de Azure (directivas de nomenclatura personalizadas) en la interfaz de Azure NetApp Files. Consulte [Instrucciones para el planeamiento de red de Azure NetApp Files](azure-netapp-files-network-topologies.md#considerations).

## <a name="when-i-delete-an-azure-netapp-files-volume-is-the-data-deleted-safely"></a>Cuando elimino un volumen de Azure NetApp Files, ¿los datos se eliminan de forma segura? 

La eliminación de un volumen de Azure NetApp Files se realiza mediante programación con efecto inmediato. La operación de eliminación incluye la eliminación de las claves usadas para cifrar datos en reposo. No hay ningún escenario que permita la recuperación de un volumen eliminado una vez que la operación de eliminación se ejecuta correctamente (mediante interfaces como Azure Portal y la API).


## <a name="next-steps"></a>Pasos siguientes  

- [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Preguntas frecuentes sobre redes](faq-networking.md)
- [Preguntas más frecuentes sobre rendimiento](faq-performance.md)
- [Preguntas más frecuentes sobre NFS](faq-nfs.md)
- [Preguntas más frecuentes de SMB](faq-smb.md)
- [Preguntas más frecuentes sobre la administración de la capacidad](faq-capacity-management.md)
- [Preguntas frecuentes sobre migración y protección de datos](faq-data-migration-protection.md)
- [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](faq-backup.md)
- [Preguntas frecuentes de la resistencia de la aplicación](faq-application-resilience.md)
- [Preguntas más frecuentes sobre la integración](faq-integration.md)