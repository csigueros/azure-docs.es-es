---
title: 'Inicio rápido: Copia de seguridad de VM con plantillas de Bicep'
description: Aprenda a hacer copias de seguridad de sus máquinas virtuales con una plantilla de Bicep.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 11/17/2021
ms.reviewer: Daya-Patil
ms.custom: mvc,subject-bicepqs, devx-track-azurepowershell
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: d72e48e0bb7116bd1a828a3d772b6f283e6fd0a2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725367"
---
#  <a name="back-up-a-virtual-machine-in-azure-with-a-bicep-template"></a>Copia de seguridad de una máquina virtual en Azure con una plantilla de Bicep

[Azure Backup](backup-overview.md) permite hacer una copia de seguridad de una VM de Azure mediante varias opciones, como Azure Portal, PowerShell, la CLI, Azure Resource Manager, Bicep, entre otras. En este artículo se describe cómo hacer una copia de seguridad de una VM de Azure con una plantilla de Azure Bicep y Azure PowerShell. Este artículo de inicio rápido se centra en el proceso de implementación de una plantilla de Bicep para crear un almacén de Recovery Services. Para obtener más información sobre el desarrollo de plantillas de Bicep, consulte la [documentación de Bicep](../azure-resource-manager/bicep/deploy-cli.md) y la [referencia de plantilla](/azure/templates/microsoft.recoveryservices/allversions).

Bicep es un lenguaje para implementar mediante declaración los recursos de Azure. Puede usar Bicep en lugar de JSON para desarrollar las plantillas de Azure Resource Manager (plantillas de ARM). La sintaxis de Bicep reduce esa complejidad y mejora la experiencia de desarrollo. Bicep es una abstracción transparente del código JSON de plantillas de ARM, y ofrece todas las funcionalidades de las plantillas JSON. Durante la implementación, la CLI de Bicep convierte un archivo Bicep en un archivo JSON de plantilla de ARM. Un archivo Bicep declara recursos de Azure y propiedades de estos recursos, sin escribir una secuencia de comandos de programación para crear recursos.

Los tipos de recursos, las versiones de API y las propiedades que son válidos en una plantilla de ARM son válidos también en un archivo Bicep.

## <a name="prerequisites"></a>Requisitos previos

A fin de configurar el entorno para el desarrollo de Bicep, consulte [Instalación de las herramientas de Bicep](../azure-resource-manager/bicep/install.md).

>[!Note]
>Instale el [módulo Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-6.6.0) más reciente y la CLI de Bicep según se detalla en el artículo.

## <a name="review-the-template"></a>Revisión de la plantilla

La plantilla usada a continuación forma parte de las [plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/recovery-services-create-vm-and-configure-backup/). Esta plantilla le permite implementar una VM Windows y un almacén de Recovery Services sencillos configurados con _DefaultPolicy_ para _Protection_.

