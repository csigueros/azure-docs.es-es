---
title: Implementación de VM en el dispositivo Azure Stack Edge mediante Azure PowerShell
description: Se describe cómo crear y administrar máquinas virtuales en un dispositivo Azure Stack Edge mediante Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32d2102bad49a3023df9b1d25f5b5378a3b30ad0
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721498"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Implementación de VM en el dispositivo Azure Stack Edge mediante Azure PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo crear y administrar una máquina virtual en el dispositivo Azure Stack Edge mediante Azure PowerShell.

## <a name="vm-deployment-workflow"></a>Flujo de trabajo de implementación de una máquina virtual

El flujo de trabajo de implementación general de la implementación de máquina virtual es el siguiente:

1. Conéctese a Azure Resource Manager local en el dispositivo.
1. Identifique la suscripción integrada en el dispositivo.
1. Traiga su imagen de máquina virtual.
1. Cree un grupo de recursos en la suscripción integrada. El grupo de recursos contendrá la máquina virtual y todos los recursos relacionados.  
1. Cree una cuenta de almacenamiento local en el dispositivo para almacenar el disco duro virtual que se usará para crear una imagen de máquina virtual.
1. Cargue una imagen de origen de Windows o Linux en la cuenta de almacenamiento para crear un disco administrado.
1. Use el disco administrado para crear una imagen de máquina virtual.
1. Habilite el proceso en un puerto de dispositivo para crear un conmutador virtual.
1. Se crea una red virtual mediante el conmutador virtual conectado al puerto en el que ha habilitado el proceso.
1. Cree una máquina virtual con la imagen de máquina virtual, la red virtual y las interfaces de red virtual creadas anteriormente para comunicarse dentro de la red virtual y asigne una dirección IP pública para acceder de forma remota a la máquina virtual. También puede incluir discos de datos para proporcionar más almacenamiento para la máquina virtual.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>Consulta de la suscripción integrada en el dispositivo

Para Azure Resource Manager, solo se admite una única suscripción fija que es visible por el usuario. Esta suscripción es única por dispositivo y no se pueden cambiar el nombre y el identificador de suscripción.

La suscripción contiene todos los recursos necesarios para la creación de máquinas virtuales. 

> [!IMPORTANT]
> La suscripción se crea al habilitar las máquinas virtuales desde Azure Portal y reside localmente en el dispositivo.

La suscripción se usa para implementar las máquinas virtuales.

### <a name="az"></a>[Az](#tab/az)

1.  Para enumerar la suscripción, ejecute el siguiente comando:

    ```powershell
    Get-AzSubscription
    ```
    
    A continuación se muestra una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> Get-AzSubscription
    
    Name                          Id                                   TenantId
    ----                          --                                   --------
    Default Provider Subscription ...                                  ...
    
    
    PS C:\WINDOWS\system32>
    ```
        
1. Obtenga la lista de los proveedores de recursos registrados que se ejecutan en el dispositivo. Normalmente, la lista incluye proceso, red y almacenamiento.

    ```powershell
    Get-AzResourceProvider
    ```

    > [!NOTE]
    > Los proveedores de recursos están registrados previamente y no se pueden modificar ni cambiar.
    
    A continuación se muestra una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32>  Get-AzResourceProvider
        
    ProviderNamespace : Microsoft.AzureBridge
    RegistrationState : Registered
    ResourceTypes     : {locations, operations, locations/ingestionJobs}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

1.  Para enumerar la suscripción, ejecute el siguiente comando:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    A continuación se muestra una salida de ejemplo:

    ```output
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription         ...                 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. Obtenga la lista de los proveedores de recursos registrados que se ejecutan en el dispositivo. Normalmente, la lista incluye proceso, red y almacenamiento.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Los proveedores de recursos están registrados previamente y no se pueden modificar ni cambiar.
    
    A continuación se muestra una salida de ejemplo:

    ```output
    PS C:\Windows\system32> Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
---
   
## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Empiece por crear un nuevo grupo de recursos de Azure y úselo como contenedor lógico para todos los recursos relacionados con la máquina virtual, como la cuenta de almacenamiento, el disco, la interfaz de red y el disco administrado.

> [!IMPORTANT]
> Todos los recursos se crean en la misma ubicación que el dispositivo, y la ubicación se establece en **DBELocal**.

### <a name="az"></a>[Az](#tab/az)

1. Configure algunos parámetros.

    ```powershell
    $ResourceGroupName = "<Resource group name>" 
    ```
1. Cree un grupo de recursos para los recursos que va a crear para la máquina virtual.
   
    ```powershell
    New-AzResourceGroup -Name $ResourceGroupName -Location DBELocal
    ```

    A continuación se muestra una salida de ejemplo:
    
    ```output
    PS C:\WINDOWS\system32> New-AzResourceGroup -Name myaseazrg -Location DBELocal
    
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/.../resourceGroups/myaseazrg
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

