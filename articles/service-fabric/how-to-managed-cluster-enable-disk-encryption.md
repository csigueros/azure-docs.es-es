---
title: Habilitación del cifrado de disco para nodos de clústeres administrados de Service Fabric
description: Aprenda a habilitar el cifrado de discos en nodos de clústeres administrados de Azure Service Fabric en Windows mediante una plantilla de Resource Manager.
ms.topic: how-to
ms.date: 11/8/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cd16cdea3a5c8fa4f235ff14ab75bdd07e348db0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349981"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-nodes"></a>Habilitación del cifrado de disco para nodos de clústeres administrados de Service Fabric

Los clústeres administrados de Service Fabric admiten dos opciones de cifrado de disco para ayudar a proteger los datos con el fin de respetar los compromisos de cumplimiento y seguridad de la organización. La opción recomendada es el cifrado en el host, pero también se admite Azure Disk Encryption. Revise las [opciones de cifrado de discos](../virtual-machines/disk-encryption-overview.md) y asegúrese de que la opción seleccionada satisface sus necesidades.


## <a name="enable-encryption-at-host-preview"></a>Habilitación del cifrado en el host (versión preliminar)

Este método de cifrado mejora [Azure Disk Encryption](how-to-managed-cluster-enable-disk-encryption.md) al admitir todos los tipos e imágenes del sistema operativo, incluidas las imágenes personalizadas, para las máquinas virtuales mediante el cifrado de datos en el servicio Azure Storage. Este método no usa la CPU de las máquinas virtuales ni afecta al rendimiento de las máquinas, lo que permite que las cargas de trabajo usen todos los recursos de SKU de máquinas virtuales disponibles.

> [!Note]
> No se puede habilitar en tipos de nodo existentes. Debe aprovisionar un nuevo tipo de nodo y migrar la carga de trabajo.

> [!Note]
> El estado de cifrado del disco en Azure Security Center se mostrará como Incorrecto en este momento al usar el cifrado en el host.

Siga estos pasos y consulte esta [plantilla de ejemplo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-HostEncryption) para implementar un nuevo clúster administrado de Service Fabric con el cifrado de host habilitado.

