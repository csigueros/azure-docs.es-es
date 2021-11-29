---
title: Configuración del dominio de NFSv4.1 para Azure NetApp Files | Microsoft Docs
description: Aquí se describe cómo configurar el dominio de NFS, versión 4.1, para usar NFSv4.1 con Azure NetApp Files.
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
ms.date: 11/11/2021
ms.author: b-juche
ms.openlocfilehash: ebf6d8e51e3e0c46ae8bd4086afdb4cb28700d6e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714283"
---
# <a name="configure-nfsv41-domain-for-azure-netapp-files"></a>Configuración del dominio de NFS, versión 4.1, para Azure NetApp Files

NFSv4 introduce el concepto de dominio de autenticación. Actualmente, Azure NetApp Files admite la asignación de usuarios solo de raíz desde el servicio al cliente NFS. Para usar la funcionalidad de NFSv4.1 con Azure NetApp Files, debe actualizar el cliente NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Comportamiento predeterminado de asignación de usuarios o grupos

La asignación de raíz se establece de manera predeterminada en el usuario `nobody` porque el dominio NFSv4 está establecido predeterminadamente en `localdomain`. Al montar un volumen de NFSv4.1 de Azure NetApp Files como raíz, verá los permisos de archivo de la siguiente manera:  

![Comportamiento predeterminado de asignación de usuarios o grupos para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Como se muestra en el ejemplo anterior, el usuario para `file1` debe ser `root`, pero se asigna a `nobody` de forma predeterminada.  En este artículo se muestra cómo establecer el usuario `file1` en `root` al cambiar la configuración `idmap Domain` a `defaultv4iddomain.com`.  

## <a name="configure-nfsv41-domain"></a>Configuración del dominio de NFS, versión 4.1  

1. Edite el archivo `/etc/idmapd.conf` en el cliente NFS.   
    Quite la marca de comentario de la línea `#Domain` (es decir, quite `#` de la línea) y cambie el valor `localdomain` tal como se indica a continuación:

    * Si el volumen no está habilitado para LDAP, establezca `Domain = defaultv4iddomain.com`.
    * Si el volumen está [habilitado para LDAP](configure-ldap-extended-groups.md), establezca `Domain` en el dominio que está configurado en la conexión de Active Directory de la cuenta de NetApp.
        Por ejemplo, si `contoso.com` es el dominio configurado en la cuenta de NetApp, establezca `Domain = contoso.com`.

    En los ejemplos siguientes se muestra la configuración inicial de `/etc/idmapd.conf` antes de los cambios:

    ```
    [General]
    Verbosity = O 
    Pipefs—Directory = /run/rpc_pipefs 
    # set your own domain here, if it differs from FQDN minus hostname 
    # Domain = localdomain 
     
    [Mapping] 
    Nobody-User = nobody 
    Nobody-Group = nogroup 
    ```

    El siguiente ejemplo muestra la configuración actualizada de volúmenes que *no son de LDAP* para NFSv4.1:

    ```
    [General]
    Verbosity = O 
    Pipefs—Directory = /run/rpc_pipefs 
    # set your own domain here, if it differs from FQDN minus hostname 
    Domain = defaultv4iddomain.com 
 
    [Mapping] 
    Nobody-User = nobody 
    Nobody-Group = nogroup 
    ```

    El siguiente ejemplo muestra la configuración actualizada de volúmenes que *tienen LDAP habilitado* para NFSv4.1. En este ejemplo, `contoso.com` es el dominio configurado en la cuenta de NetApp:

    ```
    [General]
    Verbosity = O 
    Pipefs—Directory = /run/rpc_pipefs 
    # set your own domain here, if it differs from FQDN minus hostname 
    Domain = contoso.com
    
    [Mapping] 
    Nobody-User = nobody 
    Nobody-Group = nogroup 
    ```

2. Desmonte los volúmenes NFS montados actualmente.
3. Actualice el archivo `/etc/idmapd.conf`.
4. Reinicie el servicio `rpcbind` en el host (`service rpcbind restart`) o simplemente reinicie el host.
5. Monte los volúmenes NFS según sea necesario.   

    Consulte [Montaje o desmontaje de un volumen para máquinas virtuales Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

El siguiente ejemplo muestra el cambio de usuario o grupo resultante: 

![Captura de pantalla que muestra un ejemplo del cambio de usuario o grupo resultante.](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Como se muestra en el ejemplo, el usuario o grupo ahora ha cambiado de `nobody` a `root`.

## <a name="behavior-of-other-non-root-users-and-groups"></a>Comportamiento de otros usuarios y grupos (no raíz)

Azure NetApp Files admite usuarios locales (usuarios creados localmente en un host) que tienen permisos asociados a archivos o carpetas en volúmenes NFSv4.1. Sin embargo, el servicio no admite actualmente la asignación de usuarios o grupos en varios nodos. Por lo tanto, los usuarios creados en un host no se asignan de forma predeterminada a los usuarios creados en otro host. 

En el ejemplo siguiente, `Host1` tiene tres cuentas de usuario de prueba existentes (`testuser01`, `testuser02` y `testuser03`): 

![Captura de pantalla que muestra que Host1 tiene tres cuentas de usuario de prueba existentes.](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

En `Host2`, observe que las cuentas de usuario de prueba no se han creado, pero el mismo volumen está montado en ambos hosts:

![Configuración resultante para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Paso siguiente 

* [Montaje o desmontaje de un volumen para máquinas virtuales Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configuración de ADDS LDAP con grupos extendidos para el acceso a volúmenes NFS](configure-ldap-extended-groups.md)