```json
@description('Specifies a name for generating resource names.')
@maxLength(8)
param projectName string

@description('Specifies the location for all resources.')
param location string = resourceGroup().location

@description('Specifies the administrator username for the Virtual Machine.')
param adminUsername string

@description('Specifies the administrator password for the Virtual Machine.')
@secure()
param adminPassword string

@description('Specifies the unique DNS Name for the Public IP used to access the Virtual Machine.')
param dnsLabelPrefix string

@description('Virtual machine size.')
param vmSize string = 'Standard_A2'

@description('Specifies the Windows version for the VM. This will pick a fully patched image of this given Windows version.')
@allowed([
  '2008-R2-SP1'
  '2012-Datacenter'
  '2012-R2-Datacenter'
  '2016-Nano-Server'
  '2016-Datacenter-with-Containers'
  '2016-Datacenter'
  '2019-Datacenter'
  '2019-Datacenter-Core'
  '2019-Datacenter-Core-smalldisk'
  '2019-Datacenter-Core-with-Containers'
  '2019-Datacenter-Core-with-Containers-smalldisk'
  '2019-Datacenter-smalldisk'
  '2019-Datacenter-with-Containers'
  '2019-Datacenter-with-Containers-smalldisk'
])
param windowsOSVersion string = '2016-Datacenter'

var storageAccountName = '${projectName}store'
var networkInterfaceName = '${projectName}-nic'
var vNetAddressPrefix = '10.0.0.0/16'
var vNetSubnetName = 'default'
var vNetSubnetAddressPrefix = '10.0.0.0/24'
var publicIPAddressName = '${projectName}-ip'
var vmName = '${projectName}-vm'
var vNetName = '${projectName}-vnet'
var vaultName = '${projectName}-vault'
var backupFabric = 'Azure'
var backupPolicyName = 'DefaultPolicy'
var protectionContainer = 'iaasvmcontainer;iaasvmcontainerv2;${resourceGroup().name};${vmName}'
var protectedItem = 'vm;iaasvmcontainerv2;${resourceGroup().name};${vmName}'
var networkSecurityGroupName = 'default-NSG'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}

resource publicIPAddress 'Microsoft.Network/publicIPAddresses@2020-06-01' = {
  name: publicIPAddressName
  location: location
  properties: {
    publicIPAllocationMethod: 'Dynamic'
    dnsSettings: {
      domainNameLabel: dnsLabelPrefix
    }
  }
}

resource networkSecurityGroup 'Microsoft.Network/networkSecurityGroups@2020-06-01' = {
  name: networkSecurityGroupName
  location: location
  properties: {
    securityRules: [
      {
        name: 'default-allow-3389'
        properties: {
          priority: 1000
          access: 'Allow'
          direction: 'Inbound'
          destinationPortRange: '3389'
          protocol: 'Tcp'
          sourceAddressPrefix: '*'
          sourcePortRange: '*'
          destinationAddressPrefix: '*'
        }
      }
    ]
  }
}

resource vNet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetName
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetAddressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSubnetName
        properties: {
          addressPrefix: vNetSubnetAddressPrefix
          networkSecurityGroup: {
            id: networkSecurityGroup.id
          }
        }
      }
    ]
  }
}

resource networkInterface 'Microsoft.Network/networkInterfaces@2020-06-01' = {
  name: networkInterfaceName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          publicIPAddress: {
            id: publicIPAddress.id
          }
          subnet: {
            id: '${vNet.id}/subnets/${vNetSubnetName}'
          }
        }
      }
    ]
  }
}

resource virtualMachine 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  properties: {
    hardwareProfile: {
      vmSize: vmSize
    }
    osProfile: {
      computerName: vmName
      adminUsername: adminUsername
      adminPassword: adminPassword
    }
    storageProfile: {
      imageReference: {
        publisher: 'MicrosoftWindowsServer'
        offer: 'WindowsServer'
        sku: windowsOSVersion
        version: 'latest'
      }
      osDisk: {
        createOption: 'FromImage'
      }
      dataDisks: [
        {
          diskSizeGB: 1023
          lun: 0
          createOption: 'Empty'
        }
      ]
    }
    networkProfile: {
      networkInterfaces: [
        {
          id: networkInterface.id
        }
      ]
    }
    diagnosticsProfile: {
      bootDiagnostics: {
        enabled: true
        storageUri: storageAccount.properties.primaryEndpoints.blob
      }
    }
  }
}

resource recoveryServicesVault 'Microsoft.RecoveryServices/vaults@2020-02-02' = {
  name: vaultName
  location: location
  sku: {
    name: 'RS0'
    tier: 'Standard'
  }
  properties: {}
}

resource vaultName_backupFabric_protectionContainer_protectedItem 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems@2020-02-02' = {
  name: '${vaultName}/${backupFabric}/${protectionContainer}/${protectedItem}'
  properties: {
    protectedItemType: 'Microsoft.Compute/virtualMachines'
    policyId: '${recoveryServicesVault.id}/backupPolicies/${backupPolicyName}'
    sourceResourceId: virtualMachine.id
  }
} 

```

Los recursos definidos en la plantilla anterior son los siguientes:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
- [Microsoft.RecoveryServices/vaults](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>Implementación de la plantilla

Para implementar la plantilla, seleccione _Pruébelo_ para abrir Azure Cloud Shell y pegue el siguiente script de PowerShell en la ventana del shell. Para pegar el código, haga clic con el botón derecho en la ventana del shell y seleccione **Pegar**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.recoveryservices/recovery-services-create-vm-and-configure-backup/main.bicep"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix 

```

## <a name="validate-the-deployment"></a>Validación de la implementación

### <a name="start-a-backup-job"></a>Inicio de un trabajo de copia de seguridad

La plantilla crea una VM y permite crear copias de seguridad de la VM. Después de implementar la plantilla, deberá iniciar un trabajo de copia de seguridad. Para más información, consulte [Inicio de un trabajo de copia de seguridad](quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Supervisión del trabajo de copia de seguridad

Para supervisar el trabajo de copia de seguridad, consulte [Supervisión del trabajo de copia de seguridad](quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-resources"></a>Limpieza de recursos

- Si ya no necesita realizar copias de seguridad de la máquina virtual, puede limpiarla.
- Para probar restaurar la VM, omita el proceso de limpieza.
- Si ha usado una VM existente, puede omitir el último cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para conservar el grupo de recursos y la VM.

Siga estos pasos:

1. Deshabilite la protección, elimine los puntos de restauración y el almacén.

1. Elimine el grupo de recursos y los recursos de VM asociados como se indica a continuación:

   ```azurepowershell
   Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
   $vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
   Remove-AzRecoveryServicesVault -Vault $vault
   Remove-AzResourceGroup -Name "myResourceGroup" 

   ```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado un almacén de Recovery Services, ha habilitado la protección en una máquina virtual y ha creado el punto de recuperación inicial.

- [Aprenda](tutorial-backup-vm-at-scale.md) a realizar una copia de seguridad de máquinas virtuales en Azure Portal.
- [Aprenda a](tutorial-restore-disk.md) restaurar rápidamente una máquina virtual.
- [Aprenda a](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md?tabs=CLI) crear plantillas de Bicep.
- [Aprenda](../azure-resource-manager/bicep/decompile.md?tabs=azure-cli) a descompilar plantillas de Azure Resource Manager (plantillas de ARM) en archivos Bicep.


