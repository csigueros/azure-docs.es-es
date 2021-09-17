---
title: 'Ejemplos de Azure PowerShell: actualización de una instancia de Azure Cloud Services (soporte extendido)'
description: Scripts de ejemplo para actualizar implementaciones de un servicio en la nube de Azure (soporte extendido)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22d02ef7c395ae6f7cdbbf739ed5107436bd169c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751093"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>Actualización de una instancia de Azure Cloud Services (soporte extendido)

Los siguientes ejemplos abordan diversas formas de actualizar una implementación existente de un servicio en la nube de Azure (soporte extendido).

## <a name="add-an-extension-to-existing-cloud-service"></a>Incorporación de una extensión a un servicio en la nube existente
El siguiente conjunto de comandos agrega una extensión RDP al servicio en la nube existente denominado ContosoCS que pertenece al grupo de recursos denominado ContosOrg.
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing cloud service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $rdpExtension
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>Eliminación de todas las extensiones de un servicio en la nube
El siguiente conjunto de comandos quita todas las extensiones del servicio en la nube existente denominado ContosoCS que pertenece al grupo de recursos denominado ContosOrg.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfile.Extension = @()
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>Eliminación de la extensión de escritorio remoto de un servicio en la nube
El siguiente conjunto de comandos quita la extensión RDP del servicio en la nube existente denominado ContosoCS que pertenece al grupo de recursos denominado ContosOrg.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension | Where-Object { $_.Name -ne "RDPExtension" }
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>Instancias de rol de escalabilidad horizontal/reducción horizontal
El siguiente conjunto de comandos muestra cómo escalar horizontalmente y verticalmente el recuento de instancias de rol del servicio en la nube denominado ContosoCS que pertenece al grupo de recursos denominado ContosOrg.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfile.Role | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfile.Role | Where-Object {$_.Name -eq "ContosoFrontend"}
$role.SkuCapacity -= 1

# Update cloud service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre Azure Cloud Services (soporte extendido), consulte [Introducción a Azure Cloud Services (soporte extendido)](overview.md).
- Visite el [repositorio de ejemplos de Cloud Services (soporte extendido)](https://github.com/Azure-Samples/cloud-services-extended-support)
