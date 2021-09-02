---
title: Implementación de un grupo de discos de Azure (versión preliminar)
description: Aprenda a implementar un grupo de discos de Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f0f648f7f8c04aeb6ed97a0c58fa157e743da5f1
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436976"
---
# <a name="deploy-an-azure-disk-pool-preview"></a>Implementación de un grupo de discos de Azure (versión preliminar)

En este artículo se explica cómo implementar y configurar un grupo de discos de Azure (versión preliminar). Antes de implementar un grupo de discos, lea los artículos [conceptuales](disks-pools.md) y de [planeamiento](disks-pools-planning.md).

Para que un grupo de discos funcione correctamente, debe completar los pasos siguientes:
- Registre la suscripción para la versión preliminar.
- Delegue una subred en el grupo de discos.
- Asigne al proveedor de recursos los permisos de control de acceso basado en rol del grupo de discos para administrar los recursos del disco.
- Cree el grupo de discos.
    - Agregue discos al grupo de discos.


## <a name="prerequisites"></a>Requisitos previos

Para implementar correctamente un grupo de discos, debe tener:

- Un conjunto de discos administrados que desee agregar a un grupo de discos.
- Una red virtual con una subred dedicada implementada para el grupo de discos.

Si va a usar el módulo de Azure PowerShell, instale la [versión 6.1.0 u otra más reciente](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true).

Si va a usar la CLI de Azure, [instale la versión más reciente](/cli/azure/disk-pool?view=azure-cli-latest).

## <a name="register-your-subscription-for-the-preview"></a>Registro de la suscripción para la versión preliminar

Registre su suscripción en el proveedor **Microsoft.StoragePool** para poder crear y usar grupos de discos.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. En el menú Azure Portal, busque y seleccione **Suscripciones**.
1. Seleccione la suscripción que quiere usar para los grupos de discos.
1. En el menú de la izquierda, en **Configuración**, seleccione **Proveedores de recursos**.
1. Busque el proveedor de recursos **Microsoft.StoragePool** y seleccione **Registrar**.

Una vez registrada la suscripción, puede implementar un grupo de discos.

## <a name="delegate-subnet-permission"></a>Delegación del permiso de subred

Para que el grupo de discos funcione con las máquinas cliente, debe delegar una subred en el grupo de discos de Azure. Al crear un grupo de discos, especifique una red virtual y la subred delegada. Puede crear una subred o usar una existente y delegar en el proveedor de recursos **Microsoft.StoragePool/diskPools**.

1. Vaya al panel de redes virtuales de Azure Portal y seleccione la red virtual que se usará para el grupo de discos.
1. Seleccione **Subredes** en el panel de redes virtuales y seleccione **+Subred**.
1. Cree una subred completando los siguientes campos obligatorios en el panel **Agregar subred**: Delegación de subred: Seleccione Microsoft.StoragePool.

Para más información sobre la delegación de subred, consulte [Agregar o quitar una delegación de subred](../virtual-network/manage-subnet-delegation.md).

## <a name="assign-storagepool-resource-provider-permissions"></a>Asignación de permisos al proveedor de recursos StoragePool

Para que un disco pueda usarse en un grupo de discos, debe cumplir los siguientes requisitos:

- Se debe haber asignado al proveedor de recursos **StoragePool** un rol RBAC que contenga los permisos de **Lectura** y **Escritura** de todos los discos administrados del grupo de discos.
- Debe ser un disco SSD prémium o Ultra en la misma zona de disponibilidad que el grupo de discos.
    - En el caso de los discos Ultra, debe tener un tamaño de sector de disco de 512 bytes.
- Debe ser un disco compartido con un valor de maxShares de dos o más.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Busque y seleccione el grupo de recursos que contiene los discos o cada disco.
1. Seleccione **Access Control (IAM)** .
1. En la página **Agregar asignación de roles (versión preliminar)** , seleccione **Colaborador de la máquina virtual** en la lista de roles.

    Si lo prefiere, puede crear su propio rol personalizado. Un rol personalizado para los grupos de discos debe tener los siguientes permisos de RBAC para que funcione: **Microsoft.Compute/disks/write** y **Microsoft.Compute/disks/read**.

1. En **Asignar acceso a**, seleccione **User, group, or service principle** (Usuario, grupo o entidad de servicio).
1. Seleccione **+ Seleccionar miembros** y, a continuación, busque el **proveedor de recursos StoragePool**, selecciónelo y guárdelo.

