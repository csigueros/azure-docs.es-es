---
title: Configuración de permisos de Unix y del modo cambio de propiedad para los volúmenes de protocolo dual y NFS de Azure NetApp Files | Microsoft Docs
description: Describe cómo establecer los permisos de Unix y las opciones del modo de cambio de propiedad para los volúmenes de protocolo dual y NFS de Azure NetApp Files.
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
ms.date: 08/06/2021
ms.author: b-juche
ms.openlocfilehash: bfb794b1344dbc4723ca699f572b487b54d60c77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781319"
---
# <a name="configure-unix-permissions-and-change-ownership-mode-for-nfs-and-dual-protocol-volumes"></a>Configuración de permisos de Unix y del modo de cambio de propiedad para los volúmenes de protocolo dual y NFS

Para los volúmenes de NFS o volúmenes de protocolo dual de Azure NetApp Files con el estilo de seguridad `Unix`, tiene la opción de establecer los **permisos de Unix** y las opciones del **modo de cambio de propiedad** ( **`Chown Mode`** ). Puede especificar esta configuración durante la creación del volumen o después de la creación del volumen. 

## <a name="unix-permissions"></a>Permisos de Unix   

La funcionalidad de **permisos de Unix** de Azure NetApp Files permite especificar permisos de cambio para la ruta de acceso de montaje. El valor no se aplica a los archivos de la ruta de acceso de montaje.   

La configuración de permisos de Unix se establece en `0770` de forma predeterminada. Este valor concede permisos de lectura, escritura y ejecución al propietario y al grupo, pero no se conceden permisos a otros usuarios. 

 Puede especificar un valor personalizado de permisos de Unix (por ejemplo, `0755`) para conceder el permiso deseado al propietario, grupo u otros usuarios.  

## <a name="change-ownership-mode"></a>Cambiar el modo de propiedad   

La funcionalidad modo de cambio de propiedad ( **`Chown Mode`** ) permite establecer las funcionalidades de administración de propiedad de archivos y directorios.  Puede especificar o modificar la configuración en la directiva de exportación de un volumen. Hay dos opciones disponibles para **`Chown Mode`** :   

* `Restricted` (valor predeterminado): solo el usuario raíz puede cambiar la propiedad de los archivos y directorios.
* `Unrestricted`: los usuarios no raíz pueden cambiar la propiedad de los archivos y directorios que poseen.

## <a name="considerations"></a>Consideraciones  

* Los permisos de Unix que especifique solo se aplican al punto de montaje del volumen (directorio raíz).  
* No puede modificar los permisos de Unix en volúmenes de origen o destino que se encuentran en una configuración de replicación entre regiones. 

## <a name="steps"></a>Pasos

1. Las características de permisos de Unix y de modo de cambio de propiedad están actualmente en versión preliminar. Antes de usar estas características por primera vez, debe registrarlas:   

    1. Registre la característica de **permisos de Unix**:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions
        ```

    2.  Registre la característica de **modo de cambio de propiedad**:    

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```

    3. Compruebe el estado del registro de la característica:    

        > [!NOTE]
        > **RegistrationState** puede estar en el estado `Registering` hasta 60 minutos antes de cambiar a `Registered`. Espere hasta que el estado sea `Registered` antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions  
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```
        
    También puede usar los comandos de la [CLI de Azure](/cli/azure/feature) `az feature register` y `az feature show` para registrar la característica y mostrar el estado del registro. 

2. Puede especificar la configuración de los **permisos de Unix** y del modo de cambio de propiedad ( **`Chown Mode`** ) en la pestaña **Protocolo** al [crear un volumen de NFS](azure-netapp-files-create-volumes.md) o [crear un volumen de protocolo dual](create-volumes-dual-protocol.md). 

    En el ejemplo siguiente se muestra la pantalla Crear un volumen para un volumen NFS. 

    ![Capturas de pantalla que muestran la pantalla Crear un volumen para NFS.](../media/azure-netapp-files/unix-permissions-create-nfs-volume.png)

3. En el caso de los volúmenes de NFS o de protocolo dual existentes, puede establecer o modificar los **permisos de Unix** y el **modo de cambiar de propiedad** de la siguiente manera:  

    1. Para modificar los permisos de Unix, haga clic con el botón derecho en el **volumen** y seleccione **Editar**. En la ventana Editar que aparece, especifique un valor para **Permisos de Unix**.  
        ![Capturas de pantalla que muestran la pantalla Editar para permisos de Unix.](../media/azure-netapp-files/unix-permissions-edit.png)

    2. Para modificar el modo de cambio de propiedad, haga clic en el **volumen**, haga clic en **Exportar directiva** y, a continuación, modifique la configuración **`Chown Mode`** .  
        ![Capturas de pantalla que muestran la pantalla Exportar directiva.](../media/azure-netapp-files/chown-mode-edit.png)

## <a name="next-steps"></a>Pasos siguientes  

* [Creación de un volumen de NFS para Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Creación de un volumen de dos protocolos para Azure NetApp Files](create-volumes-dual-protocol.md) 
* [configuración de directiva de exportación](azure-netapp-files-configure-export-policy.md)
