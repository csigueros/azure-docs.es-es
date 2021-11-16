---
title: Montaje de un sistema de archivos virtual en un grupo
description: Obtenga información sobre cómo montar un sistema de archivos virtual en un grupo de Batch.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 11/11/2021
ms.openlocfilehash: ed83f5771a451f92c69ba5f80de7bfa7d8388778
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369424"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Montaje de un sistema de archivos virtual en un grupo de Batch

Azure Batch admite el montaje de almacenamiento en la nube o un sistema de archivos externo en nodos de ejecución de Windows o Linux en los grupos de Batch. Cuando un nodo de ejecución se une a un grupo, el sistema de archivos virtual se monta y se trata como una unidad local en ese nodo. 

Puede montar sistemas de archivos como: 

- Azure Files
- Azure Blob Storage
- Network File System (NFS), incluida una [caché de Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)
- Sistema de archivos de Internet común (CIFS)

En este artículo, aprenderá a montar un sistema de archivos virtual en un grupo de nodos de ejecución mediante la [biblioteca de administración de Batch para .NET](/dotnet/api/overview/azure/batch).

> [!NOTE]
> El montaje de un sistema de archivos virtual solo se admite en grupos de Batch creados el 8 de agosto de 2019 o posteriormente. Los grupos de Batch creados antes de esa fecha no admitirán esta característica.

## <a name="benefits-of-mounting-on-a-pool"></a>Ventajas del montaje en un grupo

Montar el sistema de archivos en el grupo, en lugar de permitir que las tareas recuperen sus propios datos de un conjunto de datos de gran tamaño, facilita y hace más eficaz el acceso de las tareas a los datos necesarios.

Considere un escenario con varias tareas que requieren acceso a un conjunto de datos común, como la representación de una película. Cada tarea representa uno o varios fotogramas a la vez de los archivos de la escena. Al montar una unidad que contiene los archivos de la escena, es más fácil que los nodos de ejecución tengan acceso a los datos compartidos.

Además, el sistema de archivos subyacente se puede elegir y escalar de forma independiente según el rendimiento y la escala (rendimiento e IOPS) requeridos por el número de nodos de ejecución que tienen acceso simultáneamente a los datos. Por ejemplo, puede usar una caché en memoria distribuida de [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) para admitir representaciones cinematográficas de gran tamaño con miles de nodos de representación simultáneos y acceder a los datos de origen que se encuentran en el entorno local. En el caso de los datos que ya se encuentran en instancias de Blob Storage basadas en la nube, puede usar [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) para montar estos datos como un sistema de archivos local. Blobfuse solo está disponible en los nodos de Linux; sin embargo, [Azure Files](../storage/files/storage-files-introduction.md) proporciona un flujo de trabajo similar y está disponible en Windows y Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Montaje de un sistema de archivos virtual en un grupo  

El montaje de un sistema de archivos virtual en un grupo hace que el sistema de archivos esté disponible para todos los nodos de ejecución del grupo. La configuración del sistema de archivos se produce cuando un nodo de proceso se une a un grupo o se reinicia, o bien cuando se restablece su imagen inicial.

Para montar un sistema de archivos en un grupo, cree un objeto `MountConfiguration`. Elija el objeto que se adapte al sistema de archivos virtual: `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration` o `CifsMountConfiguration`.

Todos los objetos de configuración de montaje necesitan los siguientes parámetros base. Algunas configuraciones de montaje tienen en uso parámetros específicos del sistema de archivos, que se describen con más detalle en los ejemplos de código.

- **Nombre de cuenta u origen**: para montar un recurso compartido de archivos virtual, necesita el nombre de la cuenta de almacenamiento o su origen.
- **Ruta de acceso de montaje relativa u origen**: ubicación del sistema de archivos montado en el nodo de ejecución, en relación con el directorio `fsmounts` estándar accesible en el nodo mediante `AZ_BATCH_NODE_MOUNTS_DIR`. La ubicación exacta varía en función del sistema operativo que se use en el nodo. Por ejemplo, la ubicación física en un nodo de Ubuntu se asigna a `mnt\batch\tasks\fsmounts`. En un nodo CentOS, la ubicación se asigna a `mnt\resources\batch\tasks\fsmounts`.
- **Opciones de montaje u opciones de blobfuse**: estas opciones describen parámetros específicos para el montaje de un sistema de archivos.