## <a name="create-a-disk-pool"></a>Creación de un grupo de discos

Para obtener un rendimiento óptimo, implemente el grupo de discos en la misma zona de disponibilidad de los clientes. Si va a implementar un grupo de discos para una nube de Azure VMware Solution y necesita instrucciones sobre cómo identificar la zona de disponibilidad, rellene este [formulario](https://aka.ms/DiskPoolCollocate).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Busque y seleccione **Grupo de discos**.
1. Seleccione **+ Agregar** para crear un nuevo grupo de discos.
1. Rellene los detalles solicitados y seleccione la misma región y zona de disponibilidad que los clientes que usarán el grupo de discos.
1. Seleccione la subred que se ha delegado al proveedor de recursos **StoragePool** y su red virtual asociada.
1. Seleccione **Siguiente** para agregar discos al grupo.

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool.png" alt-text="Captura de pantalla del panel básico para crear un grupo de discos.":::

### <a name="add-disks"></a>Agrega discos.

#### <a name="prerequisites"></a>Requisitos previos

Para agregar un disco, debe cumplir los siguientes requisitos:

- Debe ser un disco SSD prémium o Ultra en la misma zona de disponibilidad que el grupo de discos.
    - Actualmente, solo puede agregar discos SSD prémium en el portal. Los discos Ultra deben agregarse con el módulo de Azure PowerShell o la CLI de Azure.
    - En el caso de los discos Ultra, debe tener un tamaño de sector de disco de 512 bytes.
- Debe ser un disco compartido con un valor de maxShares de dos o más.
- Debe conceder permisos de RBAC al proveedor de recursos del grupo de discos para administrar el disco que planea agregar.

Si el disco cumple estos requisitos, puede agregarlo a un grupo de discos seleccionando **+Agregar disco** en el panel del grupo de discos.

:::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-disks-blade.png" alt-text="Captura de pantalla del panel de disco para crear un grupo de discos, con la opción Agregar disco resaltada.":::

### <a name="enable-iscsi"></a>Habilitación de iSCSI

1. Seleccione el panel **iSCSI**.
1. Seleccione **Habilitar iSCSI**.
1. Escriba el nombre del destino iSCSI, el IQN de destino iSCSI se generará en función de este nombre.
    - Si desea deshabilitar el destino iSCSI para un disco individual, seleccione **Deshabilitar** en **Estado** de un disco individual.
    - El modo ACL se establece en **Dinámico** de forma predeterminada. Para usar el grupo de discos como una solución de almacenamiento de Azure VMware Solution, el modo ACL debe establecerse en **Dinámico**.
1. Seleccione **Revisar + crear**.

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-iscsi-blade.png" alt-text="Captura de pantalla del panel iSCSI para crear un grupo de discos.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

El script proporcionado realiza los siguientes pasos:
- Instala el módulo necesario para crear y usar grupos de discos.
- Crea un disco y le asigna permisos de RBAC. Si ya ha hecho esto, puede convertir en comentario estas secciones del script.
- Crea un grupo de discos y le agrega el disco.
- Crea y habilita un destino iSCSI.

Reemplace las variables de este script por sus propias variables antes de ejecutarlo. También deberá modificarlo para usar un disco Ultra existente si ha rellenado el formulario de disco Ultra.

```azurepowershell
# Install the required module for Disk Pool
Install-Module -Name Az.DiskPool -RequiredVersion 0.1.1 -Repository PSGallery

# Sign in to the Azure account and setup the variables
$subscriptionID = "<yourSubID>"
Set-AzContext -Subscription $subscriptionID
$resourceGroupName= "<yourResourceGroupName>"
$location = "<desiredRegion>"
$diskName = "<desiredDiskName>"
$availabilityZone = "<desiredAvailabilityZone>"
$subnetId='<yourSubnetID>'
$diskPoolName = "<desiredDiskPoolName>"
$iscsiTargetName = "<desirediSCSITargetName>" # This will be used to generate the iSCSI target IQN name
$lunName = "<desiredLunName>"

# You can skip this step if you have already created the disk and assigned proper RBAC permission to the resource group the disk is deployed to
$diskconfig = New-AzDiskConfig -Location $location -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -zone $availabilityZone -MaxSharesCount 2
$disk = New-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -Disk $diskconfig
$diskId = $disk.Id
$scopeDef = "/subscriptions/" + $subscriptionId + "/resourceGroups/" + $resourceGroupName
$rpId = (Get-AzADServicePrincipal -SearchString "StoragePool Resource Provider").id

New-AzRoleAssignment -ObjectId $rpId -RoleDefinitionName "Virtual Machine Contributor" -Scope $scopeDef

# Create a Disk Pool
New-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $subnetId -AvailabilityZone $availabilityZone -SkuName Standard
$diskpool = Get-AzDiskPool -ResourceGroupName $resourceGroupName -Name $DiskPoolName

# Add disks to the Disk Pool
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskId
$lun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $diskId -Name $lunName

# Create an iSCSI Target and expose the disks as iSCSI LUNs
New-AzDiskPoolIscsiTarget -DiskPoolName $diskPoolName -Name $iscsiTargetName -ResourceGroupName $resourceGroupName -Lun $lun -AclMode Dynamic

Write-Output "Print details of the iSCSI target exposed on Disk Pool"

Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName | fl
```


# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

El script proporcionado realiza los siguientes pasos:
- Instala la extensión necesaria para crear y usar grupos de discos.
- Crea un disco y le asigna permisos de RBAC. Si ya ha hecho esto, puede convertir en comentario estas secciones del script.
- Crea un grupo de discos y le agrega el disco.
- Crea y habilita un destino iSCSI.

Reemplace las variables de este script por sus propias variables antes de ejecutarlo. También deberá modificarlo para usar un disco Ultra existente si ha rellenado el formulario de disco Ultra.

```azurecli
# Add disk pool CLI extension
az extension add -n diskpool

#az extension add -s https://zuhdefault.blob.core.windows.net/cliext/diskpool-0.1.1-py3-none-any.whl

#Select subscription
az account set --subscription "<yourSubscription>"

##Initialize input parameters
resourceGroupName='<yourRGName>'
location='<desiredRegion>'
zone=<desiredZone>
subnetId='<yourSubnetID>'
diskName='<desiredDiskName>'
diskPoolName='<desiredDiskPoolName>'
targetName='<desirediSCSITargetName>'
lunName='<desiredLunName>'

#You can skip this step if you have already created the disk and assigned permission in the prerequisite step. Below is an example for premium disks.
az disk create --name $diskName --resource-group $resourceGroupName --zone $zone --location $location --sku Premium_LRS --max-shares 2 --size-gb 1024

#You can deploy all your disks into one resource group and assign StoragePool Resource Provider permission to the group
storagePoolObjectId=$(az ad sp list --filter "displayName eq 'StoragePool Resource Provider'" --query "[0].objectId" -o json)
storagePoolObjectId="${storagePoolObjectId%"}"
storagePoolObjectId="${storagePoolObjectId#"}"

az role assignment create --assignee-object-id $storagePoolObjectId --role "Virtual Machine Contributor" --resource-group $resourceGroupName

#Create a disk pool 
az disk-pool create --name $diskPoolName \
--resource-group $resourceGroupName \
--location $location \
--availability-zones $zone \
--subnet-id $subnetId \
--sku name="Standard"

#Initialize an iSCSI target. You can have 1 iSCSI target per disk pool
az disk-pool iscsi-target create --name $targetName \
--disk-pool-name $diskPoolName \
--resource-group $resourceGroupName \
--acl-mode Dynamic

#Add the disk to disk pool
diskId=$(az disk show --name $diskName --resource-group $resourceGroupName --query "id" -o json)
diskId="${diskId%"}"
diskId="${diskId#"}"

az disk-pool update --name $diskPoolName --resource-group $resourceGroupName --disks $diskId

#Expose disks added in the Disk Pool as iSCSI Lun 
az disk-pool iscsi-target update --name $targetName \
 --disk-pool-name $diskPoolName \
 --resource-group $resourceGroupName \
 --luns name=$lunName managed-disk-azure-resource-id=$diskId
```
---

## <a name="next-steps"></a>Pasos siguientes

- Si tiene algún problema al implementar un grupo de discos, consulte [Solución de problemas de grupos de discos de Azure (versión preliminar)](disks-pools-troubleshoot.md).
- [Conexión de grupos de discos a hosts de Azure VMware Solution (versión preliminar)](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md).
- [Administración de un grupo de discos](disks-pools-manage.md).