1. Revise las [restricciones](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#restrictions) siguientes para validar que cumplen sus requisitos.

2. Configure los [requisitos previos](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#prerequisites) necesarios antes de la implementación del clúster.

3. Configure la propiedad `enableEncryptionAtHost` en la plantilla de clúster administrado para cada tipo de nodo que requiere cifrado de disco. El ejemplo está preconfigurado.

   * El valor de apiVersion del recurso de clúster administrado de Service Fabric debe ser **2021-11-01-preview** o posterior.

   ```json
        {
               "apiVersion": "[variables('sfApiVersion')]",
               "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
               "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
               "location": "[resourcegroup().location]",
               "properties": {
                   "enableEncryptionAtHost": true
                   ...
               }
       }
   ```

4. Implementación y comprobación

   Implemente el clúster administrado configurado con el cifrado de host habilitado.

   ```powershell
   $clusterName = "<clustername>" 
   $resourceGroupName = "<rg-name>"

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   Puede comprobar el estado del cifrado de disco en el conjunto de escalado subyacente de un tipo de nodo mediante el comando `Get-AzVmssDiskEncryption`. En primer lugar, debe buscar el nombre del grupo de recursos de apoyo del clúster administrado (que contiene la red virtual subyacente, el equilibrador de carga, la dirección IP pública, el grupo de seguridad de red, los conjuntos de escalado y las cuentas de almacenamiento). Asegúrese de modificar `VmssName` a cualquier nombre de tipo de nodo de clúster que desee comprobar (como se especifica en la plantilla de implementación).

   ```powershell
   $NodeTypeName = "NT2"
   $clustername = <clustername>
   $resourceGroupName = "<rg-name>"
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   $VMSS = Get-AzVmss -ResourceGroupName $supportResourceGroupName -Name $NodeTypeName
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   ```

   La salida devuelta debe ser similar a esta:

   ```console
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   True
   ```

## <a name="enable-azure-disk-encryption"></a>Habilitación de Azure Disk Encryption
Azure Disk Encryption ofrece cifrado de volumen para los discos de datos y del sistema operativo en las máquinas virtuales de Azure mediante el uso de la característica DM-Crypt de Linux o la característica BitLocker de Windows. ADE está integrado con Azure Key Vault para ayudarle a controlar y administrar los secretos y las claves de cifrado de discos.

En esta guía, aprenderá a habilitar el cifrado de disco en nodos de clústeres administrados de Service Fabric en Windows con la funcionalidad de [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) para [conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) a través de plantillas de Azure Resource Manager.

1. Registro en Azure Disk Encryption

   La versión preliminar del cifrado de disco de un conjunto de escalado de máquinas virtuales requiere el registro automático. Ejecute el siguiente comando:

   ```powershell
   Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
   ```

   Compruebe el estado del registro mediante la ejecución de:

   ```powershell
   Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
   ```

   Una vez que el estado cambia a *Registrado*, está listo para continuar.

2. Aprovisionamiento de Key Vault para el cifrado de disco

   Azure Disk Encryption requiere Azure Key Vault para controlar y administrar las claves y los secretos de cifrado de discos. El clúster administrado de Key Vault y Service Fabric debe residir en la misma región y suscripción de Azure. Siempre que se cumplan estos requisitos, puede usar un almacén de claves nuevo o uno ya existente si lo habilita para el cifrado de discos.

3. Creación de un almacén de claves con cifrado de disco habilitado

   Ejecute los siguientes comandos para crear un nuevo almacén de claves para el cifrado de discos. Asegúrese de que la región de Key Vault está en la misma región que el clúster.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
   New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

   ```azurecli
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
   ```

   ---

4. Actualización del almacén de claves existente para habilitar el cifrado de disco

   Ejecute los siguientes comandos para habilitar el cifrado de disco para un almacén de claves ya existente.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

   ```azurecli
   az keyvault update --name keyvaultName --enabled-for-disk-encryption 
   ```

   ---

### <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Actualización de los archivos de plantilla y de los parámetros para el cifrado de disco

El siguiente paso le guiará a través de los cambios de plantilla necesarios para habilitar el cifrado de disco en un [clúster administrado existente](tutorial-managed-cluster-deploy.md). Como alternativa, puede implementar un nuevo clúster administrado de Service Fabric con el cifrado de discos habilitado con esta plantilla: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Agregue los siguientes parámetros a la plantilla, sustituyendo su propia suscripción, el nombre del grupo de recursos y el nombre del almacén en `keyVaultResourceId`:

   ```json
   "parameters": {
    "keyVaultResourceId": { 
      "type": "string", 
      "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
      "metadata": { 
      "description": "Full resource id of the Key Vault used for disk encryption." 
   } 
    },
    "volumeType": { 
     "type": "string", 
     "defaultValue": "All", 
     "metadata": { 
      "description": "Type of the volume OS or Data to perform encryption operation" 
   }
   }
   }, 
   ```

2. A continuación, agregue la extensión de máquina virtual `AzureDiskEncryption` a los tipos de nodos de clústeres administrados de la plantilla:

   ```json
   "properties": { 
   "vmExtensions": [ 
   { 
   "name": "AzureDiskEncryption", 
   "properties": { 
     "publisher": "Microsoft.Azure.Security", 
     "type": "AzureDiskEncryption", 
     "typeHandlerVersion": "2.2", 
     "autoUpgradeMinorVersion": true, 
     "settings": {      
           "EncryptionOperation": "EnableEncryption", 
           "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
        "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
        "VolumeType": "[parameters('volumeType')]" 
        } 
      } 
   } 
   ] 
   } 
   ```

3. Por último, actualice el archivo de parámetros, sustituyendo su propia suscripción, el grupo de recursos y el nombre del almacén de claves en *keyVaultResourceId*:

   ```json
   "parameters": { 
   ...
    "keyVaultResourceId": { 
     "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
     "value": "All" 
    }    
   } 
   ```

4. Implementación y comprobación de los cambios

   Una vez que esté listo, implemente los cambios para habilitar el cifrado de disco en el clúster administrado.

   ```powershell
   $clusterName = "<clustername>" 

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   Puede comprobar el estado del cifrado de disco en el conjunto de escalado subyacente de un tipo de nodo mediante el comando `Get-AzVmssDiskEncryption`. En primer lugar, debe buscar el nombre del grupo de recursos de apoyo del clúster administrado (que contiene la red virtual subyacente, el equilibrador de carga, la dirección IP pública, el grupo de seguridad de red, los conjuntos de escalado y las cuentas de almacenamiento). Asegúrese de modificar `VmssName` a cualquier nombre de tipo de nodo de clúster que desee comprobar (como se especifica en la plantilla de implementación).

   ```powershell
   $VmssName = "NT1"
   $clustername = <clustername>
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
   ```

   La salida debe ser similar a esta:

   ```console
   ResourceGroupName            : SFC_########-####-####-####-############
   VmScaleSetName               : NT1
   EncryptionEnabled            : True
   EncryptionExtensionInstalled : True
   ```

## <a name="next-steps"></a>Pasos siguientes

[Ejemplo: clúster administrado de Service Fabric con SKU estándar, un tipo de nodo con cifrado de disco habilitado](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption para máquinas virtuales Windows](../virtual-machines/windows/disk-encryption-overview.md)

[Cifrado de conjuntos de escalado de máquinas virtuales con Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)