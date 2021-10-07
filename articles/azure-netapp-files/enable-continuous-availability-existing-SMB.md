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
ms.date: 09/28/2021
ms.author: b-juche
ms.openlocfilehash: 705cc540097ea41bc0039336d7cc9fdc025dd247
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218391"
---
# <a name="enable-continuous-availability-on-existing-smb-volumes"></a>Habilitación de la disponibilidad continua en volúmenes de SMB existentes

Puede habilitar la característica de disponibilidad continua (CA) de SMB al [crear un nuevo volumen SMB](azure-netapp-files-create-volumes-smb.md#continuous-availability). También puede habilitar la CA de SMB en un volumen SMB existente. En este artículo se muestra cómo hacerlo.

## <a name="considerations"></a>Consideraciones

* La opción [**Ocultar ruta de acceso de instantánea**](snapshots-edit-hide-path.md) actualmente no tiene ningún efecto en los volúmenes SMB habilitados para CA.  

* El directorio `~snapshot` (que se puede usar para recorrer en otros volúmenes SMB) no es visible para los volúmenes SMB habilitados para CA. Todavía puede escribir manualmente `~snapshot\<snapshotName>` para acceder a la instantánea.

## <a name="steps"></a>Pasos

1. Asegúrese de que ha registrado la característica de [recursos compartidos con disponibilidad continua de SMB](https://aka.ms/anfsmbcasharespreviewsignup).  
2. Haga clic en el volumen SMB en el que desea tener habilitada la CA de SMB. A continuación, haga clic en **Editar**.  
3. En la ventana Editar que aparece, seleccione la casilla **Habilitar disponibilidad continua**.   
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
