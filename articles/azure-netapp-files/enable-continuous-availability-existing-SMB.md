---
title: Habilitación de la disponibilidad continua en volúmenes SMB de Azure NetApp Files existentes | Microsoft Docs
description: Se describe cómo habilitar la disponibilidad continua de SMB en el volumen SMB de Azure NetApp Files existente.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/15/2021
ms.author: b-juche
ms.openlocfilehash: 8dace313ed9296dd514fb059e8ed681a6276af0f
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074539"
---
# <a name="enable-continuous-availability-on-existing-smb-volumes"></a>Habilitación de la disponibilidad continua en volúmenes de SMB existentes

Puede habilitar la característica de disponibilidad continua (CA) de SMB al [crear un nuevo volumen SMB](azure-netapp-files-create-volumes-smb.md#continuous-availability). También puede habilitar la CA de SMB en un volumen SMB existente. En este artículo se muestra cómo hacerlo.

## <a name="considerations"></a>Consideraciones

* La opción [**Ocultar ruta de acceso de instantánea**](snapshots-edit-hide-path.md) actualmente no tiene ningún efecto en los volúmenes SMB habilitados para CA.  

* El directorio `~snapshot` (que se puede usar para recorrer en otros volúmenes SMB) no es visible para los volúmenes SMB habilitados para CA. Todavía puede escribir manualmente `~snapshot\<snapshotName>` para acceder a la instantánea.

## <a name="steps"></a>Pasos

1. Asegúrese de que ha registrado la característica de [recursos compartidos con disponibilidad continua de SMB](https://aka.ms/anfsmbcasharespreviewsignup).  

    Debe habilitar la disponibilidad continua solo para los [contenedores de perfil de usuario de FSLogix](../virtual-desktop/create-fslogix-profile-container.md) y SQL Server. *No* se admite el uso de recursos compartidos de disponibilidad continua de SMB para cargas de trabajo que no son contenedores de perfil de usuario de SQL Server y FSLogix. Esta característica se admite actualmente en Windows SQL Server. Linux SQL Server no se admite actualmente. Si usa una cuenta que no sea de administrador (dominio) para instalar SQL Server, asegúrese de que la cuenta tiene asignado el privilegio de seguridad necesario. Si la cuenta de dominio no tiene el privilegio de seguridad necesario (`SeSecurityPrivilege`) y el privilegio no se puede establecer en el nivel de dominio, puede conceder el privilegio a la cuenta mediante el campo **Security privilege users** (Usuarios con privilegios de seguridad) de conexiones de Active Directory. Consulte la sección [Creación de una conexión de Active Directory](create-active-directory-connections.md#create-an-active-directory-connection).
            
3. Haga clic en el volumen SMB en el que desea tener habilitada la CA de SMB. A continuación, haga clic en **Editar**.  
4. En la ventana Editar que aparece, seleccione la casilla **Habilitar disponibilidad continua**.   
    ![Instantánea que muestra la opción para habilitar la disponibilidad continua.](../media/azure-netapp-files/enable-continuous-availability.png)

4. Reinicie los sistemas Windows que se conectan al recurso compartido SMB existente.   

    > [!NOTE]
    > La selección de la opción **Habilitar disponibilidad continua** por sí sola no hace que las sesiones SMB existentes estén disponibles continuamente de forma automática. Después de seleccionar la opción, asegúrese de reiniciar el servidor para que el cambio surta efecto.  

5. Use el siguiente comando para comprobar que la entidad de certificación está habilitada y se usa en el sistema que monta el volumen:

    ```powershell-interactive
    get-smbconnection | select -Property servername,ContinuouslyAvailable
    ```
 
    Es posible que tenga que instalar una versión más reciente de PowerShell. 

    Si conoce el nombre del servidor, puede usar el parámetro `-ServerName` con el comando. Consulte los detalles del comando de PowerShell [Get-SmbConnection](/powershell/module/smbshare/get-smbconnection?view=windowsserver2019-ps&preserve-view=true).

## <a name="next-steps"></a>Pasos siguientes  

* [Cree un volumen SMB para Azure NetApp Files](azure-netapp-files-create-volumes-smb.md)
