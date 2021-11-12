---
title: Preguntas más frecuentes sobre SMB para Azure NetApp Files | Microsoft Docs
description: Respuestas a las preguntas más frecuentes (P+F) sobre el protocolo SMB de Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 7b1c6b1e3e9814ed83c92ada2388ff54f5b3829c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270108"
---
# <a name="smb-faqs-for-azure-netapp-files"></a>P+F de SMB para Azure NetApp Files

En este artículo se responden las preguntas más frecuentes (P+F) sobre el protocolo SMB de Azure NetApp Files.

## <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>¿Qué versiones de SMB son compatibles con Azure NetApp Files?

Azure NetApp Files admite SMB 2.1 y SMB 3.1 (que incluye compatibilidad con SMB 3.0). 

## <a name="is-an-active-directory-connection-required-for-smb-access"></a>¿Es necesaria una conexión de Active Directory para el acceso SMB? 

Sí, tiene que crear una conexión de Active Directory antes de implementar un volumen de SMB. Para que la conexión tenga éxito la subred delegada de Azure NetApp Files tiene que tener acceso a los controladores de dominio especificados.  Consulte [Crear un volumen de SMB](./azure-netapp-files-create-volumes-smb.md) para más información. 

## <a name="how-many-active-directory-connections-are-supported"></a>¿Cuántas conexiones de Active Directory se admiten?

Solo puede configurar una conexión de Active Directory (AD) por suscripción y por región. Para más información, consulte [Requisitos para las conexiones de Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections). 

Sin embargo, puede asignar varias cuentas de NetApp que se encuentran en la misma suscripción y la misma región a un servidor de AD común creado en una de las cuentas de NetApp. Consulte [Asignación de varias cuentas de NetApp de la misma suscripción y región a una conexión de AD](create-active-directory-connections.md#shared_ad). 

## <a name="does-azure-netapp-files-support-azure-active-directory"></a>¿ Azure NetApp Files admite Azure Active Directory? 

Se admite tanto [Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) como [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Puede usar controladores de dominio de Active Directory existentes con Azure NetApp Files. Los controladores de dominio pueden residir en Azure como máquinas virtuales o en el entorno local mediante ExpressRoute o VPN S2S. En este momento, Azure NetApp Files no admite la unión a AD para [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/).

Si usa Azure NetApp Files con Azure Active Directory Domain Services, la ruta de acceso de la unidad organizativa es `OU=AADDC Computers` cuando configura Active Directory para su cuenta de NetApp.

## <a name="what-versions-of-windows-server-active-directory-are-supported"></a>¿Qué versiones de Windows Server Active Directory se admiten?

Azure NetApp Files admite las versiones 2008r2SP1-2019 de Windows Server de Active Directory Domain Services.

## <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Tengo problemas para conectarme al recurso compartido de SMB. ¿Cuál debo hacer?

Como procedimiento recomendado, establezca la tolerancia máxima para la sincronización del reloj del equipo en cinco minutos. Para más información, consulte [Tolerancia máxima para la sincronización del reloj del equipo](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)). 

## <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>¿Puedo administrar `SMB Shares`, `Sessions` y `Open Files` a través de la consola de administración de equipos (MMC)?

Actualmente no se admite la administración de `SMB Shares`, `Sessions` y `Open Files` a través de la consola de administración de equipos (MMC).

## <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>¿Cómo puedo obtener la dirección IP de un volumen SMB a través del portal?

Use el vínculo de la **vista JSON** en el panel Información general del volumen y busque el identificador **startIP** en **properties** -> **mountTargets**.

## <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>¿Puede un recurso compartido de SMB de Azure NetApp Files actuar como raíz del espacio de nombres DFS (DFS-N)?

No. Sin embargo, los recursos compartidos de SMB de Azure NetApp Files pueden actuar como destino de la carpeta del espacio de nombres DFS (DFS-N).   
Para usar un recurso compartido de SMB de Azure NetApp Files como destino de la carpeta de DFS-N, proporcione la ruta de acceso de montaje de convención de nomenclatura universal (UNC) del recurso compartido de SMB de Azure NetApp Files mediante el procedimiento para [agregar destino de la carpeta DFS](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target).  

## <a name="can-the-smb-share-permissions-be-changed"></a>¿Se pueden cambiar los permisos del recurso compartido de SMB?   

No, los permisos del recurso compartido no se pueden cambiar. Pero sí se pueden cambiar los permisos NTFS del volumen `root` mediante el procedimiento para [permisos de carpetas y archivos NTFS](azure-netapp-files-create-volumes-smb.md#ntfs-file-and-folder-permissions). 


## <a name="next-steps"></a>Pasos siguientes  

- [Preguntas más frecuentes sobre el rendimiento de SMB para Azure NetApp Files](azure-netapp-files-smb-performance.md)
- [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Preguntas frecuentes sobre redes](faq-networking.md)
- [Preguntas más frecuentes de seguridad](faq-security.md)
- [Preguntas más frecuentes sobre rendimiento](faq-performance.md)
- [Preguntas más frecuentes sobre NFS](faq-nfs.md)
- [Preguntas más frecuentes sobre la administración de la capacidad](faq-capacity-management.md)
- [Preguntas frecuentes sobre migración y protección de datos](faq-data-migration-protection.md)
- [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](faq-backup.md)
- [Preguntas frecuentes de la resistencia de la aplicación](faq-application-resilience.md)
- [Preguntas más frecuentes sobre la integración](faq-integration.md)
