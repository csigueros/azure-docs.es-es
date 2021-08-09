---
title: Extensión de máquina virtual de Azure Key Vault para Windows
description: Implemente un agente que realice la actualización automática de secretos de Key Vault en máquinas virtuales mediante una extensión de máquina virtual.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 546537003d599dc66f77ace31471e04c8cef2d43
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854835"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extensión de máquina virtual de Key Vault para Windows

La extensión de máquina virtual de Key Vault proporciona la actualización automática de certificados almacenados en un almacén de claves de Azure. En concreto, la extensión supervisa una lista de certificados observados almacenados en almacenes de claves y, cuando detecta un cambio, recupera e instala los certificados correspondientes. En este documento se especifican las plataformas compatibles, configuraciones y opciones de implementación de la extensión de máquina virtual de Key Vault para Windows. 

### <a name="operating-system"></a>Sistema operativo

La extensión de máquina virtual de Key Vault admite las siguientes versiones de Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

También se admite la extensión VM de Key Vault en una VM local personalizada que se carga y se convierte en una imagen especializada para su uso en Azure mediante la instalación básica de Windows Server 2019.

### <a name="supported-certificate-content-types"></a>Tipos de contenido de certificado admitidos

- PKCS #12
- PEM

## <a name="prerequisites"></a>Prerrequisitos

  - Instancia de Key Vault con certificado. Consulte [Creación de un almacén de claves](../../key-vault/general/quick-create-portal.md).
  - La máquina virtual debe tener asignada una [identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md).
  - La directiva de acceso de Key Vault se debe establecer con los secretos `get` y los permisos `list` para la identidad administrada de VM/VMSS y recuperar la parte del certificado de un secreto. Consulte [Autenticación en Key Vault](../../key-vault/general/authentication.md) y [Asignación de una directiva de acceso de Key Vault](../../key-vault/general/assign-access-policy-cli.md).
  -  Virtual Machine Scale Sets debe tener el siguiente valor de identidad:

  ``` 
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "[parameters('userAssignedIdentityResourceId')]": {}
    }
  }
  ```
  
  - La extensión AKV debe tener este valor: 

  ```
  "authenticationSettings": {
    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
  }
  ```

## <a name="extension-schema"></a>Esquema de extensión

