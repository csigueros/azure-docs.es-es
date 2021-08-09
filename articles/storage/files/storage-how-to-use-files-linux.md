---
title: Uso de Azure Files con Linux | Microsoft Docs
description: Aprenda a montar un recurso compartido de archivos de Azure mediante SMB en Linux. Consulte la lista de requisitos previos. Revise las consideraciones de seguridad de SMB en los clientes de Linux.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7e02d85fe5385b8918fbfdb037382aeeef444267
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110088361"
---
# <a name="use-azure-files-with-linux"></a>Uso de Azure Files con Linux
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden montar en distribuciones de Linux mediante el [cliente kernel de SMB](https://wiki.samba.org/index.php/LinuxCIFS).

Para montar un recurso compartido de archivos de Azure en Linux se recomienda usar SMB 3.1.1. De forma predeterminada, Azure Files requiere cifrado en tránsito, que solo se admite SMB 3.0, y en las versiones posteriores. Azure Files también admite SMB 2.1, que no admite el cifrado en tránsito, pero no puede montar recursos compartidos de archivos de Azure con SMB 2.1 desde otra región de Azure o de forma local por motivos de seguridad. Salvo que la aplicación requiera específicamente SMB 2.1, use SMB 3.1.1.

| Distribución | SMB 3.1.1 | SMB 3.0 |
|-|-----------|---------|
| Versión del kernel de Linux | <ul><li>Compatibilidad básica con 3.1.1: 4.17</li><li>Montaje predeterminado: 5.0</li><li>Cifrado AES-128-GCM: 5.3</li></ul> | <ul><li>Compatibilidad básica con 3.0: 3.12</li><li>Cifrado AES-128-CCM: 4.11</li></ul> |
| [Ubuntu](https://wiki.ubuntu.com/Releases) | Cifrado AES-128-GCM: 18.04.5 LTS+ | Cifrado AES-128-CCM: 16.04.4 LTS+ |
| [Red Hat Enterprise Linux (RHEL)](https://access.redhat.com/articles/3078) | <ul><li>Básico: 8.0 (o posterior)</li><li>Montaje predeterminado: 8.2 (o posterior)</li><li>Cifrado AES-128-GCM: 8.2 (o posterior)</li></ul> | 7.5 (o posterior) |
| [Debian](https://www.debian.org/releases/) | Básico: 10 (o posterior) | Cifrado AES-128-CCM: 10 (o posterior) |
| [SUSE Linux Enterprise Server](https://www.suse.com/support/kb/doc/?id=000019587) | Cifrado AES-128-GCM: 15 SP2 (o posterior) | Cifrado AES-128-GCM: 12 SP2 (o posterior) |

Si la distribución de Linux no aparece en la tabla anterior, puede comprobar la versión del kernel de Linux con el comando `uname`:

```bash
uname -r
```

> [!Note]  
> Se agregó compatibilidad con SMB 2.1 a la versión 3.7 del kernel de Linux. Si usa una versión del kernel de Linux posterior a la 3.7, debe ser compatible con SMB 2.1.

## <a name="prerequisites"></a>Prerrequisitos
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Asegúrese de que el paquete cifs-utils está instalado.**  
    El paquete cifs-utils se puede instalar con el administrador de paquetes en la distribución de Linux de su elección. 

    En **Ubuntu** y **Debian**, use el administrador de paquetes `apt`:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    En **Red Hat Enterprise Linux más de 8 (o versiones posteriores)** , use el administrador de paquetes `dnf`:

    ```bash
    sudo dnf install cifs-utils
    ```

    En las versiones anteriores de **Red Hat Enterprise Linux**, use el administrador de paquetes `yum`:

    ```bash
    sudo yum install cifs-utils 
    ```

    En **SUSE Linux Enterprise Server**, use el administrador de paquetes `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    En otras distribuciones, use el administrador de paquetes apropiado o [compile desde el origen](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **La versión más reciente de la interfaz de la línea de comandos (CLI).** Para más información sobre cómo instalar la CLI de Azure, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli) y seleccione el sistema operativo. Si prefiere usar el módulo de Azure PowerShell en PowerShell 6+, puede hacerlo. Sin embargo, las instrucciones de este artículo son para la CLI de Azure.

* **Asegurarse de que el puerto 445 está abierto**: SMB se comunica a través del puerto TCP 445, así que compruebe que el firewall no bloquea el puerto TCP 445 en la máquina cliente.  Reemplace `<your-resource-group>` y `<your-storage-account>` y luego ejecute el siguiente script:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Si la conexión se realizó correctamente, verá algo parecido a la siguiente salida:

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Si no puede abrir el puerto 445 en su red corporativa o si un ISP le impide hacerlo, puede utilizar una conexión VPN o ExpressRoute para solucionar el puerto 445. Para más información, consulte [Consideraciones de redes para el acceso directo a los recursos compartidos de archivos de Azure](storage-files-networking-overview.md).

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montaje del recurso compartido de archivos de Azure a petición con mount
Al montar un recurso compartido de archivos en un sistema operativo Linux, el recurso compartido de archivos remoto se representa como una carpeta en el sistema de archivos local. Los recursos compartidos de archivos se pueden montar en cualquier lugar del sistema. En el ejemplo siguiente el montaje se realiza en la ruta de acceso `/mount`. Para cambiarla por otra que desee, modifique la variable `$mntRoot`.

Reemplace `<resource-group-name>`, `<storage-account-name>` y `<file-share-name>` por la información correcta para su entorno:

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"
fileShareName="<file-share-name>"

mntRoot="/mount"
mntPath="$mntRoot/$storageAccountName/$fileShareName"

sudo mkdir -p $mntPath
```

A continuación, monte el recurso compartido de archivos mediante el comando `mount`. En el siguiente ejemplo, el comando `$smbPath` se rellena con el nombre de dominio completo para el punto de conexión del archivo de la cuenta de almacenamiento y `$storageAccountKey` se rellena con la clave de la cuenta de almacenamiento. 

# <a name="smb-311"></a>[SMB 3.1.1](#tab/smb311)
> [!Note]  
> A partir de la versión 5.0 del kernel de Linux, SMB 3.1.1 es el protocolo negociado predeterminado. Si usa una versión del kernel de Linux anterior a la 5.0, especifique `vers=3.1.1` en la lista de opciones de montaje.  

```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-30"></a>[SMB 3.0](#tab/smb30)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-21"></a>[SMB 2.1](#tab/smb21)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=2.1,username=$storageAccountName,password=$storageAccountKey,serverino
```

---

Puede usar `uid`/`gid` o `dir_mode` y `file_mode` en las opciones de montaje del comando `mount` para establecer permisos. Para obtener más información sobre cómo establecer permisos, consulte [notación numérica de UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) en Wikipedia.

Si lo desea, también puede montar el mismo recurso compartido de archivos de Azure en varios puntos de montaje. Cuando haya terminado de usar el recurso compartido de archivos de Azure, use `sudo umount $mntPath` para desmontarlo.

## <a name="automatically-mount-file-shares"></a>Montaje automático de recursos compartidos de archivos
Al montar un recurso compartido de archivos en un sistema operativo Linux, el recurso compartido de archivos remoto se representa como una carpeta en el sistema de archivos local. Los recursos compartidos de archivos se pueden montar en cualquier lugar del sistema. En el ejemplo siguiente el montaje se realiza en la ruta de acceso `/mount`. Para cambiarla por otra que desee, modifique la variable `$mntRoot`.

```bash
mntRoot="/mount"
sudo mkdir -p $mntRoot
```

Para montar un recurso compartido de archivos de Azure en Linux, use el nombre de la cuenta de almacenamiento como nombre de usuario del recurso compartido de archivos y la clave de la cuenta de almacenamiento como contraseña. Dado que las credenciales de la cuenta de almacenamiento pueden cambiar con el tiempo, deben almacenarse por separado de la configuración de montaje. 

En el ejemplo siguiente se muestra cómo crear un archivo para almacenar las credenciales. No olvide reemplazar `<resource-group-name>` y `<storage-account-name>` por la información correcta para su entorno.

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"

# Create a folder to store the credentials for this storage account and
# any other that you might set up.
credentialRoot="/etc/smbcredentials"
sudo mkdir -p "/etc/smbcredentials"

# Get the storage account key for the indicated storage account.
# You must be logged in with az login and your user identity must have 
# permissions to list the storage account keys for this command to work.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# Create the credential file for this individual storage account
smbCredentialFile="$credentialRoot/$storageAccountName.cred"
if [ ! -f $smbCredentialFile ]; then
    echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
    echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
else 
    echo "The credential file $smbCredentialFile already exists, and was not modified."
fi

# Change permissions on the credential file so only root can read or modify the password file.
sudo chmod 600 $smbCredentialFile
```

Para montar automáticamente un recurso compartido de archivos, puede elegir entre usar un montaje estático a través de la utilidad `/etc/fstab` o un montaje dinámico a través de la utilidad `autofs`. 

### <a name="static-mount-with-etcfstab"></a>Montaje estático con /etc/fstab
Utilice el entorno anterior para crear una carpeta para su cuenta de almacenamiento o recurso compartido de archivos en la carpeta de montaje. Reemplace `<file-share-name>` por el nombre adecuado del recurso compartido de archivos de Azure.

```bash
fileShareName="<file-share-name>"

mntPath="$mntRoot/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath
```

Por último, cree un registro en el archivo `/etc/fstab` para el recurso compartido de archivos de Azure. En el comando siguiente, se usa el valor predeterminado de los permisos de archivo y carpeta de Linux, 0755, lo que significa lectura, escritura y ejecución para el propietario (según el propietario de Linux de archivo o directorio), lectura y ejecución para los usuarios en el grupo de propietarios y lectura y ejecución para otros en el sistema. Es posible que desee establecer los permisos `uid` y `gid`, o `dir_mode` y `file_mode` alternativos en el montaje según sea necesario. Para obtener más información sobre cómo establecer permisos, consulte [notación numérica de UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) en Wikipedia.

```bash
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
    echo "$smbPath $mntPath cifs nofail,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
else
    echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
fi

sudo mount -a
```

> [!Note]  
> A partir de la versión 5.0 del kernel de Linux, SMB 3.1.1 es el protocolo negociado predeterminado. Puede especificar versiones alternativas del protocolo mediante la opción de montaje `vers` (las versiones de protocolo son `3.1.1`, `3.0` y `2.1`).

### <a name="dynamically-mount-with-autofs"></a>Montaje dinámico con autofs
Para montar dinámicamente un recurso compartido de archivos con la utilidad `autofs`, instálelo mediante el administrador de paquetes en la distribución de Linux que prefiera.  

En las distribuciones de **Ubuntu** y **Debian**, use el administrador de paquetes `apt`:

```bash
sudo apt update
sudo apt install autofs
```

En **Red Hat Enterprise Linux 8 (o versiones posteriores)** , use el administrador de paquetes `dnf`:
```bash
sudo dnf install autofs
```

En las versiones anteriores de **Red Hat Enterprise Linux**, use el administrador de paquetes `yum`:

```bash
sudo yum install autofs 
```

En **SUSE Linux Enterprise Server**, use el administrador de paquetes `zypper`:
```bash
sudo zypper install autofs
```

A continuación, actualice los archivos de configuración `autofs`. 

```bash
fileShareName="<file-share-name>"

httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath" > /etc/auto.fileshares

echo "/fileshares /etc/auto.fileshares --timeout=60" > /etc/auto.master
```

El último paso es reiniciar el servicio `autofs`.

```bash
sudo systemctl restart autofs
```

## <a name="securing-linux"></a>Protección de Linux
Para montar un recurso compartido de archivos de Azure con SMB se debe poder acceder al puerto 445. Muchas organizaciones bloquean este puerto debido a los riesgos de seguridad inherentes a SMB 1. SMB 1, también conocido como CIFS (Sistema de archivos de Internet común), es un protocolo de sistema de archivos heredado que se incluye en muchas distribuciones de Linux. SMB 1 es un protocolo obsoleto, ineficaz y, lo más importante, no seguro. La buena noticia es que Azure Files no es compatible con SMB 1 y, a partir de la versión 4.18 del kernel de Linux, Linux hace posible deshabilitar SMB 1. Siempre [recomendamos encarecidamente](https://aka.ms/stopusingsmb1) deshabilitar SMB 1 en los clientes Linux antes de usar recursos compartidos de archivos SMB en producción.

A partir del kernel de Linux 4.18, el módulo de kernel de SMB, denominado `cifs` por motivos de herencia, expone un nuevo parámetro de módulo (a menudo conocido como *parm* de diversos documentos externos), denominado `disable_legacy_dialects`. Aunque se presentó en el kernel de Linux 4.18, algunos proveedores han trasladado este cambio a los kernels más antiguos que admiten. Para mayor comodidad, en la tabla siguiente se detalla la disponibilidad de este parámetro de módulo en distribuciones de Linux comunes.

| Distribución | Puede deshabilitar SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | Sí |
| Ubuntu 19.04+ | Sí |
| Debian 8-9 | No |
| Debian 10+ | Sí |
| Fedora 29+ | Sí |
| CentOS 7 | No | 
| CentOS 8+ | Sí |
| Red Hat Enterprise Linux 6.x-7.x | No |
| Red Hat Enterprise Linux 8+ | Sí |
| openSUSE Leap 15.0 | No |
| openSUSE Leap 15.1+ | Sí |
| openSUSE Tumbleweed | Sí |
| SUSE Linux Enterprise 11.x-12.x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15.1 | No |

Puede comprobar si la distribución de Linux es compatible con el parámetro del módulo `disable_legacy_dialects` a través del comando siguiente.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Este comando debe generar el siguiente mensaje:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Antes de deshabilitar SMB 1, debe comprobar para asegurarse de que el módulo SMB no está cargado actualmente en el sistema (esto sucede automáticamente si ha montado un recurso compartido de SMB). Puede hacerlo con el siguiente comando, que no debe generar nada si no se carga SMB:

```bash
lsmod | grep cifs
```

Para descargar el módulo, desmonte primero todos los recursos compartidos de SMB (con el comando `umount` como se describió anteriormente). Puede identificar todos los recursos compartidos de SMB montados en el sistema con el siguiente comando:

```bash
mount | grep cifs
```

Una vez que haya desmontado todos los recursos compartidos de archivos SMB, es seguro descargar el módulo. Para ello, puede usar el comando `modprobe` :

```bash
sudo modprobe -r cifs
```

Puede cargar manualmente el módulo con SMB 1 descargado mediante el comando `modprobe`:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Por último, puede comprobar que el módulo SMB se ha cargado con el parámetro, para ello, examine los parámetros cargados en `/sys/module/cifs/parameters`:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Para deshabilitar de forma persistente SMB 1 en las distribuciones basadas en Ubuntu y Debian, debe crear un nuevo archivo (si aún no tiene opciones personalizadas para otros módulos) denominado `/etc/modprobe.d/local.conf` con la configuración. Para ello, use el siguiente comando:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Para comprobar que esto ha funcionado, cargue el módulo SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para más información sobre Azure Files:

* [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
* [P+F](./storage-files-faq.md)
* [Solución de problemas](storage-troubleshoot-linux-file-connection-problems.md)
