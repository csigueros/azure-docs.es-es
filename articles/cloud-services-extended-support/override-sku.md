---
title: Invalidación de la información de SKU sobre CSCFG/CSDEF para Azure Cloud Services (soporte extendido)
description: En este artículo se describe cómo invalidar la información de SKU en los archivos .cscfg y .csdef para Azure Cloud Services (soporte extendido).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31ff47cd4e110e62769678836bdd803b71369e0b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437885"
---
# <a name="override-sku-settings-in-cscfg-and-csdef-files-for-cloud-services-extended-support"></a>Invalidación de la configuración de SKU en los archivos .cscfg y .csdef para Cloud Services (soporte extendido)

En este artículo se describe cómo actualizar el tamaño de rol y el recuento de instancias en Azure Cloud Services mediante la propiedad **allowModelOverride**. Cuando se usa esta propiedad, no es necesario actualizar los archivos de configuración del servicio (.cscfg) y de definición del servicio (.csdef). Por lo tanto, puede escalar y reducir vertical u horizontalmente el servicio en la nube sin necesidad de volver a empaquetarlo e implementarlo.

## <a name="set-the-allowmodeloverride-property"></a>Establecimiento de la propiedad allowModelOverride
Puede establecer la propiedad **allowModelOverride** en `true` o `false`. 
* Cuando **allowModelOverride** se establece en `true`, una llamada API actualizará el tamaño de rol y el recuento de instancias para el servicio en la nube sin validar los valores con los archivos .csdef y .cscfg. 
   > [!Note]
   > El archivo .cscfg se actualizará para reflejar el recuento de instancias de rol. El archivo .csdef (insertado en .cspkg) conservará los valores anteriores.

* Cuando **allowModelOverride** se establece en `false`, una llamada API producirá un error si los valores de tamaño de rol y recuento de instancias no coinciden con los valores de los archivos .csdef y .cscfg, respectivamente.

El valor predeterminado es `false`. Si la propiedad se restablece en `false` después de establecerse en `true`, los archivos .csdef y .cscfg se volverán a validar.

En los ejemplos siguientes se muestra cómo establecer la propiedad **allowModelOverride** mediante una plantilla de Azure Resource Manager (ARM), PowerShell o el SDK.

### <a name="arm-template"></a>Plantilla ARM
Si la propiedad **allowModelOverride** se establece en `true`, se actualizará el servicio en la nube con las propiedades de rol definidas en la sección `roleProfile`:
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
Si el modificador `AllowModelOverride` se establece en el nuevo cmdlet `New-AzCloudService`, se actualizará el servicio en la nube con las propiedades de SKU definidas en el perfil de rol:
```powershell
New-AzCloudService ` 
-Name "ContosoCS" ` 
-ResourceGroupName "ContosOrg" ` 
-Location "East US" `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
Si la variable `AllowModelOverride` se establece en `true`, se actualizará el servicio en la nube con las propiedades de SKU definidas en el perfil de rol:

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate("ContosOrg", "ContosoCS", cloudService);
```
### <a name="azure-portal"></a>Azure portal
Azure Portal no permite usar la propiedad **allowModelOverride** para invalidar el tamaño de rol y el recuento de instancias en los archivos .csdef y .cscfg. 


## <a name="next-steps"></a>Pasos siguientes 
- Consulte los [requisitos previos de implementación](deploy-prerequisite.md) de Cloud Services (soporte extendido).
- Vea las [preguntas más frecuentes](faq.yml) sobre Cloud Services (soporte extendido).
