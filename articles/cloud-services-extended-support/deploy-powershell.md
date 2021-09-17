---
title: 'Implementación de un servicio en la nube (soporte extendido): PowerShell'
description: Implementación de un servicio en la nube (soporte extendido) mediante PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d5d28c64da48e6d9c47e101bf4d1fb8b53b239f3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747574"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Implementación de un servicio en la nube (soporte extendido) mediante Azure PowerShell

En este artículo se muestra cómo usar el módulo `Az.CloudService` de PowerShell para implementar Cloud Services (soporte extendido) en Azure, con varios roles (WebRole y WorkerRole).

## <a name="pre-requisites"></a>Requisitos previos

1. Consulte los [requisitos previos de implementación](deploy-prerequisite.md) de Cloud Services (soporte extendido) y cree los recursos asociados. 
2. Instale el módulo Az.CloudService de PowerShell.

    ```azurepowershell-interactive
    Install-Module -Name Az.CloudService 
    ```

3. Crear un grupo de recursos. Este paso es opcional si se usa un grupo de recursos existente.   

    ```azurepowershell-interactive
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

4. Cree una cuenta de almacenamiento y un contenedor que se usará para almacenar el paquete de servicios en la nube (.cspkg) y los archivos de configuración de servicio (.cscfg). Se necesita un nombre único para el nombre de la cuenta de almacenamiento. Este paso es opcional si se usa una cuenta de almacenamiento existente.

    ```azurepowershell-interactive
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```
    
## <a name="deploy-a-cloud-services-extended-support"></a>Implementación de Cloud Services (soporte extendido)

Use cualquiera de los siguientes cmdlets de PowerShell para implementar Cloud Services (soporte extendido):

1. [**Creación rápida de un servicio en la nube mediante una cuenta de almacenamiento**](#quick-create-cloud-service-using-a-storage-account)

    - Este conjunto de parámetros introduce los archivos .cscfg, .cspkg y .csdef como entradas junto con la cuenta de almacenamiento. 
    - El cmdlet crea el perfil de rol del servicio en la nube, el perfil de red y el perfil del sistema operativo con intervención mínima del usuario. 
    - Para la entrada del certificado, se debe especificar el nombre del almacén de claves. Las huellas digitales del certificado del almacén de claves se validan con las especificadas en el archivo .cscfg.
    
 2. [**Creación rápida de un servicio en la nube mediante un URI de SAS**](#quick-create-cloud-service-using-a-sas-uri)
 
    - Este conjunto de parámetros introduce el URI de SAS del archivo .cspkg junto con las rutas de acceso locales de los archivos .csdef y .cscfg. No se requiere ninguna entrada de la cuenta de almacenamiento. 
    - El cmdlet crea el perfil de rol del servicio en la nube, el perfil de red y el perfil del sistema operativo con intervención mínima del usuario. 
    - Para la entrada del certificado, se debe especificar el nombre del almacén de claves. Las huellas digitales del certificado del almacén de claves se validan con las especificadas en el archivo .cscfg.
    
3. [**Creación de un servicio en la nube con rol, sistema operativo, perfil de red y extensión y URI de SAS**](#create-cloud-service-using-profile-objects--sas-uris)

    - Este conjunto de parámetros introduce los URI de SAS de los archivos .cscfg y .cspkg.
    - El usuario debe especificar el rol, la red, el sistema operativo y el perfil de extensión, y deben coincidir con los valores de .cscfg y .csdef. 

### <a name="quick-create-cloud-service-using-a-storage-account"></a>Creación rápida de un servicio en la nube mediante una cuenta de almacenamiento

Cree la implementación del servicio en la nube mediante los archivos .cscfg, .csdef y .cspkg.

```azurepowershell-interactive
$cspkgFilePath = "<Path to cspkg file>"
$cscfgFilePath = "<Path to cscfg file>"
$csdefFilePath = "<Path to csdef file>"
      
# Create Cloud Service       
New-AzCloudService
-Name "ContosoCS" `
-ResourceGroupName "ContosOrg" `
-Location "EastUS" `
-ConfigurationFile $cscfgFilePath `
-DefinitionFile $csdefFilePath `
-PackageFile $cspkgFilePath `
-StorageAccount $storageAccount `
[-KeyVaultName <string>]
```

### <a name="quick-create-cloud-service-using-a-sas-uri"></a>Creación rápida de un servicio en la nube mediante un URI de SAS

