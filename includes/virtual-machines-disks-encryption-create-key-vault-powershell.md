---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f74228b918b3d87ab77b75132501327b08b25668
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022059"
---
1. Asegúrese de haber instalado la versión más reciente de [Azure PowerShell](/powershell/azure/install-az-ps) y de haber iniciado sesión en una cuenta de Azure con Connect-AzAccount

1. Cree una instancia de Azure Key Vault y la clave de cifrado.

    Al crear la instancia de Key Vault, debe habilitar la protección contra purgas. La protección de purgas garantiza que una clave eliminada no se puede eliminar permanentemente hasta que transcurra el período de retención. Esta configuración le protege contra la pérdida de datos debido a la eliminación accidental. Estos valores son obligatorios cuando se usa una instancia de Key Vault para cifrar discos administrados.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName `
    -ResourceGroupName $ResourceGroupName `
    -Location $LocationName `
    -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName `
          -Name $keyName `
          -Destination $keyDestination 
    ```

1.    Cree una instancia de un objeto DiskEncryptionSet. Puede establecer RotationToLatestKeyVersionEnabled en $true para habilitar la rotación automática de la clave. Al habilitar la rotación automática, el sistema actualiza automáticamente todos los discos administrados, instantáneas e imágenes que hagan referencia al conjunto de cifrado de disco para usar la nueva versión de la clave en una hora.  
    
        ```powershell
      $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName `
            -SourceVaultId $keyVault.ResourceId `
            -KeyUrl $key.Key.Kid `
            -IdentityType SystemAssigned `
            -RotationToLatestKeyVersionEnabled $false

       $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName `
               -ResourceGroupName $ResourceGroupName `
               -InputObject $desConfig
        ```

1.    Conceda al recurso DiskEncryptionSet acceso al almacén de claves.

        > [!NOTE]
        > Azure puede tardar unos minutos en crear la identidad del elemento DiskEncryptionSet en Azure Active Directory. Si recibe un error similar a "No se encuentra el objeto en Active Directory" al ejecutar el siguiente comando, espere unos minutos y vuelva a intentarlo.
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```

### <a name="use-a-key-vault-in-a-different-subscription"></a>Uso de un almacén de claves en una suscripción diferente

Como alternativa, puede administrar las instancias de Azure Key Vault de forma centralizada desde una sola suscripción y usar las claves almacenadas en Key Vault para cifrar discos administrados e instantáneas en otras suscripciones de su organización. Esto permite a su equipo de seguridad aplicar y administrar fácilmente una directiva de seguridad sólida en una sola suscripción.

> [!IMPORTANT]
> Para esta configuración, tanto Key Vault como el conjunto de cifrado de disco deben estar en la misma región y usar el mismo inquilino.

El siguiente script es un ejemplo de cómo puede configurar un conjunto de cifrado de disco para usar una clave de una instancia de Key Vault que se encuentra en una suscripción diferente, pero en la misma región:

```azurepowershell
$sourceSubscriptionId="<sourceSubID>"
$sourceKeyVaultName="<sourceKVName>"
$sourceKeyName="<sourceKeyName>"

$targetSubscriptionId="<targetSubID>"
$targetResourceGroupName="<targetRGName>"
$targetDiskEncryptionSetName="<targetDiskEncSetName>"
$location="<targetRegion>"

Set-AzContext -Subscription $sourceSubscriptionId

$key = Get-AzKeyVaultKey -VaultName $sourceKeyVaultName -Name $sourceKeyName

Set-AzContext -Subscription $targetSubscriptionId

$desConfig=New-AzDiskEncryptionSetConfig -Location $location `
-KeyUrl $key.Key.Kid `
-IdentityType SystemAssigned `
-RotationToLatestKeyVersionEnabled $false

$des=New-AzDiskEncryptionSet -Name $targetDiskEncryptionSetName `
-ResourceGroupName $targetResourceGroupName `
-InputObject $desConfig
```