A continuación se muestra una salida de ejemplo:

```output
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```
---

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Cree una nueva cuenta de almacenamiento mediante el grupo de recursos creado en el paso anterior. Se trata de una cuenta de almacenamiento local que se usa para cargar la imagen de disco virtual para la VM.

### <a name="az"></a>[Az](#tab/az)

1. Configure algunos parámetros.

    ```powershell
    $StorageAccountName = "<Storage account name>"    
    ```

1. Cree una nueva cuenta de almacenamiento local en el dispositivo.

    ```powershell
    New-AzStorageAccount -Name $StorageAccountName -ResourceGroupName $ResourceGroupName -Location DBELocal -SkuName Standard_LRS
    ```
    
    > [!NOTE]
    > Con Azure Resource Manager, solo puede crear cuentas de almacenamiento local, como almacenamiento con redundancia local (estándar o prémium). Para crear cuentas de almacenamiento en niveles, consulte [Tutorial: Transferencia de datos a través de cuentas de almacenamiento de Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-add-storage-accounts.md).

    Este es un ejemplo de salida:
    
    ```output
    PS C:\WINDOWS\system32> New-AzStorageAccount -Name myaseazsa -ResourceGroupName myaseazrg -Location DBELocal -SkuName Standard_LRS
    
    StorageAccountName ResourceGroupName PrimaryLocation SkuName      Kind    AccessTier CreationTime
    ------------------ ----------------- --------------- -------      ----    ---------- ------------
    myaseazsa          myaseazrg         DBELocal        Standard_LRS Storage            6/10/2021 11:45...
    
    PS C:\WINDOWS\system32>
    ```

1. Obtenga la clave de la cuenta de almacenamiento de la cuenta que creó en el paso anterior. Cuando se le solicite, proporcione el nombre del grupo de recursos y el nombre de la cuenta de almacenamiento.

    ```powershell
    Get-AzStorageAccountKey
    ``` 

    Este es un ejemplo de salida:

    ```output
    PS C:\WINDOWS\system32> Get-AzStorageAccountKey
    
    cmdlet Get-AzStorageAccountKey at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    ResourceGroupName: myaseazrg
    Name: myaseazsa
    
    KeyName Value                                                                                    Permissions
    ------- -----                                         ------
    key1    gv3OF57tuPDyzBNc1M7fhil2UAiiwnhTT6zgiwE3TlF/CD217Cvw2YCPcrKF47joNKRvzp44leUe5HtVkGx8RQ==   Full
    key2    kmEynIs3xnpmSxWbU41h5a7DZD7v4gGV3yXa2NbPbmhrPt10+QmE5PkOxxypeSqbqzd9si+ArNvbsqIRuLH2Lw==   Full
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Con Azure Resource Manager, solo puede crear cuentas de almacenamiento local, como almacenamiento con redundancia local (estándar o prémium). Para crear cuentas de almacenamiento en niveles, consulte [Tutorial: Transferencia de datos a través de cuentas de almacenamiento de Azure Stack Edge Pro con GPU](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).

A continuación se muestra una salida de ejemplo:

```output
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/.../resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Para obtener la clave de la cuenta de almacenamiento, ejecute el comando `Get-AzureRmStorageAccountKey`. A continuación se muestra una salida de ejemplo:

```output
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```
---

## <a name="add-the-blob-uri-to-the-host-file"></a>Adición del URI del blob al archivo de host

