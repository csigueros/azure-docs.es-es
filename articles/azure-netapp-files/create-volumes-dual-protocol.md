---
title: Creación de un volumen de protocolo dual para Azure NetApp Files | Microsoft Docs
description: Describe cómo crear un volumen que usa el protocolo dual (NFSv3 y SMB, o NFSv4.1 y SMB) con compatibilidad con la asignación de usuarios LDAP.
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
ms.openlocfilehash: 33e01466a3e0629af9a691e33eb9161bf8098611
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751427"
---
# <a name="create-a-dual-protocol-volume-for-azure-netapp-files"></a>Creación de un volumen de dos protocolos para Azure NetApp Files

Azure NetApp Files admite la creación de volúmenes mediante NFS (NFSv3 o NFSv4.1), SMB3 o un protocolo dual (NFSv3 y SMB, o NFSv4.1 y SMB). En este artículo se muestra cómo crear un volumen que use el protocolo dual con compatibilidad con la asignación de usuarios LDAP. 

Para crear volúmenes NFS, consulte [Creación de un volumen NFS](azure-netapp-files-create-volumes.md). Para crear volúmenes SMB, consulte [Creación de un volumen SMB](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Antes de empezar 

* Debe haber creado ya un grupo de capacidad.  
    Consulte [Configuración de un grupo de capacidad](azure-netapp-files-set-up-capacity-pool.md).   
* Debe haber una subred delegada en Azure NetApp Files.  
    Consulte [Delegación de una subred en Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Consideraciones

* Asegúrese de cumplir los [requisitos para las conexiones de Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Cree una cuenta `pcuser` en Active Directory (AD) y asegúrese de que está habilitada. Esta cuenta servirá como usuario predeterminado. Se usará para asignar usuarios de UNIX para acceder a un volumen de protocolo dual configurado con el estilo de seguridad NTFS. La cuenta `pcuser` solo se usa cuando no hay ningún usuario en AD. Si un usuario tiene una cuenta en AD con los atributos POSIX establecidos, esa cuenta será la que se use para la autenticación y no se asignará a la cuenta `pcuser`. 
* Cree una zona de búsqueda inversa en el servidor DNS y, a continuación, agregue un registro de puntero (PTR) de la máquina host de AD en esa zona de búsqueda inversa. De lo contrario, se producirá un error en la creación del volumen de dos protocolos.
* La opción **Allow local NFS users with LDAP** (Permitir usuarios NFS locales con LDAP) de las conexiones de Active Directory tiene como fin proporcionar acceso ocasional y temporal a los usuarios locales. Cuando esta opción está habilitada, la autenticación de usuario y la búsqueda desde el servidor LDAP dejan de funcionar. Por lo tanto, debe mantener esta opción *deshabilitada* en las conexiones de Active Directory, excepto en el caso de que un usuario local necesite acceder a volúmenes habilitados para LDAP. En ese caso, debe deshabilitar esta opción en cuanto el acceso del usuario local ya no sea necesario para el volumen. Consulte [Habilitación de los usuarios de NFS locales con LDAP para acceder a un volumen de dos protocolos](#allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume) para saber cómo administrar el acceso de los usuarios locales.
* Asegúrese de que el cliente NFS esté actualizado y ejecute las actualizaciones más recientes del sistema operativo.
* Los volúmenes de protocolo doble admiten Active Directory Domain Services (ADDS) y Azure Active Directory Domain Services (AADDS). 
* Los volúmenes de protocolo doble no admiten el uso de LDAP a través de TLS con AADDS. Consulte las [consideraciones de LDAP sobre TLS](configure-ldap-over-tls.md#considerations).
* La versión de NFS usada por un volumen de protocolo dual puede ser es NFSv3 o NFSv4.1. Se aplican las siguientes consideraciones:
    * El protocolo dual no es compatible con los atributos extendidos de ACLS de Windows `set/get` desde clientes de NFS.
    * Los clientes de NFS no pueden cambiar los permisos para el estilo de seguridad de NTFS, y los clientes de Windows no pueden cambiar los permisos de los volúmenes del protocolo dual de estilo UNIX.   

        En la tabla siguiente se describen los estilos de seguridad y sus efectos:  
        
        | Estilo de seguridad    | Clientes que pueden modificar permisos   | Permisos que pueden usar los clientes  | Estilo de seguridad efectivo resultante    | Clientes que pueden tener acceso a archivos     |
        |-  |-  |-  |-  |-  |
        | `Unix`    | NFS   | Bits de modo NFSv3 o NFSv4.1    | UNIX  | NFS y Windows   |
        | `Ntfs`    | Windows   | ACL de NTFS     | NTFS  |NFS y Windows|

    * La dirección en la que se produce la asignación de nombres (Windows a UNIX o UNIX a Windows) depende del protocolo que se usa y del estilo de seguridad que se aplica a un volumen. Un cliente de Windows siempre requiere una asignación de nombres de Windows a UNIX. La posibilidad de que se aplique un usuario para revisar los permisos depende del estilo de seguridad. Por el contrario, un cliente NFS solo necesita usar una asignación de nombres de UNIX a Windows si el estilo de seguridad NTFS está en uso. 

        En la tabla siguiente se describen las asignaciones de nombres y los estilos de seguridad:  
    
        |     Protocolo          |     Estilo de seguridad          |     Dirección de asignación de nombres          |     Permisos aplicados          |
        |-|-|-|-|
        |  SMB  |  `Unix`  |  Windows a UNIX  |  UNIX (bits de modo o ACL NFSv4.x)  |
        |  SMB  |  `Ntfs`  |  Windows a UNIX  |  Listas ACL NTFS (basadas en el identificador de seguridad de Windows que accede al recurso compartido)  |
        |  NFSv3  |  `Unix`  |  Ninguno  |  UNIX (bits de modo o ACL NFSv4.x) <br><br>  Tenga en cuenta que las listas ACL NFSv4.x se pueden aplicar mediante un cliente administrativo NFSv4.x y las respetan los clientes NFSv3.  |
        |  NFS  |  `Ntfs`  |  UNIX a Windows  |  Listas ACL NTFS (basadas en el identificador de seguridad del usuario de Windows asignado)  |

* Si tiene topologías grandes y usa el estilo de seguridad `Unix` con un volumen de protocolo dual o LDAP con grupos extendidos, es posible que Azure NetApp Files no pueda acceder a todos los servidores de las topologías.  Si se produce esta situación, póngase en contacto con su equipo de cuentas para obtener ayuda.  <!-- NFSAAS-15123 --> 
* No es necesario un certificado de CA raíz del servidor para crear un volumen de protocolo dual. Solo es necesario si está habilitado LDAP sobre TLS.

## <a name="create-a-dual-protocol-volume"></a>Creación de un volumen de protocolo dual

1.  Haga clic en la hoja **Volúmenes** desde la hoja Grupos de capacidad. Haga clic en **+ Agregar volumen** para crear un volumen. 

    ![Vaya a Volúmenes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  En la ventana Crear un volumen, haga clic en **Crear** y proporcione la información para los campos siguientes bajo la pestaña Aspectos básicos:   
    * **Nombre del volumen**      
        Especifique el nombre para el volumen que va a crear.   

        Un nombre de volumen debe ser único dentro de cada grupo de capacidad. Debe tener tres caracteres de longitud, como mínimo. El nombre debe comenzar por una letra. Solo puede incluir letras, números, caracteres de subrayado("_") y guiones ("-").  

        No se puede usar `default` ni `bin` como nombre del volumen.

    * **Grupo de capacidad**  
        Especifique el grupo de capacidad en el que desee que el volumen se cree.

    * **Cuota**  
        Especifique la cantidad de almacenamiento lógico que se asigna al volumen.  

        El campo **Cuota disponible** muestra la cantidad de espacio no utilizado en el grupo de capacidad elegido que puede usar para crear un nuevo volumen. El tamaño del volumen nuevo no debe superar la cuota disponible.  

    * **Rendimiento (MiB/S)**    
        Si el volumen se crea en un grupo de capacidad con QoS manual, especifique el rendimiento que desea para el volumen.   

        Si el volumen se crea en un grupo de capacidad con QoS automático, el valor que se muestra en este campo es (rendimiento de cuota x nivel de servicio).   

    * **Red virtual**  
        Especifique la red virtual de Azure desde la que desea tener acceso al volumen.  

        La red virtual que especifique debe tener una subred delegada en Azure NetApp Files. Solo puede tener acceso al servicio Azure NetApp Files desde la misma red virtual o desde una red virtual que se encuentre en la misma ubicación que el volumen mediante el emparejamiento de redes virtuales. También puede acceder al volumen desde la red local mediante ExpressRoute.   

    * **Subred**  
        Especifique la subred que desea usar para el volumen.  
        La red virtual que especifique debe estar delegada en Azure NetApp Files. 
        
        Si no ha delegado una subred, haga clic en **Crear nuevo** en el volumen de creación de un volumen. A continuación, en la página de creación de la subred, especifique la información de la subred y seleccione **Microsoft.NetApp/volumes** para delegar la subred para Azure NetApp Files. En cada red virtual, solo puede delegarse una subred a Azure NetApp Files.   
 
        ![Crear un volumen](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creación de una subred](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Si desea aplicar una directiva de instantáneas existente al volumen, haga clic en **Mostrar la sección avanzada** para expandirla, especifique si quiere ocultar la ruta de acceso de la instantánea y seleccione una directiva de instantáneas en el menú desplegable. 

        Para obtener información sobre cómo crear una directiva de instantáneas, consulte [Administración de directivas de instantánea](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostrar la sección avanzada](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Haga clic en la pestaña **Protocol** (Protocolo) y, después, complete las siguientes acciones:  
    * Seleccione **Dual-protocol** (Protocolo dual) como tipo de protocolo para el volumen.   

    * Seleccione la conexión de **Active Directory** que va a usar.

    * Especifique una **ruta de acceso de volumen** única. Esta ruta de acceso se usa al crear destinos de montaje. Los requisitos de la ruta de acceso son los siguientes:  

        - Debe ser única dentro de cada subred de la región. 
        - Debe comenzar con un carácter alfabético.
        - Solo puede contener letras, números o guiones (`-`). 
        - La longitud no debe superar los 80 caracteres.

    * Especifique las **versiones** que va a usar del protocolo dual: **NFSv4.1 y SMB**, o **NFSv3 y SMB**.

        La característica para usar el protocolo dual **NFSv4.1 y SMB** se encuentra actualmente en versión preliminar. Si es la primera vez que usa esta característica, debe registrarla:  

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```

        Compruebe el estado del registro de la característica: 

        > [!NOTE]
        > **RegistrationState** puede estar en el estado `Registering` hasta 60 minutos antes de cambiar a `Registered`. Espere hasta que el estado sea **Registrado** antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```
        También puede usar los comandos de la [CLI de Azure](/cli/azure/feature) `az feature register` y `az feature show` para registrar la característica y mostrar el estado del registro. 

    * Especifique el **estilo de seguridad** que se vaya a usar: NTFS (valor predeterminado) o UNIX.

    * Si desea habilitar el cifrado del protocolo SMB3 para el volumen de protocolo dual, seleccione **Habilitar cifrado del protocolo SMB3**.   

        Esta característica habilita el cifrado solo para los datos SMB3 en proceso. No cifra los datos NFSv3 en proceso. Los clientes SMB que no usen el cifrado SMB3 no podrán acceder a este volumen. Los datos en reposo se cifrarán al margen de esta configuración. Consulte [Cifrado SMB](azure-netapp-files-smb-performance.md#smb-encryption) para más información. 

        La característica **Cifrado del protocolo SMB3** está actualmente en su versión preliminar. Si es la primera vez que usa esta característica, regístrela antes de usarla: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Compruebe el estado del registro de la característica: 

        > [!NOTE]
        > **RegistrationState** puede estar en el estado `Registering` hasta 60 minutos antes de cambiar a `Registered`. Espere hasta que el estado sea `Registered` antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        También puede usar los comandos de la [CLI de Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` y `az feature show` para registrar la característica y mostrar el estado del registro.  

    * Si seleccionó NFSv4.1 y SMB para las versiones del volumen de protocolo dual, indique si desea habilitar el cifrado **Kerberos** para el volumen.

        Se requieren configuraciones adicionales para Kerberos. Siga las instrucciones de [Configuración del cifrado de Kerberos NFSv4.1](configure-kerberos-encryption.md).

    *  Personalice los **permisos de Unix** según sea necesario para especificar los permisos de cambio de la ruta de acceso de montaje. El valor no se aplica a los archivos de la ruta de acceso de montaje. El valor predeterminado es `0770`. Este valor concede permisos de lectura, escritura y ejecución al propietario y al grupo, pero no se conceden permisos a otros usuarios.     
        Se aplican el requisito y las consideraciones de registro para establecer los **permisos de Unix**. Siga las instrucciones que encontrará en [Configuración de permisos de Unix y cambio del modo de propiedad](configure-unix-permissions-change-ownership-mode.md).  

    * Si lo desea, [configure la directiva de exportación para el volumen](azure-netapp-files-configure-export-policy.md).

    ![Especificación del protocolo dual](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Haga clic en **Revisar y crear** para revisar los detalles del volumen. Después, haga clic en **Crear** para crear el volumen.

    El volumen que creó aparece en la hoja Volúmenes. 
 
    Un volumen hereda los atributos de ubicación, la suscripción y el grupo de recursos de su grupo de capacidad. Para supervisar el estado de la implementación del volumen, puede usar la pestaña Notificaciones.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Habilitación de los usuarios de NFS locales con LDAP para acceder a un volumen de dos protocolos 

La opción **Allow local NFS users with LDAP** (Permitir usuarios NFS locales con LDAP) de las conexiones de Active Directory permite que los usuarios del cliente NFS local que no están presentes en el servidor LDAP de Windows accedan a un volumen de protocolo doble que tenga habilitado LDAP con grupos extendidos. 

> [!NOTE] 
> Antes de habilitar esta opción, debe comprender las [consideraciones](#considerations).   
> La opción **Allow local NFS users with LDAP** (Permitir usuarios de NFS locales con LDAP) forma parte de la característica **LDAP with extended groups** (LDAP con grupos extendidos) y requiere registro. Consulte [Configuración de ADDS LDAP con grupos extendidos para el acceso a volúmenes NFS](configure-ldap-extended-groups.md) para más información.

1. Haga clic en **Conexiones de Active Directory**.  En una conexión de Active Directory existente, haga clic en el menú contextual (los tres puntos `…`) y seleccione **Editar**.  

2. En la ventana **Edit Active Directory settings** (Editar configuración de Active Directory) que aparece, seleccione la opción **Permitir usuarios locales de NFS con LDAP**.  

    ![Captura de pantalla que muestra la opción Permitir usuarios locales de NFS con LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>Administración de los atributos POSIX de LDAP

Puede administrar los atributos POSIX como UID, el directorio particular y otros valores mediante el complemento de MMC Usuarios y equipos de Active Directory.  En el ejemplo siguiente se muestra el editor de atributos de Active Directory:  

![Editor de atributos de Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Debe establecer los siguientes atributos para los usuarios LDAP y los grupos LDAP: 
* Atributos necesarios para los usuarios LDAP:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* Atributos necesarios para los grupos LDAP:   
    `objectClass: posixGroup`, `gidNumber: 555`
* Todos los usuarios y grupos deben tener unos valores `uidNumber` y `gidNumber`, respectivamente, únicos. 

Azure Active Directory Domain Services (AAD DS) no permite modificar atributos POSIX en usuarios y grupos creados en la unidad organizativa AADDC Users de la organización. Como solución alternativa, puede crear una unidad organizativa personalizada y crear usuarios y grupos en ella.

Si va a sincronizar los usuarios y grupos de su inquilinato de Azure AD con usuarios y grupos de la unidad organizativa AADDC Users, no podrá mover usuarios y grupos a una unidad organizativa personalizada. Los usuarios y grupos creados en la unidad organizativa personalizada no se sincronizarán con el inquilinato de AD. Para más información, consulte la sección [Consideraciones y limitaciones de las unidades organizativas personalizadas](../active-directory-domain-services/create-ou.md#custom-ou-considerations-and-limitations).

### <a name="access-active-directory-attribute-editor"></a>Acceso al Editor de atributos de Active Directory 

En un sistema Windows, puede acceder al Editor de atributos de Active Directory de la siguiente manera:  

1. Haga clic en **Inicio**, vaya a **Herramientas administrativas de Windows** y, luego, haga clic en **Usuarios y equipos de Active Directory** para abrir la ventana correspondiente.  
2.  Haga clic en el nombre de dominio que quiere ver y expanda el contenido.  
3.  Para mostrar el Editor de atributos avanzado, habilite la opción **Características avanzadas** en el menú **Ver** de Usuarios y equipos de Active Directory.   
    ![Captura de pantalla que muestra cómo acceder al menú Características avanzadas del Editor de atributos.](../media/azure-netapp-files/attribute-editor-advanced-features.png) 
4. Haga doble clic en **Usuarios** en el panel izquierdo para ver la lista de usuarios.
5. Haga doble clic en un usuario determinado para ver su pestaña **Editor de atributos**.
 
## <a name="configure-the-nfs-client"></a>Configuración del cliente NFS 

Siga las instrucciones de [Configuración de un cliente NFS para Azure NetApp Files](configure-nfs-clients.md) para configurar el cliente NFS.  

## <a name="next-steps"></a>Pasos siguientes  

* [Configuración del cifrado Kerberos de NFSv4.1](configure-kerberos-encryption.md)
* [Configuración de un cliente NFS para Azure NetApp Files](configure-nfs-clients.md)
* [Configuración de permisos de Unix y cambio del modo de propiedad](configure-unix-permissions-change-ownership-mode.md). 
* [Configuración de ADDS LDAP sobre TLS para Azure NetApp Files](configure-ldap-over-tls.md)
* [Configuración de ADDS LDAP con grupos extendidos para el acceso a volúmenes NFS](configure-ldap-extended-groups.md)
* [Solución de problemas de SMB o de volúmenes de dos protocolos](troubleshoot-dual-protocol-volumes.md)
* [Solución de problemas de volúmenes LDAP](troubleshoot-ldap-volumes.md)
