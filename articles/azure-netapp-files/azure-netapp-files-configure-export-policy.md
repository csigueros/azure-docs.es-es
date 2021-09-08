---
title: 'Configuración de la directiva de exportación para volúmenes NFS o de protocolo dual de Azure NetApp Files: Azure NetApp Files'
description: Describe cómo configurar la directiva de exportación para controlar el acceso a un volumen NFS mediante Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 08/06/2021
ms.openlocfilehash: f930c52c4b913e9411c6f619231fb50d0bb6cc5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733397"
---
# <a name="configure-export-policy-for-nfs-or-dual-protocol-volumes"></a>Configuración de la directiva de exportación para volúmenes NFS o de protocolo dual

Puede configurar la directiva de exportación para controlar el acceso a un volumen de Azure NetApp Files que usa el protocolo NFS (NFSv3 y NFSv4.1) o el protocolo dual (NFSv3 y SMB, o NFSv4.1 y SMB). 

Puede crear hasta cinco reglas en la directiva de exportación.

## <a name="configure-the-policy"></a>Configuración de la directiva 

1.  En la página **Volúmenes**, seleccione el volumen para el que desea configurar la directiva de exportación y, a continuación, seleccione **Directiva de exportación**. También puede configurar la directiva de exportación durante la creación del volumen.

2.  Especifique la siguiente información para crear una regla de directiva de exportación:   
    * **Índice**: Especifique el número de índice para la regla.  
      
      Una directiva de exportación puede constar de hasta cinco reglas. Las reglas se evalúan según el orden en la lista de números de índice. Primero se evalúan las reglas con números de índice inferior. Por ejemplo, la regla con el número de índice 1 se evalúa antes que la que tenga el número de índice 2. 

    * **Clientes permitidos**: Especifique el valor en uno de los siguientes formatos:  
      * Dirección IPv4. Ejemplo: `10.1.12.24`
      * Dirección IPv4 con una máscara de subred expresada como un número de bits. Ejemplo: `10.1.12.10/4`
      * Direcciones IP separadas por comas. Puede especificar varias direcciones IP de host en una sola regla separándolas con comas. El límite de longitud es de 4096 caracteres. Ejemplo: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Acceso**: Seleccione uno de los tipos de accesos siguientes:  
      * Sin acceso 
      * Lectura y escritura
      * Solo lectura

    * **Solo lectura** y **Lectura y escritura**: Si usa el cifrado Kerberos con NFSv 4.1, siga las instrucciones de [Configuración del cifrado Kerberos con NFSv4.1](configure-kerberos-encryption.md).  Para ver el impacto en el rendimiento de Kerberos, consulte [Impacto en el rendimiento de Kerberos en los volúmenes NFSv4.1](performance-impact-kerberos.md). 

      ![Opciones de seguridad de Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Acceso raíz**: Especifique si la cuenta `root` puede acceder al volumen.  De forma predeterminada, el acceso a la raíz se establece en **On** y la cuenta `root` tiene acceso al volumen.

      ![Directiva de exportación](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

    * **Modo de cambio de propiedad:** : modifique el modo de cambio de propiedad según sea necesario para establecer las funcionalidades de administración de la propiedad de archivos y directorios.  Hay dos opciones de disponibles:   

      * `Restricted` (valor predeterminado): solo el usuario raíz puede cambiar la propiedad de los archivos y directorios.
      * `Unrestricted`: los usuarios no raíz pueden cambiar la propiedad de los archivos y directorios que poseen.  

        Se aplican requisitos de registro y consideraciones para establecer el valor de **`Chown Mode`** . Siga las instrucciones que encontrará en [Configuración de permisos de Unix y del modo de cambio de propiedad para los volúmenes de protocolo dual y NFS](configure-unix-permissions-change-ownership-mode.md).  

      ![Captura de pantalla que muestra la opción del modo de cambio de propiedad.](../media/azure-netapp-files/chown-mode-export-policy.png) 

## <a name="next-steps"></a>Pasos siguientes 
* [Montaje o desmontaje de un volumen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configuración de permisos de Unix y del modo de cambio de propiedad para los volúmenes de protocolo dual y NFS](configure-unix-permissions-change-ownership-mode.md) 
* [Administración de instantáneas](azure-netapp-files-manage-snapshots.md)
