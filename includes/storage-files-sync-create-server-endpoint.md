---
title: Archivo de inclusión
description: archivo de inclusión
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 6/01/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 9d8d983b5ac7082ff3037d465f43ea9a2ad47fb4
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300436"
---
# <a name="portal"></a>[Portal](#tab/azure-portal)
Para agregar un punto de conexión de servidor, vaya al grupo de sincronización recién creado y seleccione **Agregar punto de conexión del servidor**.

![Adición de un nuevo punto de conexión de servidor al panel Grupo de sincronización](media/storage-files-sync-create-server-endpoint/add-server-endpoint.png)

En la hoja **Agregar punto de conexión de servidor** que se abre, escriba la siguiente información para crear un punto de conexión de servidor:

- **Servidor registrado**: el nombre del servidor o el clúster en el que se va a crear el punto de conexión de servidor.
- **Ruta de acceso**: la ruta de acceso de Windows Server que se va a sincronizar como parte del grupo de sincronización.
- **Nube por niveles**: un conmutador para habilitar o deshabilitar la nube por niveles. Con los niveles de la nube, los archivos que se usen o a los que se acceda con poca frecuencia se pueden colocar en capas en Azure Files.
- **Espacio disponible del volumen**: la cantidad de espacio libre que se reserva en el volumen en el que reside el punto de conexión de servidor. Por ejemplo, si el espacio disponible del volumen se establece en el 50 % en un volumen con un único punto de conexión de servidor, aproximadamente la mitad de la cantidad de datos se coloca en niveles en Azure Files. Con independencia de si la característica de niveles en la nube está habilitada, el recurso compartido de archivos de Azure siempre tiene una copia completa de los datos en el grupo de sincronización.
- **Modo de descarga inicial**: selección opcional que puede ser útil cuando hay archivos en el recurso compartido de archivos de Azure, pero no en el servidor. Tal situación puede existir, por ejemplo, cuando se crea un punto de conexión de servidor para agregar otro servidor de sucursal a un grupo de sincronización o cuando se recupera de un desastre un servidor que no funciona. Si la nube por niveles está habilitada, el valor predeterminado es recuperar inicialmente solo el espacio de nombres, no el contenido del archivo. Esta opción es útil si cree que es preferible que las solicitudes de acceso de los usuarios decidan qué contenido de archivo se recupera en el servidor. Si la nube por niveles está deshabilitada, el valor predeterminado es que el espacio de nombres se descargue primero y, luego, se recuperen los archivos en función de la marca de tiempo de última modificación hasta que se alcance la capacidad local. Sin embargo, se puede cambiar el modo de descarga inicial para que se descargue solo el espacio de nombres. Existe un tercer modo que solo se puede usar si la nube por niveles está deshabilitada para este punto de conexión de servidor. Este modo evita que se recupere primero el espacio de nombres. Los archivos solo aparecerán en el servidor local si tienen la oportunidad de descargarse por completo. Este modo es útil si, por ejemplo, una aplicación necesita que haya archivos completos y no puede tolerar archivos en niveles en su espacio de nombres.

Para agregar el punto de conexión de servidor, seleccione **Crear**. Los archivos se mantienen ahora sincronizados entre el recurso compartido de archivos de Azure y Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ejecute los siguientes comandos de PowerShell para crear el punto de conexión de servidor, asegúrese de reemplazar `<your-server-endpoint-path>` y `<your-volume-free-space>` por los valores deseados y compruebe también el valor opcional de las directivas de [descarga inicial](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-download-section) y [carga inicial](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-sync-section).

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = "NamespaceOnly"
$initialUploadPolicy = "Merge"
# Optional property. Choose from: [Merge] default for all new server endpoints. Content from the server and the cloud merge. This is the right choice if one location is empty or other server endpoints already exist in the sync group. [ServerAuthoritative] This is the right choice when you seeded the Azure file share (e.g. with Data Box) AND you are connecting the server location you seeded from. This enables you to catch up the Azure file share with the changes that happened on the local server since the seeding.

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy `
        -InitialUploadPolicy $initialUploadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [`az storagesync sync-group server-endpoint`](/cli/azure/storagesync/sync-group/server-endpoint#az_storagesync_sync_group_server_endpoint_create) para crear un nuevo punto de conexión de servidor.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --initial-upload-policy Merge [OR] ServerAuthoritative

```

---