El siguiente JSON muestra el esquema para la extensión de máquina virtual de Key Vault. La extensión no requiere configuración protegida, ya que todas sus opciones se consideran información pública. La extensión requiere una lista de certificados supervisados, frecuencia de sondeo y el almacén de certificados de destino. Concretamente:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <string specifying polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Las direcciones URL de los certificados observados deben tener el formato `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Esto se debe a que la ruta de acceso `/secrets` devuelve el certificado completo, incluida la clave privada, mientras que la ruta de acceso `/certificates` no. Se puede encontrar más información sobre los certificados aquí: [Certificados de Key Vault](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> La propiedad "authenticationSettings" es **necesaria** solo para máquinas virtuales con **identidades asignadas por el usuario**.
> Especifica la identidad que se usará para la autenticación en Key Vault.


### <a name="property-values"></a>Valores de propiedad

| Nombre | Valor / ejemplo | Tipo de datos |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine o CurrentUser (distingue mayúsculas de minúsculas) | string |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | matriz de cadenas
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>Implementación de plantilla

Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. Las plantillas son ideales al implementar una o varias máquinas virtuales que requieren la actualización de los certificados tras la implementación. La extensión se puede implementar en máquinas virtuales individuales o en conjuntos de escalado de máquinas virtuales. El esquema y la configuración son comunes para ambos tipos de plantilla. 

La configuración de JSON para una extensión de máquina virtual debe estar anidada dentro del fragmento de recursos de máquina virtual de la plantilla, en concreto en el objeto `"resources": []` de la plantilla de máquina virtual y, en el caso de un conjunto de escalado de máquinas virtuales, en el objeto `"virtualMachineProfile":"extensionProfile":{"extensions" :[]`.

 > [!NOTE]
> La extensión de VM requeriría la asignación de la identidad administrada del sistema o del usuario para autenticarse en Key Vault.  Consulte [Autenticación en Key Vault y asignación de una directiva de acceso de Key Vault](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <string specifying polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Orden de las dependencias de la extensión
La extensión de máquina virtual de Key Vault admite el orden de las extensiones si está configurado. De forma predeterminada, la extensión informa de que se ha iniciado correctamente en cuanto se ha iniciado el sondeo. Sin embargo, se puede configurar para que espere hasta que se haya descargado correctamente la lista completa de certificados antes de informar de un inicio correcto. Si otras extensiones dependen de tener instalado el conjunto completo de certificados para iniciarse, la habilitación de esta opción permitirá a esa extensión declarar una dependencia de la extensión de Key Vault. Esto evitará que se inicien esas extensiones hasta que se hayan instalado todos los certificados de los que dependen. La extensión volverá a intentar la descarga inicial de forma indefinida y permanecerá en un estado `Transitioning`.

Para activar esta opción, establezca lo siguiente:
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```

> [!Note] 
> El uso de esta característica no es compatible con una plantilla de Resource Manager que crea una identidad asignada por el sistema y actualiza una directiva de acceso de Key Vault con esa identidad. Su utilización producirá un interbloqueo porque la directiva de acceso al almacén no se puede actualizar hasta que se hayan iniciado todas las extensiones. En su lugar, debe usar una *identidad MSI asignada por el usuario única* y establecer una ACL previamente en los almacenes con esa identidad antes de la implementación.

## <a name="azure-powershell-deployment"></a>Implementación de Azure PowerShell

> [!WARNING]
> Los clientes de PowerShell suelen agregar `\` a `"` en settings.json, lo que hace que akvvm_service produzca un error `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.` Los caracteres `\` y `"` adicionales serán visibles en el portal, en **Extensiones**, en **Configuración**. Para evitarlo, inicialice `$settings` como `HashTable` de PowerShell:
> 
> ```powershell
> $settings = @{
>     "secretsManagementSettings" = @{ 
>         "pollingIntervalInS"       = "<pollingInterval>"; 
>         "certificateStoreName"     = "<certStoreName>"; 
>         "certificateStoreLocation" = "<certStoreLoc>"; 
>         "observedCertificates"     = @("<observedCert1>", "<observedCert2>") } }
> ```
>
  
Azure PowerShell puede usarse para implementar la extensión de máquina virtual de Key Vault en una máquina virtual o un conjunto de escalado de máquinas virtuales. 

* Para implementar la extensión en una máquina virtual:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Para implementar la extensión en un conjunto de escalado de máquinas virtuales:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Implementación de la CLI de Azure

La CLI de Azure puede usarse para implementar la extensión de máquina virtual de Key Vault en una máquina virtual o un conjunto de escalado de máquinas virtuales. 
 
* Para implementar la extensión en una máquina virtual:
    
    ```azurecli
       # Start the deployment
         az vm extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Para implementar la extensión en un conjunto de escalado de máquinas virtuales:

   ```azurecli
        # Start the deployment
        az vmss extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Tenga en cuenta las restricciones y los requisitos siguientes:
- Restricciones de Key Vault:
  - Debe existir en el momento de la implementación. 
  - La directiva de acceso de Key Vault debe estar establecida para la identidad de VM/VMSS mediante una identidad administrada. Consulte [Autenticación en Key Vault](../../key-vault/general/authentication.md) y [Asignación de una directiva de acceso de Key Vault](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Solución de problemas y asistencia

### <a name="frequently-asked-questions"></a>Preguntas más frecuentes

* ¿Hay un límite en el número de observedCertificates que se pueden configurar?
  No, la extensión de VM de Key Vault no tiene un límite para el número de observedCertificates.

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante Azure PowerShell. Para ver el estado de implementación de las extensiones de una máquina virtual determinada, ejecute el comando siguiente con Azure PowerShell.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**CLI de Azure**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>Registros y configuración

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).