Ya ha agregado el identificador URI del blob en el archivo de hosts del cliente que usa para conectarse a Azure Blob Storage en **Modificación del archivo de host para la resolución de nombres de punto de conexión** de [Conexión a Azure Resource Manager en un dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Esta entrada se usó para agregar el URI del blob:

`<Device IP address>` `<storage name>.blob.<appliance name>.<dnsdomain>`

## <a name="install-certificates"></a>Instalación de certificados

Si usa HTTPS, tendrá que instalar los certificados adecuados en el dispositivo. En este caso, instale el certificado del punto de conexión de blob. Para más información, consulte [Uso de certificados con el dispositivo Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-manage-certificates.md).

## <a name="upload-a-vhd"></a>Carga de un disco duro virtual

Copie las imágenes de disco que se van a usar en blobs en páginas en la cuenta de almacenamiento local que creó anteriormente. Puede usar una herramienta como [AzCopy](../storage/common/storage-use-azcopy-v10.md) para cargar el disco duro virtual (VHD) en la cuenta de almacenamiento. 


### <a name="az"></a>[Az](#tab/az)

Use los comandos siguientes con AzCopy 10:  

1. Establezca algunos parámetros, incluida la versión adecuada de las API para AzCopy. En este ejemplo, se ha utilizado AzCopy 10.

    ```powershell
    $Env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2019-07-07"    
    $ContainerName = <Container name>
    $ResourceGroupName = <Resource group name>
    $StorageAccountName = <Storage account name>
    $VHDPath = "Full VHD Path"
    $VHDFile = <VHD file name>
    ```
1. Copie el disco duro virtual del origen (en este caso, el sistema local) en la cuenta de almacenamiento que creó en el dispositivo en el paso anterior.

    ```powershell
    $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value    
    $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob    
    $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint    
    $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly        
    <Path to azcopy.exe> cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"    
    ```

    Este es un ejemplo de salida: 
    
    ```output
    PS C:\windows\system32> $ContainerName = "testcontainer1"
    PS C:\windows\system32> $ResourceGroupName = "myaseazrg"
    PS C:\windows\system32> $StorageAccountName = "myaseazsa"
    PS C:\windows\system32> $VHDPath = "C:\Users\alkohli\Downloads\Ubuntu1604"           
    PS C:\windows\system32> $VHDFile = "ubuntu13.vhd"
    
    PS C:\windows\system32> $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value
    PS C:\windows\system32> $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob
    PS C:\windows\system32> $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint
    PS C:\windows\system32> $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

    PS C:\windows\system32> C:\azcopy\azcopy_windows_amd64_10.10.0\azcopy.exe cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
    INFO: Scanning...
    INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support
    
    Job 72a5e3dd-9210-3e43-6691-6bebd4875760 has started
    Log file is located at: C:\Users\alkohli\.azcopy\72a5e3dd-9210-3e43-6691-6bebd4875760.log
    
    INFO: azcopy.exe: A newer version 10.11.0 is available to download
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

Use los comandos siguientes con AzCopy 10:  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

A continuación se muestra una salida de ejemplo: 

```output
$ContainerName = <Container name>
$ResourceGroupName = <Resource group name>
$StorageAccountName = <Storage account name>
$VHDPath = "Full VHD path"
$VHDFile = <VHD file name>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```
---

## <a name="create-a-managed-disk-from-the-vhd"></a>Creación de un disco administrado a partir del VHD

Ahora, creará un disco administrado a partir del disco duro virtual cargado.

### <a name="az"></a>[Az](#tab/az)

1. Configure algunos parámetros.

    ```powershell
    $DiskName = "<Managed disk name>"
    ```

1. Cree un disco administrado a partir del VHD cargado. Para obtener la dirección URL de origen del disco duro virtual, vaya al contenedor de la cuenta de almacenamiento que contiene el VHD en el Explorador de Storage. Seleccione el VHD, haga clic con el botón derecho y seleccione **Propiedades**. En el cuadro de diálogo **Propiedades del blob**, seleccione el **Identificador URI**. 

    ```powershell
    $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id    
    $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "Source URL for your VHD"
    New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    ```
    Este es un ejemplo de salida:
    
    ```output
    PS C:\WINDOWS\system32> $DiskName = "myazmd"
    PS C:\WINDOWS\system32> $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id
    PS C:\WINDOWS\system32> $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "https://myaseazsa.blob.myasegpu.wdshcsso.com/testcontainer1/ubuntu13.vhd"
    PS C:\WINDOWS\system32> New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    
    ResourceGroupName            : myaseazrg
    ManagedBy                    :
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    Zones                        :
    TimeCreated                  : 6/24/2021 12:19:56 PM
    OsType                       :
    HyperVGeneration             :
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationDat
                                   a
    DiskSizeGB                   : 30
    DiskSizeBytes                : 32212254720
    UniqueId                     : 53743801-cbf2-4d2f-acb4-971d037a9395
    EncryptionSettingsCollection :
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/.../r
                                   esourceGroups/myaseazrg/providers/Microsoft.Compute/d
                                   isks/myazmd
    Name                         : myazmd
    Type                         : Microsoft.Compute/disks
    Location                     : DBELocal
    Tags                         : {}
    
    PS C:\WINDOWS\system32>
    ```
 

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
A continuación se muestra una salida de ejemplo: 

```output
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd
```
 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

A continuación se muestra una salida de ejemplo. Para más información sobre este cmdlet, consulte [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

```output
Tags               : New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/.../resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```
---

## <a name="create-a-vm-image-from-the-managed-disk"></a>Creación de una imagen de máquina virtual a partir del disco administrado

Ahora, va a crear una imagen de máquina virtual a partir del disco administrado.

### <a name="az"></a>[Az](#tab/az)

1. Configure algunos parámetros.

    ```powershell
    $DiskSize = "<Size greater than or equal to size of source managed disk>"
    $OsType = "<linux or windows>" 
    $ImageName = "<Image name>"
    ```
1. Cree una imagen de máquina virtual. Los tipos de sistema operativo admitidos son Linux y Windows.

    ```powershell
    $imageConfig = New-AzImageConfig -Location DBELocal
    $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId 
    New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName  
    ```  
    Este es un ejemplo de salida. 

    ```output
    PS C:\WINDOWS\system32> $OsType = "linux"
    PS C:\WINDOWS\system32> $ImageName = "myaseazlinuxvmimage"
    PS C:\WINDOWS\system32> $DiskSize = 35
    PS C:\WINDOWS\system32> $imageConfig = New-AzImageConfig -Location DBELocal
    PS C:\WINDOWS\system32> $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId
    
    ResourceGroupName    :
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    :
    HyperVGeneration     : V1
    Id                   :
    Name                 :
    Type                 :
    Location             : DBELocal
    Tags                 :
    
    PS C:\WINDOWS\system32> New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName
    
    ResourceGroupName    : myaseazrg
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    : Succeeded
    HyperVGeneration     : V1
    Id                   : /subscriptions/.../resourceG
                           roups/myaseazrg/providers/Microsoft.Compute/images/myaseazlin
                           uxvmimage
    Name                 : myaseazlinuxvmimage
    Type                 : Microsoft.Compute/images
    Location             : dbelocal
    Tags                 : {}
    
    PS C:\WINDOWS\system32> 
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

Ejecute el siguiente comando. Reemplace *\<Disk name>* , *\<OS type>* y *\<Disk size>* por los valores reales.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Los tipos de sistema operativo admitidos son Linux y Windows.

A continuación se muestra una salida de ejemplo. Para más información sobre este cmdlet, consulte [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

```output
PS C:\Windows\system32> New-AzImage -Image $imageConfig -ImageName ig191113014333    -ResourceGroupName RG191113014333
ResourceGroupName    : RG191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
HyperVGeneration     : V1
Id                   : /subscriptions/.../resourceGroups/RG191113014333/providers/Microsoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```
---

## <a name="create-your-vm-with-previously-created-resources"></a>Creación de la máquina virtual con los recursos creados anteriormente

Antes de crear e implementar la máquina virtual, debe crear una red virtual y asociarla una interfaz de red virtual.

> [!IMPORTANT]
> Se aplican las reglas siguientes:
> - Solo puede crear una red virtual, incluso entre grupos de recursos. La red virtual debe tener exactamente el mismo espacio de direcciones que la red lógica.
> - Una red virtual solo puede tener una subred. La subred debe tener exactamente el mismo espacio de direcciones que la red virtual.
> - Cuando se crea la tarjeta de interfaz de red virtual, solo se puede usar el método de asignación estática. El usuario debe proporcionar una dirección IP privada.<!--Confirm w/ Neeraj given we can have both static and DHCP-->

### <a name="query-the-automatically-created-virtual-network"></a>Consulta a la red virtual creada automáticamente

Cuando se habilita el proceso desde la interfaz de usuario local del dispositivo, se crea automáticamente la red virtual denominada `ASEVNET` en el grupo de recursos `ASERG`. 

### <a name="az"></a>[Az](#tab/az)

Use el siguiente comando para consultar la red virtual existente:

```powershell
$ArmVn = Get-AzVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

Use el siguiente comando para consultar la red virtual existente:

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```
---

### <a name="create-a-virtual-network-interface-card"></a>Creación de una tarjeta de interfaz de red virtual

Va a crear una tarjeta de interfaz de red virtual mediante el identificador de subred de la red virtual.

### <a name="az"></a>[Az](#tab/az)

1. Configure algunos parámetros.

    ```powershell
    $IpConfigName = "<IP config name>"
    $NicName = "<Network interface name>"
    ```

1. Cree una interfaz de red virtual.

    ```powershell
    $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig    
    ```

    De manera predeterminada, se asigna dinámicamente una dirección IP a la interfaz de red desde la red habilitada para proceso. Use el parámetro `-PrivateIpAddress parameter` si va a asignar una dirección IP estática a la interfaz de red.         

    Este es un ejemplo de salida:
    
    ```output
    PS C:\WINDOWS\system32> $IpConfigName = "myazipconfig1"
    PS C:\WINDOWS\system32> $NicName = "myaznic1"
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig
    PS C:\WINDOWS\system32> $Nic
        
    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
                                      },
                                      "ProvisioningState": "Succeeded",
                                      "PrivateIpAddressVersion": "IPv4",
                                      "LoadBalancerBackendAddressPools": [],
                                      "LoadBalancerInboundNatRules": [],
                                      "Primary": true,
                                      "ApplicationGatewayBackendAddressPools": [],
                                      "ApplicationSecurityGroups": []
                                    }
                                  ]
    DnsSettings                 : {
                                    "DnsServers": [],
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
           
    PS C:\WINDOWS\system32>
    ```

Opcionalmente, al crear una tarjeta de interfaz de red virtual para una máquina virtual, puede pasar la dirección IP pública. En este caso, la dirección IP pública devuelve la dirección IP privada. 

```powershell
New-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

De manera predeterminada, se asigna dinámicamente una dirección IP a la interfaz de red desde la red habilitada para proceso. Use el parámetro `-PrivateIpAddress parameter` si va a asignar una dirección IP estática a la interfaz de red. 

A continuación se muestra una salida de ejemplo:

```output
PS C:\windows\system32> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\windows\system32> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\windows\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\windows\system32> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\windows\system32> $Nic

PS C:\windows\system32> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/.../resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Opcionalmente, al crear una tarjeta de interfaz de red virtual para una máquina virtual, puede pasar la dirección IP pública. En este caso, la dirección IP pública devuelve la dirección IP privada. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```
---

### <a name="create-a-vm"></a>Crear una VM

Ahora puede usar la imagen de la VM para crear una VM y conectarla a la red virtual que creó anteriormente.

### <a name="az"></a>[Az](#tab/az)

1. Establezca el nombre de usuario y la contraseña para iniciar sesión en la máquina virtual que desea crear.

    ```powershell
    $pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
    $cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
    ```
    Después de crear y encender la máquina virtual, usará el nombre de usuario y contraseña anteriores para iniciar sesión en ella.

1. Establezca los parámetros.

    ```powershell
    $VmName = "<VM name>"
    $ComputerName = "<VM display name>"
    $OsDiskName = "<OS disk name>"
    ```
1. Cree la máquina virtual.

    ```powershell
    $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
 
    $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
     
    $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
     
    $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
     
    $VirtualMachine =  Add-AzVMNetworkInterface -Vm $VirtualMachine -Id $nicID
     
    $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
     
    $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
     
    New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    ```

    Este es un ejemplo de salida.
    
    ```powershell
    PS C:\WINDOWS\system32> $pass = ConvertTo-SecureString "Password1" -AsPlainText -Force;
    PS C:\WINDOWS\system32> $cred = New-Object System.Management.Automation.PSCredential("myazuser", $pass)
    PS C:\WINDOWS\system32> $VmName = "myazvm"
    >> $ComputerName = "myazvmfriendlyname"
    >> $OsDiskName = "myazosdisk1"
    PS C:\WINDOWS\system32> $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
    PS C:\WINDOWS\system32> $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> $nicID/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.Network/networkInterfaces/myaznic1
    PS C:\WINDOWS\system32> $VirtualMachine =  Add-AzVMNetworkInterface -VM $VirtualMachine -Id $nicID
    PS C:\WINDOWS\system32> $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
    PS C:\WINDOWS\system32> New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    WARNING: Since the VM is created using premium storage or managed disk, existing
    standard storage account, myaseazsa, is used for boot diagnostics.
    VERBOSE: Performing the operation "New" on target "myazvm".
    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```
1. Para averiguar la dirección IP asignada a la máquina virtual que ha creado, consulte la interfaz de red virtual que ha creado. Busque el elemento `PrivateIPAddress` y copie la dirección IP de la máquina virtual. Este es un ejemplo de salida.

    ```powershell
    PS C:\WINDOWS\system32> $Nic

    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
                                      },
                                      "ProvisioningState": "Succeeded",
                                      "PrivateIpAddressVersion": "IPv4",
                                      "LoadBalancerBackendAddressPools": [],
                                      "LoadBalancerInboundNatRules": [],
                                      "Primary": true,
                                      "ApplicationGatewayBackendAddressPools": [],
                                      "ApplicationSecurityGroups": []
                                    }
                                  ]
    DnsSettings                 : {
                                    "DnsServers": [],
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
      
    PS C:\WINDOWS\system32>
    ```


### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

Después de crear y encender la máquina virtual, usará el siguiente nombre de usuario y contraseña para iniciar sesión en ella.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```
---

## <a name="connect-to-the-vm"></a>Conexión a la máquina virtual

Las instrucciones para conectarse pueden ser diferentes en función de si se ha creado una máquina virtual Windows o Linux.

## <a name="connect-to-a-linux-vm"></a>Conexión a una máquina virtual Linux

Para conectarse a una máquina virtual Linux, realice lo siguiente:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

Si usó una dirección IP pública durante la creación de la máquina virtual, puede usar esa IP para conectarse a la máquina virtual. Para obtener la dirección IP pública, ejecute el siguiente comando: 

### <a name="az"></a>[Az](#tab/az)

```powershell
$publicIp = Get-AzPublicIpAddress -Name $PublicIp -ResourceGroupName $ResourceGroupName
```
En este caso, la dirección IP pública será la misma que la dirección IP privada que pasó durante la creación de la interfaz de red virtual.
  
### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
En este caso, la dirección IP pública será la misma que la dirección IP privada que pasó durante la creación de la interfaz de red virtual.

---

## <a name="connect-to-a-windows-vm"></a>Conexión a una máquina virtual Windows

Para conectarse a una máquina virtual Windows, realice lo siguiente:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


## <a name="manage-the-vm"></a>Administración de la VM

En las siguientes secciones se describen algunas de las operaciones comunes que puede crear en el dispositivo Azure Stack Edge Pro.

### <a name="list-vms-that-are-running-on-the-device"></a>Enumeración de las máquinas virtuales que se ejecutan en el dispositivo

Para devolver una lista de todas las máquinas virtuales que se ejecutan en el dispositivo Azure Stack Edge, ejecute este comando:

### <a name="az"></a>[Az](#tab/az)

```powershell
Get-AzVM -ResourceGroupName <String> -Name <String>
```

Para más información sobre este cmdlet, consulte [Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-6.1.0&preserve-view=true).

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Get-AzureRmVM -ResourceGroupName <String> -Name <String>
```

---


### <a name="turn-on-the-vm"></a>Activación de la VM

Ejecute el siguiente cmdlet para activar una máquina virtual que se ejecute en el dispositivo:

### <a name="az"></a>[Az](#tab/az)

```powershell
Start-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
Para más información sobre este cmdlet, consulte [Start-AzVM](/powershell/module/az.compute/start-azvm?view=azps-5.9.0&preserve-view=true).

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Para más información sobre este cmdlet, consulte [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

---

### <a name="suspend-or-shut-down-the-vm"></a>Suspensión o apagado de la VM

Ejecute el siguiente cmdlet para detener o apagar una máquina virtual que se ejecute en el dispositivo:

### <a name="az"></a>[Az](#tab/az)

```powershell
Stop-AzVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Para más información sobre este cmdlet, consulte el cmdlet [Stop-AzVM](/powershell/module/az.compute/stop-azvm?view=azps-5.9.0&preserve-view=true).

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Para más información sobre este cmdlet, consulte [Cmdlet Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

---

### <a name="add-a-data-disk"></a>Agregar un disco de datos

Si aumentan los requisitos de la carga de trabajo en la máquina virtual, es posible que tenga que agregar un disco de datos. Para ello, ejecute el siguiente comando:

### <a name="az"></a>[Az](#tab/az)

```powershell
Add-AzRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```
---



### <a name="delete-the-vm"></a>Eliminación de la máquina virtual

Ejecute el siguiente cmdlet para quitar una máquina virtual del dispositivo:

### <a name="az"></a>[Az](#tab/az)

```powershell
Remove-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
Para más información sobre este cmdlet, consulte el cmdlet [Remove-AzVm](/powershell/module/az.compute/remove-azvm?view=azps-5.9.0&preserve-view=true).

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```
Para más información sobre este cmdlet, consulte [Cmdlet Remove-AzureRmVm](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).

---

## <a name="next-steps"></a>Pasos siguientes

[Cmdlets de Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
