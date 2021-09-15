---
title: Uso de almacenamiento de archivos externo en Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo configurar un laboratorio que use almacenamiento de archivos externo en servicios de laboratorio.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: dc0f2a4f51fb12c61d0e1e16cb23d030a5dc9cc6
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969280"
---
# <a name="use-external-file-storage-in-lab-services"></a>Uso de almacenamiento de archivos externo en Lab Services

En este artículo se tratarán algunas de las opciones de almacenamiento de archivos externos al utilizar Azure Lab Services. [Azure Files](https://azure.microsoft.com/services/storage/files/) ofrece recursos compartidos de archivos en la nube totalmente administrados a los que [se puede acceder mediante SMB 2.1 and SMB 3.0](../storage/files/storage-how-to-use-files-windows.md). Un recurso compartido de Azure Files puede conectarse de forma pública o privada dentro de una red virtual. También puede configurar el recurso compartido para que use las credenciales de Active Directory de un alumno para conectarse al recurso compartido de archivos. Si está usando una máquina Linux, también puede usar Azure NetApp Files con volúmenes NFS para el almacenamiento de archivos externos con Azure Lab Services.  

## <a name="which-solution-to-use"></a>Qué solución se va a usar

Cada solución tiene requisitos y capacidades diferentes. En la siguiente tabla se enumeran los puntos importantes que se deben tener en cuenta para cada solución.  

|     Solución    |    Consideraciones importantes    |
| -------------- | ------------------------ |
| [Recurso compartido de Azure Files con el punto de conexión público](#azure-files-share) | <ul><li>Todos tienen acceso de lectura y escritura.</li><li>No se requiere ningún emparejamiento de red virtual.</li><li>Accesible para todas las máquinas virtuales, no solo máquinas virtuales de laboratorio.</li><li>Si está usando Linux, los alumnos tendrán acceso a la clave de la cuenta de almacenamiento.</li></ul> |
| [Recurso compartido de Azure Files con punto de conexión privado](#azure-files-share) | <ul><li>Todos tienen acceso de lectura y escritura.</li><li>Se requiere el emparejamiento de red virtual.</li><li>Accesible solo para las máquinas virtuales en la misma red que la cuenta de almacenamiento (o en una red emparejada a dicha cuenta).</li><li>Si está usando Linux, los alumnos tendrán acceso a la clave de la cuenta de almacenamiento.</li></ul> |
| [Azure Files con autorización basada en identidades](#azure-files-with-identity-based-authorization) | <ul><li>Se pueden establecer permisos de acceso de lectura o de lectura y escritura para la carpeta o el archivo.</li><li>Se requiere el emparejamiento de red virtual.</li><li>La cuenta de almacenamiento debe estar conectada a Active Directory.</li><li>Los dispositivos deben estar unidos a un dominio.</li><li>La clave de la cuenta de almacenamiento no se utiliza para que los alumnos se conecten al recurso compartido de archivos.</li></ul> |
| [Archivos de NetApp con volúmenes NFS](#netapp-files-with-nfs-volumes) | <ul><li>Se puede establecer el acceso de lectura o de lectura y escritura para los volúmenes.</li><li>Los permisos se configuran con la dirección IP de la máquina virtual de un alumno.</li><li>Se requiere el emparejamiento de red virtual.</li><li>Es posible que tenga que registrarse para usar el servicio NetApp Files.</li><li>Solo Linux.</li></ul>

El costo de usar un almacenamiento externo no se incluye en el precio por usar Azure Lab Services.  Para obtener más información sobre los precios, consulte [Precios de Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) y [Precios de Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Recurso compartido de Azure Files

Se tiene acceso a los recursos compartidos de Azure Files mediante un punto de conexión público o privado. Monte los recursos compartidos y use la clave de la cuenta de almacenamiento como contraseña. Con este enfoque, todos tienen acceso de lectura y escritura al recurso compartido.

Si está usando un punto de conexión público al recurso compartido de Azure Files, es importante que recuerde lo siguiente:

- La red virtual de la cuenta de almacenamiento no tiene que estar emparejada con la cuenta del laboratorio.  El recurso compartido se puede crear en cualquier momento antes de publicar la máquina virtual de la plantilla.
- Se puede tener acceso al recurso compartido desde cualquier equipo si un usuario tiene la clave de la cuenta de almacenamiento.  
- Los alumnos de Linux pueden ver la clave de la cuenta de almacenamiento. Las credenciales para montar un recurso compartido de Azure Files se almacenan en el archivo `{file-share-name}.cred` de las máquinas virtuales Linux y se pueden leer mediante sudo. Ya que a los alumnos se les concede acceso sudo de manera predeterminada en las máquinas virtuales de Azure Lab Services, pueden leer la clave de la cuenta de almacenamiento. Si el punto de conexión de la cuenta de almacenamiento es público, los alumnos pueden obtener acceso al recurso compartido fuera de la máquina virtual de los alumnos. Considere la posibilidad de rotar la clave de la cuenta de almacenamiento una vez finalizada la clase, así como de usar recursos compartidos privados.

Si está usando un punto de conexión privado al recurso compartido de Azure Files, es importante que recuerde lo siguiente:

- El acceso está restringido al tráfico que se origina desde la red privada y no se puede acceder a él a través de la red pública de Internet. Solo las máquinas virtuales de la red virtual privada, las máquinas virtuales de una red emparejadas con la red virtual privada o las máquinas conectadas a una VPN para la red privada pueden acceder al recurso compartido.  
- Los alumnos de Linux pueden ver la clave de la cuenta de almacenamiento. Las credenciales para montar un recurso compartido de Azure Files se almacenan en el archivo `{file-share-name}.cred` de las máquinas virtuales Linux y se pueden leer mediante sudo. Ya que a los alumnos se les concede acceso sudo de manera predeterminada en las máquinas virtuales de Azure Lab Services, pueden leer la clave de la cuenta de almacenamiento. Considere la posibilidad de rotar la clave de la cuenta de almacenamiento una vez finalizada la clase.
- Este enfoque requiere que la red virtual de recurso compartido de archivos esté emparejada con la cuenta de laboratorio. La red virtual de la cuenta de Azure Storage se debe emparejar con la red virtual de la cuenta de laboratorio antes de que se cree el laboratorio.

> [!NOTE]
> De manera predeterminada, los recursos compartidos de archivos estándar pueden llegar hasta 5 TiB. Consulte [Creación de un recurso compartido de archivos de Azure](../storage/files/storage-how-to-create-file-share.md) para obtener información sobre cómo crear recursos compartidos de archivos que puedan llegar hasta 100 TiB.

Siga estos pasos para crear una máquina virtual conectada a un recurso compartido de archivos de Azure.

1. Cree una [cuenta de Azure Storage](../storage/files/storage-how-to-create-file-share.md). En la página **Método de conectividad**, elija **punto de conexión público** o **punto de conexión privado**.
2. Si opta por el método privado, cree un [punto de conexión privado](../private-link/tutorial-private-endpoint-storage-portal.md) para que los recursos compartidos de archivos sean accesibles desde la red virtual.
3. Cree un [recurso compartido de archivos de Azure](../storage/files/storage-how-to-create-file-share.md). El recurso compartido de archivos es accesible mediante el nombre de host público de la cuenta de almacenamiento.  El recurso compartido de archivos es accesible mediante la dirección IP privada si se usa un punto de conexión privado.  
4. Montaje del recurso compartido de archivos de Azure en la plantilla de máquina virtual:
    - [Windows](../storage/files/storage-how-to-use-files-windows.md)
    - [Linux](../storage/files/storage-how-to-use-files-linux.md). Para evitar problemas de montaje en las máquinas virtuales de alumnos, consulte la sección sobre el [uso de Azure Files con Linux](#use-azure-files-with-linux).
5. [Publicación](how-to-create-manage-template.md#publish-the-template-vm) de la plantilla de máquina virtual.

> [!IMPORTANT]
> Asegúrese de que el Firewall de Windows Defender no esté bloqueando la conexión SMB saliente a través del puerto 445. De forma predeterminada, SMB se permite para las máquinas virtuales de Azure.

### <a name="use-azure-files-with-linux"></a>Uso de Azure Files con Linux

Si usa las instrucciones predeterminadas para montar un recurso compartido de Azure Files, parecerá que el recurso compartido de archivos desaparece en las máquinas virtuales de los alumnos después de publicar la plantilla. El siguiente script modificado soluciona este problema.  

En el caso de un recurso compartido de archivos con un punto de conexión público:
```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

En el caso de un recurso compartido de archivos con un punto de conexión privado:
```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_ip=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_ip/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Si la plantilla de máquina virtual que monta el recurso compartido de Azure Files en el directorio `/mnt` ya está publicada, el alumno puede:

- Mueva la instrucción para montar `/mnt` en la parte superior del archivo `/etc/fstab`.  
- Modificar la instrucción para montar `/mnt/{file-share-name}` en otro directorio, como `/prm-mnt/{file-share-name}`.

Los alumnos deben ejecutar `mount -a` para volver a montar los directorios.

Para obtener más información general, consulte [Uso de Azure Files con Linux](../storage/files/storage-how-to-use-files-linux.md).

## <a name="azure-files-with-identity-based-authorization"></a>Azure Files con autorización basada en identidades

Azure Files también se puede acceder a los recursos compartidos mediante la autenticación de Active Directory, si se cumple lo siguiente:

- La máquina virtual del alumno está unida a un dominio.
- La autenticación de Active Directory está [habilitada en la cuenta de Azure Storage](../storage/files/storage-files-active-directory-overview.md) que hospeda el recurso compartido de archivos.  

La unidad de red está montada en la máquina virtual mediante la identidad del usuario, no mediante la clave de la cuenta de almacenamiento. Los puntos de conexión privados o públicos proporcionan acceso a la cuenta de almacenamiento.

Tenga en cuenta los siguientes puntos importantes:

- Puede configurar los permisos en el nivel de directorio o de archivo.
- Puede usar las credenciales del usuario actual para autenticarse en el recurso compartido de archivos.

En el caso de un punto de conexión público, la red virtual de la cuenta de almacenamiento no tiene que estar emparejada con la cuenta de laboratorio. Puede crear el recurso compartido de archivos en cualquier momento antes de publicar la plantilla de máquina virtual.

En el caso de un punto de conexión privado:

- El acceso está restringido al tráfico que se origina desde la red privada y no se puede acceder a él a través de la red pública de Internet. Solo las máquinas virtuales de la red virtual privada, las máquinas virtuales de una red emparejadas con la red virtual privada o las máquinas conectadas a una VPN para la red privada pueden acceder al recurso compartido.  
- Este enfoque requiere que la red virtual de recurso compartido de archivos esté emparejada con la cuenta de laboratorio. La red virtual de la cuenta de Azure Storage se debe emparejar con la red virtual de la cuenta de laboratorio antes de que se cree el laboratorio.

Para crear un recurso compartido de Azure Files que esté habilitado para la autenticación de Active Directory, así como unir a un dominio las máquinas virtuales de laboratorio, siga estos pasos:

1. Cree una [cuenta de Azure Storage](../storage/files/storage-how-to-create-file-share.md).
2. Si opta por el método privado, cree un [punto de conexión privado](../private-link/tutorial-private-endpoint-storage-portal.md) para que los recursos compartidos de archivos sean accesibles desde la red virtual. Cree una [zona DNS privada](../dns/private-dns-privatednszone.md) o use una existente. Las zonas de Azure DNS privadas proporcionan la resolución de nombres dentro de una red virtual.
3. Cree un [recurso compartido de archivos de Azure](../storage/files/storage-how-to-create-file-share.md).
4. Siga los pasos para habilitar la autorización basada en identidades. Si usa Active Directory local y lo sincroniza con Azure Active Directory (Azure AD), consulte el artículo [Autenticación de Active Directory Domain Services local en SMB para recursos compartidos de archivos de Azure](../storage/files/storage-files-identity-auth-active-directory-enable.md). Si solo usa Azure AD, consulte [Habilitación de la autenticación de Azure Active Directory Domain Services en Azure Files](../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md).
    >[!IMPORTANT]
    >Hable con el equipo que administra su instancia de Active Directory para comprobar que se cumplen todos los requisitos previos enumerados en las instrucciones.
5. Asignación de roles de permisos de recurso compartido SMB en Azure. Para obtener más información sobre los permisos que se conceden a cada rol, consulte [permisos de nivel de recurso compartido](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).
    - El rol **Colaborador con privilegios elevados de recursos compartidos de SMB de datos de archivos de almacenamiento** se debe asignar a la persona o grupo que configurará los permisos para el contenido del recurso compartido de archivos.
    - El rol **Colaborador de recursos compartidos de SMB de datos de archivos de almacenamiento** debe asignarse a los alumnos que necesiten agregar o editar archivos en el recurso compartido de archivos.
    - El rol **Lector de recursos compartidos de SMB de datos de archivos de almacenamiento** se debe asignar a los alumnos que solo necesiten leer los archivos del recurso compartido de archivos.
6. Configure los permisos en el nivel de directorio o de archivo para el recurso compartido. Debe configurar los permisos desde una máquina unida a un dominio que tenga acceso de red al recurso compartido de archivos. Para modificar los permisos en el nivel de archivo o de directorio, monte el recurso compartido de archivos con la clave de almacenamiento, no con las credenciales de Azure AD. Para asignar permisos, use el comando [Set-Acl](/powershell/module/microsoft.powershell.security/set-acl) de PowerShell, o [icacls](/windows-server/administration/windows-commands/icacls) en Windows.
7. [Empareje la red virtual](how-to-connect-peer-virtual-network.md) de la cuenta de almacenamiento con la cuenta de laboratorio.
8. [Cree el laboratorio de clase](how-to-manage-classroom-labs.md).
9. Guarde un script en la plantilla de la máquina virtual que los alumnos puedan ejecutar para conectarse a la unidad de red. Para obtener un script de ejemplo:
    1. Abra la cuenta de almacenamiento en Azure Portal.
    1. En **File service**, seleccione **Recursos compartidos de archivos**.
    1. Busque el recurso compartido al que desea conectarse, seleccione el botón de puntos suspensivos en el extremo derecho y elija **Conectar**.
    1. Verá instrucciones para Windows, Linux y macOS. Si está usando Windows, establezca el **método de autenticación** en **Active Directory**.
    1. Copie el código del ejemplo y guárdelo en la máquina de plantilla en un archivo `.ps1` para Windows, o un archivo `.sh` para Linux.
10. En la máquina de plantilla, descargue y ejecute el script para [unir las máquinas de los alumnos al dominio](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage). El script `Join-AzLabADTemplate` [publicará la plantilla de máquina virtual](how-to-create-manage-template.md#publish-the-template-vm) automáticamente.  
    > [!NOTE]
    > La máquina de plantilla no está unida a un dominio. Para ver los archivos en el recurso compartido, los instructores deben usar la máquina virtual de un alumno.
11. Los alumnos que usan Windows pueden conectarse al recurso compartido de Azure Files mediante el [Explorador de archivos](../storage/files/storage-how-to-use-files-windows.md) con sus credenciales después de especificar la ruta de acceso al recurso compartido de archivos. Como alternativa, los alumnos pueden ejecutar el script anterior para conectarse a la unidad de red. En el caso de los alumnos que están usando Linux, ejecute el script anterior.

## <a name="netapp-files-with-nfs-volumes"></a>NetApp Files con volúmenes NFS

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) es un servicio de almacenamiento de archivos de alto rendimiento, medido y de clase empresarial.

- Las directivas de acceso se pueden establecer por volumen.
- Las directivas de permisos se basan en IP para cada volumen.
- Si los estudiantes necesitan su propio volumen al que otros alumnos no tienen acceso, deben asignarse directivas de permisos después de publicar el laboratorio.
- En el contexto de Azure Lab Services, solo se admiten máquinas Linux.
- La red virtual del grupo de capacidad de Azure NetApp Files se debe emparejar con la red virtual de la cuenta de laboratorio **antes** de que se cree el laboratorio.

Para usar un recurso compartido de Azure NetApp Files en Azure Lab Services:

1. Obtenga acceso a [Azure NetApp Files](https://aka.ms/azurenetappfiles), si es necesario.
2. Para crear un grupo de capacidad de NetApp Files y uno o varios volúmenes NFS, consulte [Configuración de Azure NetApp Files y un volumen NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md). Para obtener información sobre los niveles de servicio, consulte [Niveles de servicio para Azure NetApp Files](../azure-netapp-files/azure-netapp-files-service-levels.md).
3. [Emparejar la red virtual](how-to-connect-peer-virtual-network.md) del grupo de capacidad de NetApp Files con la cuenta de laboratorio.
4. [Cree el laboratorio de clase](how-to-manage-classroom-labs.md).
5. En la plantilla de máquina virtual, instale los componentes necesarios para usar los recursos compartidos de archivos de NFS.
    - Ubuntu:

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS:

        ```bash
        sudo yum install nfs-utils
        ```

6. En la plantilla de máquina virtual, guarde el siguiente script como `mount_fileshare.sh` para [montar el recurso compartido de NetApp Files](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). Asigne `capacity_pool_ipaddress` a la variable la dirección IP de destino de montaje para el grupo de capacidad. Obtenga las instrucciones de montaje del volumen para encontrar el valor adecuado. El script espera la ruta de acceso o el nombre del volumen de NetApp Files. Para asegurarse de que los usuarios pueden ejecutar el script, ejecute `chmod u+x mount_fileshare.sh`.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ might cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Si todos los alumnos están compartiendo el acceso al mismo volumen de NetApp Files, puede ejecutar el script `mount_fileshare.sh` en la máquina de la plantilla antes de su publicación. Si cada alumno recibe su propio volumen, guarde el script para que el alumno lo ejecute más tarde.
8. [Publicación](how-to-create-manage-template.md#publish-the-template-vm) de la plantilla de máquina virtual.
9. [Configure la directiva](../azure-netapp-files/azure-netapp-files-configure-export-policy.md) para el recurso compartido de archivos. La directiva de exportación puede permitir que una sola máquina virtual o varias tengan acceso a un volumen. Puede conceder acceso de solo lectura o de lectura y escritura.
10. Los alumnos deben iniciar su máquina virtual y ejecutar el script para montar el recurso compartido de archivos. Solo tendrán que ejecutar el script una vez. El comando tendrá un aspecto similar al siguiente: `./mount_fileshare.sh myvolumename`.

## <a name="next-steps"></a>Pasos siguientes

Estos pasos son comunes a la configuración de cualquier laboratorio.

- [Creación y administración de plantillas](how-to-create-manage-template.md)
- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)