1. Cargue el paquete de servicios en la nube (cspkg) en la cuenta de almacenamiento.

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    $cscfgFilePath = "<Path to cscfg file>"
    $csdefFilePath = "<Path to csdef file>"
    ```

 2. Cree la implementación del servicio en la nube mediante los archivos .cscfg y .csdef, y el URI de SAS de .cspkg.

    ```azurepowershell-interactive
    New-AzCloudService
        -Name "ContosoCS" `
        -ResourceGroupName "ContosOrg" `
        -Location "EastUS" `
        -ConfigurationFile $cspkgFilePath `
        -DefinitionFile $csdefFilePath `
        -PackageURL $cspkgUrl `
        [-KeyVaultName <string>]
    ```
    
### <a name="create-cloud-service-using-profile-objects--sas-uris"></a>Creación de un servicio en la nube mediante objetos de perfil y URI de SAS

1.  Cargue la configuración de servicios en la nube (cscfg) en la cuenta de almacenamiento. 

    ```azurepowershell-interactive
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```
2. Cargue el paquete de servicios en la nube (cspkg) en la cuenta de almacenamiento.

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
    
3. Cree una red virtual y una subred. Este paso es opcional si se usan una red y una subred existentes. En este ejemplo se usa una sola red virtual y una subred para ambos roles de servicios en la nube (WebRole y WorkerRole). 

    ```azurepowershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
4. Cree una dirección IP pública y establezca la propiedad de la etiqueta DNS de esa dirección IP pública. Cloud Services (soporte extendido) solo admite direcciones IP públicas de la SKU [básica](../virtual-network/public-ip-addresses.md#basic). Las direcciones IP públicas de la SKU estándar no funcionan con Cloud Services.
Si usa una dirección IP estática, debe hacer referencia a ella como IP reservada del archivo de configuración de servicio (.cscfg).

    ```azurepowershell-interactive
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

5. Cree un objeto de perfil de red y asocie la dirección IP pública al front-end del equilibrador de carga. La plataforma Azure crea automáticamente un recurso de equilibrador de carga de SKU "clásica" en la misma suscripción que el recurso de servicio en la nube. El recurso de equilibrador de carga es un recurso de solo lectura en Azure Resource Manager. Las actualizaciones del recurso solo se admiten únicamente a través de los archivos de implementación de servicios en la nube (.cscfg y .csdef).

    ```azurepowershell-interactive
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
6. Crear un almacén de claves. Este almacén de claves se usará para almacenar los certificados asociados a los roles del servicio en la nube (soporte extendido). El almacén de claves debe estar vinculado a la misma región y suscripción que el servicio en la nube y tener un nombre único. Para más información, consulte [Uso de certificados con Azure Cloud Services (soporte extendido)](certificates-and-key-vault.md).

    ```azurepowershell-interactive
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

7. Actualice la directiva de acceso de Key Vault y conceda permisos de certificados a la cuenta de usuario. 

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Como alternativa, establezca la directiva de acceso mediante ObjectId, que puede obtener al ejecutar `Get-AzADUser`. 
    
    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

8. En este ejemplo agregaremos un certificado autofirmado a un almacén de claves. La huella digital del certificado se debe agregar en el archivo de configuración de servicio en la nube (. cscfg) para implementarse en roles de servicio en la nube. 

    ```azurepowershell-interactive
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
9. Cree un objeto en memoria de perfil de SO. El perfil de SO especifica los certificados que están asociados a roles de servicio en la nube. Se trata del mismo certificado creado en el paso anterior. 

    ```azurepowershell-interactive
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

10. Cree un objeto en memoria de perfil de rol. El perfil de rol define las propiedades específicas de SKU de roles, como el nombre, la capacidad y el nivel. En este ejemplo, se han definido dos roles: frontendRole y backendRole. La información del perfil de rol debe coincidir con la configuración de rol definida en el archivo de configuración (cscfg) y el archivo de definición de servicio (csdef). 

    ```azurepowershell-interactive
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

11. Si lo desea, cree el objeto en memoria de perfil de extensión que desee agregar al servicio en la nube. En este ejemplo, se agregará la extensión RDP. 

    ```azurepowershell-interactive
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    
    ConfigFile solo debe tener etiquetas PublicConfig y debe incluir un espacio de nombres. tal y como se muestra a continuación:
   
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
    
12. (OPcional) Defina las etiquetas, como en una tabla hash de PowerShell, que desee agregar al servicio en la nube. 

    ```azurepowershell-interactive
    $tag=@{"Owner" = "Contoso"} 
    ```

13. Cree una implementación de servicio en la nube mediante objetos de perfil y direcciones URL de SAS.

    ```azurepowershell-interactive
    $cloudService = New-AzCloudService ` 
        -Name “ContosoCS” ` 
        -ResourceGroupName “ContosOrg” ` 
        -Location “East US” ` 
        -PackageUrl $cspkgUrl ` 
        -ConfigurationUrl $cscfgUrl ` 
        -UpgradeMode 'Auto' ` 
        -RoleProfile $roleProfile ` 
        -NetworkProfile $networkProfile  ` 
        -ExtensionProfile $extensionProfile ` 
        -OSProfile $osProfile `
        -Tag $tag 
    ```

## <a name="next-steps"></a>Pasos siguientes 
- Vea las [preguntas más frecuentes](faq.yml) sobre Cloud Services (soporte extendido).
- Implemente una instancia de Cloud Services (soporte extendido) mediante [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), una [plantilla](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
- Visite el [repositorio de ejemplos de Cloud Services (soporte extendido)](https://github.com/Azure-Samples/cloud-services-extended-support).