Una vez creado el objeto `MountConfiguration`, asigne el objeto a la propiedad `MountConfigurationList` al crear el grupo. El montaje del sistema de archivos se produce cuando un nodo se une a un grupo o se reinicia, o bien cuando se restablece su imagen inicial.

Cuando se monta el sistema de archivos, se crea una variable de entorno `AZ_BATCH_NODE_MOUNTS_DIR` que apunta a la ubicación de los sistemas de archivos montados y a los archivos de registro, que son útiles para la solución de problemas y la depuración. Los archivos de registro se explican con más detalle en la sección [Diagnóstico de errores de montaje](#diagnose-mount-errors).  

> [!IMPORTANT]
> El número máximo de sistemas de archivos montados en un grupo es 10. Consulte [Límites y cuotas del servicio Batch](batch-quota-limit.md#other-limits) para obtener más información y otros límites.

## <a name="mount-azure-file-share-with-powershell"></a>Montaje de un recurso compartido de archivos de Azure con PowerShell

Puede montar un recurso compartido de archivos de Azure en un grupo de Batch mediante [Azure PowerShell](/powershell/) o [Azure Cloud Shell](../cloud-shell/overview.md).

# <a name="windows"></a>[Windows](#tab/windows)

1. Inicie sesión en la suscripción de Azure.

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Obtenga el contexto de la cuenta de Batch.
    
    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. Cree un grupo de Batch con la siguiente configuración. Reemplace los valores de ejemplo por su propia información, según corresponda.

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "Storage-Account-key")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
    ```

1. Acceda a los archivos de montaje mediante la ruta de acceso directa de la unidad. Por ejemplo:

    ```powershell
    cmd /c "more S:\folder1\out.txt & timeout /t 90 > NULL"
    ```

1. Compruebe que el archivo de salida es correcto.

1. Si usa el Protocolo de escritorio remoto (RDP) o SSH, agregue credenciales para acceder directamente a la unidad `S`. El agente de Azure Batch solo concede acceso a las tareas de Azure Batch en Windows. Cuando se conecta al nodo mediante RDP, la cuenta de usuario no tiene acceso automático a la unidad de montaje. 

    Use `cmdkey` para agregar sus credenciales. Reemplace los valores de ejemplo por su propia información.

    ```powershell
    cmdkey /add:"<storage-account-name>.file.core.windows.net" /user:"Azure\<storage-account-name>" /pass:"<storage-account-key>"
    ```

# <a name="linux"></a>[Linux](#tab/linux)

1. Inicie sesión en la suscripción de Azure.

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Obtenga el contexto de la cuenta de Batch.

    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. Cree un grupo de Batch con la siguiente configuración. Reemplace los valores de ejemplo por su propia información, según corresponda.

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "<Storage-Account-key>", "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
     
    ```

1. Acceda a los archivos de montaje mediante la variable de entorno `AZ_BATCH_NODE_MOUNTS_DIR`. Por ejemplo:

    ```bash
    /bin/bash -c 'more $AZ_BATCH_NODE_MOUNTS_DIR/S/folder1/out.txt; sleep 20s'
    ```

    Opcionalmente, también puede acceder a los archivos de montaje mediante la ruta de acceso directa.

1. Compruebe que el archivo de salida es correcto.

1. Si usa RDP o SSH, puede acceder de forma manual a la unidad `S` directamente. Use la ruta de acceso `/mnt/batch/tasks/fsmounts/S`.

---

### <a name="troubleshoot-powershell-mounting"></a>Solución de problemas de montaje de PowerShell

Al montar un recurso compartido de archivos de Azure en un grupo de Batch con PowerShell o Cloud Shell, podría recibir el siguiente error:

```text
Mount Configuration Error | An error was encountered while configuring specified mount(s)
Message: System error (out of memory, cannot fork, no more loop devices)
MountConfigurationPath: S
```

Si recibe este error, conéctese mediante RDP o SSH al nodo para comprobar los archivos de registro relacionados. El agente de Batch implementa el montaje de forma diferente en Windows y Linux. En Linux, Batch instala el paquete `cifs-utils`. Después, Batch emite el comando de montaje. En Windows, Batch usa `cmdkey` para agregar las credenciales de la cuenta de Batch. Después, Batch emite el comando de montaje mediante `net use`. Por ejemplo:

```powershell
net use S: \\<storage-account-name>.file.core.windows.net\<fileshare> /u:AZURE\<storage-account-name> <storage-account-key>
```

# <a name="windows"></a>[Windows](#tab/windows)

1. Conéctese al nodo mediante RDP.

1. Abra el archivo de registro, `fshare-S.log`. La ruta de acceso al archivo es `D:\batch\tasks\fsmounts`.

1. Revise los mensajes de error. Por ejemplo:

    ```text
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. Solucione el problema siguiendo las indicaciones de [Solución de problemas de Azure Files en Windows (SMB)](../storage/files/storage-troubleshoot-windows-file-connection-problems.md).

# <a name="linux"></a>[Linux](#tab/linux)

1. Conéctese al nodo mediante SSH.

1. Abra el archivo de registro, `fshare-S.log`. La ruta de acceso al archivo es `/mnt/batch/tasks/fsmounts`.

1. Revise los mensajes de error. Por ejemplo, `mount error(13): Permission denied`.

1. Solucione el problema siguiendo las indicaciones de [Solución de problemas de Azure Files en Linux (SMB)](../storage/files/storage-troubleshoot-linux-file-connection-problems.md).

---

Si no puede usar RDP o SSH para comprobar los archivos de registro en el nodo, compruebe los registros de Batch directamente. Use este método para los registros de Windows y Linux.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En la barra de búsqueda, escriba y seleccione **Cuentas de Batch**.

1. En la página **Cuentas de Batch**, seleccione la cuenta con el grupo de Batch.

1. En el menú de la página de la cuenta de Batch, en **Características**, seleccione **Grupos**.

1. Seleccione el nombre del grupo.

1. En el menú de la página del grupo de Batch, en **General**, seleccione **Nodos**.

1. Seleccione el nombre del nodo.

1. En la página **Información general** del nodo, seleccione **Cargar registros de lotes**.

1. En el panel **Cargar registros de lotes**, seleccione su contenedor de Azure Storage. Después, elija **Seleccionar contenedor de almacenamiento**.

1. Seleccione y descargue los archivos de registro del contenedor de almacenamiento. 

1. Abra `agent-debug.log`.

1. Revise los mensajes de error. Por ejemplo: 

    ```text
    ..20210322T113107.448Z.00000000-0000-0000-0000-000000000000.ERROR.agent.mount.filesystems.basefilesystem.basefilesystem.py.run_cmd_persist_output_async.59.2912.MainThread.3580.Mount command failed with exit code: 2, output:
    
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. Solucione el problema siguiendo las indicaciones de [Solución de problemas de Azure Files en Windows (SMB)](../storage/files/storage-troubleshoot-windows-file-connection-problems.md) o [Solución de problemas de Azure Files en Linux (SMB)](../storage/files/storage-troubleshoot-linux-file-connection-problems.md).

Si sigue sin encontrar la causa del error, puede [montar el recurso compartido de archivos manualmente con PowerShell](#manually-mount-file-share-with-powershell).

### <a name="manually-mount-file-share-with-powershell"></a>Montaje manual del recurso compartido de archivos con PowerShell

Si no consigue diagnosticar o corregir errores de montaje con PowerShell, puede montar el recurso compartido de archivos manualmente.

# <a name="windows"></a>[Windows](#tab/windows)

1. Cree un grupo sin una configuración de montaje. Por ejemplo:

    ```powershell
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1  -BatchContext $Context
    ```

1. Espere a que el nodo se encuentre en estado **Inactivo**.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En la barra de búsqueda, escriba y seleccione **Cuentas de almacenamiento**.

1. Seleccione el nombre de la cuenta de almacenamiento con el recurso compartido de archivos.

1. En el menú de la página de la cuenta de almacenamiento, en **Almacenamiento de datos**, seleccione **Recursos compartidos de archivos**.

1. En la página **Recursos compartidos de archivos**, seleccione el nombre del recurso compartido.

1. En la página **Información general** del recurso compartido de archivos, seleccione **Conectar**.

1. En el panel **Conectar**, seleccione la pestaña **Windows**.

1. En **Letra de unidad**, escriba la unidad que quiere usar. De manera predeterminada, es `Z`.

1. En **Método de autenticación**, seleccione cómo quiere conectarse al recurso compartido de archivos.

1. Copie el comando de PowerShell para montar el recurso compartido de archivos.

1. Conéctese al nodo mediante RDP.

1. Ejecute el comando que copió para montar el recurso compartido de archivos.

1. Observe los mensajes de error de la salida. Use esta información para solucionar cualquier problema relacionado con las redes.

# <a name="linux"></a>[Linux](#tab/linux)


1. Cree un grupo sin una configuración de montaje. Por ejemplo:

    ```bash
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -BatchContext $Context
    ```

1. Espere a que el nodo se encuentre en estado **Inactivo**.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En la barra de búsqueda, escriba y seleccione **Cuentas de almacenamiento**.

1. Seleccione el nombre de la cuenta de almacenamiento con el recurso compartido de archivos.

1. En el menú de la página de la cuenta de almacenamiento, en **Almacenamiento de datos**, seleccione **Recursos compartidos de archivos**.

1. En la página **Recursos compartidos de archivos**, seleccione el nombre del recurso compartido.

1. En la página **Información general** del recurso compartido de archivos, seleccione **Conectar**.

1. En el panel **Conectar**, seleccione la pestaña **Linux**.

1. Escriba el **Punto de montaje** que quiere usar.

1. Copie el comando de Linux para montar el recurso compartido de archivos.

1. Conéctese al nodo mediante SSH.

1. Ejecute el comando que copió para montar el recurso compartido de archivos.

1. Observe los mensajes de error de la salida. Use esta información para solucionar cualquier problema relacionado con las redes.


---

## <a name="examples"></a>Ejemplos

En los ejemplos de código siguientes se muestra cómo montar varios recursos compartidos de archivos en un grupo de nodos de ejecución.

### <a name="azure-files-share"></a>Recurso compartido de Azure Files

Azure Files es la oferta estándar del sistema de archivos en la nube de Azure. Para obtener información sobre los parámetros del ejemplo de código, consulte [Uso de un recurso compartido de Azure Files (SMB)](../storage/files/storage-how-to-use-files-windows.md) o [Uso de un recurso compartido de Azure Files (NFS)](../storage/files/storage-files-how-to-create-nfs-shares.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-container"></a>Contenedor de blobs de Azure

Otra opción consiste en usar Azure Blob Storage a través de [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). El montaje de un sistema de archivos de blob requiere `AccountKey`, `SasKey` o `Managed Identity` con acceso a la cuenta de almacenamiento.

Para obtener información sobre cómo obtener estas claves, consulte lo siguiente: 

- [Administración de las claves de acceso de la cuenta de almacenamiento](../storage/common/storage-account-keys-manage.md)
- [Grant limited access to Azure Storage resources using shared access signatures (SAS)](../storage/common/storage-sas-overview.md) (Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido [SAS])
- [Configuración de identidades administradas en grupos de Batch](managed-identity-pools.md) 

Para obtener más información y sugerencias sobre el uso de blobfuse, consulte [proyecto de blobfuse](https://github.com/Azure/azure-storage-fuse).

Para obtener acceso predeterminado al directorio montado de blobfuse, ejecute la tarea como **administrador**. Blobfuse monta el directorio en el espacio de usuario y, cuando se crea el grupo, se monta como raíz. En Linux, todas las tareas de **administrador** son raíz. Todas las opciones del módulo FUSE se describen en la [página de referencia de FUSE](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Revise las [Preguntas más frecuentes sobre solución de problemas](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) para obtener más información y sugerencias sobre el uso de blobfuse. También puede revisar los [problemas de GitHub en el repositorio de blobfuse](https://github.com/Azure/azure-storage-fuse/issues) para comprobar los problemas y las resoluciones actuales de blobfuse.

> [!NOTE]
> En el ejemplo siguiente se muestran `AccountKey`, `SasKey` y `IdentityReference`, pero son mutuamente excluyentes, por lo que solo se puede especificar uno.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "SasKey",
                IdentityReference = new ComputeNodeIdentityReference("/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name"),
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

> [!TIP]
>Si usa una identidad administrada, asegúrese de que la identidad se ha [asignado al grupo](managed-identity-pools.md) para que esté disponible en la máquina virtual que hace el montaje. La identidad deberá tener el rol `Storage Blob Data Contributor` para funcionar correctamente.

### <a name="network-file-system"></a>Network File System

Los sistemas de Network File System (NFS) se pueden montar en nodos de grupo, lo que permite Azure Batch pueda tener acceso a los sistemas de archivos tradicionales. Esta configuración se puede realizar con un solo servidor NFS implementado en la nube o un servidor NFS local al que se tiene acceso a través de una red virtual. Los montajes NFS admiten [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md). Avere vFXT es una solución de caché en memoria distribuida para tareas de informática de alto rendimiento (HPC) con uso intensivo de datos y otras interfaces estándar compatibles con NFS. Por ejemplo, [NFS para Azure Blob](../storage/blobs/network-file-system-protocol-support.md) y [NFS para Azure Files](../storage/files/storage-files-how-to-mount-nfs-shares.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=3.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Sistema de archivos de Internet común

El montaje de [sistemas de archivos de Internet común (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) en nodos de grupo es otra manera de proporcionar acceso a los sistemas de archivos tradicionales. CIFS es un protocolo de uso compartido de archivos que proporciona un mecanismo abierto y multiplataforma para solicitar servicios y archivos de servidor de red. CIFS se basa en la versión mejorada del [protocolo SMB](/windows-server/storage/file-server/file-server-smb-overview), que está diseñado para el uso compartido de archivos de Internet e intranet.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnóstico de errores de montaje

Si se produce un error en una configuración de montaje, se producirá un error en el nodo de ejecución del grupo y el estado del nodo pasará a ser `unusable`. Para diagnosticar un error de configuración de montaje, inspeccione la propiedad [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) para obtener más información sobre el error.

Para obtener los archivos de registro para la depuración, use [OutputFiles](batch-task-output-files.md) para cargar los archivos `*.log`. Los archivos `*.log` contienen información sobre el montaje del sistema de archivos en la ubicación `AZ_BATCH_NODE_MOUNTS_DIR`. Los archivos de registro de montaje tienen el formato `<type>-<mountDirOrDrive>.log` para cada montaje. Por ejemplo, un montaje `cifs` en un directorio de montaje denominado `test` tendrá un archivo de registro de montaje denominado `cifs-test.log`.

## <a name="support-matrix"></a>Matrices compatibles

Azure Batch admite los siguientes tipos de sistemas de archivos virtuales para los agentes de nodo generados para sus respectivos publicador y oferta.

| Tipo de SO | Recurso compartido de Azure Files | Contenedor de blobs de Azure | Montaje de NFS | Montaje de CIFS |
|---|---|---|---|---|
| Linux | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="networking-requirements"></a>Requisitos de red

Al usar montajes de archivos virtuales con [grupos de Azure Batch en una red virtual](batch-virtual-network.md), tenga en cuenta los siguientes requisitos y asegúrese de que no se bloquee el tráfico necesario.

- **Recursos compartidos de archivos de Azure**:
  - Requiere que el puerto TCP 445 esté abierto para el tráfico hacia y desde la etiqueta de servicio "storage". Para más información, consulte [Uso de un recurso compartido de archivos de Azure con Windows](../storage/files/storage-how-to-use-files-windows.md#prerequisites).
- **Contenedores de blobs de Azure**:
  - Requiere que el puerto TCP 443 esté abierto para el tráfico hacia y desde la etiqueta de servicio "storage".
  - Las máquinas virtuales deben tener acceso a https://packages.microsoft.com para descargar los paquetes blobfuse y gpg. En función de la configuración, es posible que también necesite acceder a otras direcciones URL para descargar más paquetes.
- **Network File System (NFS)** :
  - Requiere acceso al puerto 2049 (de manera predeterminada; su configuración podría tener otros requisitos).
  - Las máquinas virtuales deben tener acceso al administrador de paquetes adecuado para descargar los paquetes `nfs-common` (para Debian o Ubuntu) o `nfs-utils` (para CentOS). Esta dirección URL puede variar según la versión del sistema operativo. En función de la configuración, es posible que también necesite acceder a otras direcciones URL para descargar otros paquetes.
  - El montaje de Azure Blob o Azure Files mediante NFS podría tener más requisitos de red. Por ejemplo, puede que necesite nodos de proceso que compartan la misma subred de una red virtual que la cuenta de almacenamiento.
- **Sistema de archivos de Internet común (CIFS)** :
  - Requiere acceso al puerto TCP 445.
  - Las máquinas virtuales deben tener acceso a los administradores de paquetes adecuados para descargar el paquete `cifs-utils`. Esta dirección URL puede variar según la versión del sistema operativo.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre el montaje de un recurso compartido de Azure Files con [Windows](../storage/files/storage-how-to-use-files-windows.md) o [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Obtenga información sobre el uso y el montaje de sistemas de archivos virtuales de [blobfuse](https://github.com/Azure/azure-storage-fuse).
- Consulte [Network File System overview](/windows-server/storage/nfs/nfs-overview) (Introducción a Network File System) para obtener información sobre NFS y sus aplicaciones.
- Consulte [Microsoft SMB protocol and CIFS protocol overview](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) (Introducción a los protocolos CIFS y SMB de Microsoft) para obtener más información sobre CIFS.