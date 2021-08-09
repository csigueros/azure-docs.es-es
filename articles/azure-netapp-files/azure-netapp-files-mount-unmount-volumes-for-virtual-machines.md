---
title: Montaje de volúmenes de Azure NetApp Files para máquinas virtuales
description: Descubre cómo montar o desmontar un volumen para máquinas virtuales Windows o máquinas virtuales Linux en Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 05/17/2021
ms.openlocfilehash: 2da352a10f5ff9bacdfb28de8752e5ffefa3fd82
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110068201"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montaje o desmontaje de un volumen para máquinas virtuales Linux 

Puede montar o desmontar un volumen para máquinas virtuales Windows o Linux según sea necesario.  Las instrucciones de montaje para las máquinas virtuales Linux están disponibles en Azure NetApp Files.  

## <a name="requirements"></a>Requisitos 

* Debe tener al menos una directiva de exportación para poder tener acceso a un volumen de NFS.
* Para montar un volumen de NFS correctamente, asegúrese de que los siguientes puertos NFS estén abiertos entre el cliente y los volúmenes NFS:
    * 111 TCP/UDP = `RPCBIND/Portmapper`
    * 635 TCP/UDP = `mountd`
    * 2049 TCP/UDP = `nfs`
    * 4045 TCP/UDP = `nlockmgr` (solo NFSv3)
    * 4046 TCP/UDP = `status` (solo NFSv3)

## <a name="steps"></a>Pasos

1. Haga clic en la hoja **Volúmenes** y seleccione el volumen para el que desea realizar el montaje. 
2. Haga clic en **Instrucciones de montaje** en el volumen seleccionado y siga las instrucciones para montar el volumen. 

    ![Instrucciones de montaje NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instrucciones de montaje SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Si monta un volumen NFS, asegúrese de usar la opción `vers` en el comando `mount` para especificar la versión de protocolo NFS correspondiente al volumen que desea montar. 
    * Si usa NFSv4.1, utilice el siguiente comando para montar el sistema de archivos: `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > Si usa NFSv4.1 y su caso de uso conlleva el aprovechamiento de máquinas virtuales con los mismos nombres de host (por ejemplo, en una prueba de recuperación ante desastres), consulte la sección sobre la [configuración de dos máquinas virtuales con el mismo nombre de host para acceder a volúmenes de NFSv4.1](configure-nfs-clients.md#configure-two-vms-with-the-same-hostname-to-access-nfsv41-volumes).

3. Si desea tener un volumen NFS montado automáticamente cuando se inicia o se reinicia una máquina virtual de Azure, agregue una entrada al archivo `/etc/fstab` del host. 

    Por ejemplo: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` es la dirección IP del volumen de Azure NetApp Files que se encuentra en la hoja de propiedades de volumen.
    * `$FILEPATH` es la ruta de acceso de exportación del volumen de Azure NetApp Files.
    * `$MOUNTPOINT` es el directorio creado en el host de Linux que se utiliza para montar la exportación de NFS.

4. Si desea montar el volumen en Windows con NFS:

    a. Monte primero el volumen en una máquina virtual Unix o Linux.  
    b. Ejecute el comando `chmod 777` o `chmod 775` en el volumen.  
    c. Monte el volumen con el cliente NFS en Windows.
    
5. Si desea montar un volumen Kerberos de NFS, consulte [Configuración del cifrado Kerberos NFSv4.1](configure-kerberos-encryption.md) para obtener más detalles. 

## <a name="next-steps"></a>Pasos siguientes

* [Configuración del dominio predeterminado de NFS, versión 4.1, para Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Preguntas más frecuentes sobre NFS](./azure-netapp-files-faqs.md#nfs-faqs)
* [Introducción a Network File System](/windows-server/storage/nfs/nfs-overview)
* [Montaje de un volumen Kerberos de NFS](configure-kerberos-encryption.md#kerberos_mount)
* [Configuración de dos máquinas virtuales con el mismo nombre de host para que accedan a volúmenes de NFSv4.1](configure-nfs-clients.md#configure-two-vms-with-the-same-hostname-to-access-nfsv41-volumes) 
