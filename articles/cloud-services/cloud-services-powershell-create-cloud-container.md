---
title: Creación de un contenedor de servicio en la nube (clásico) con PowerShell | Microsoft Docs
description: En este artículo se explica cómo crear un contenedor de servicios en la nube con PowerShell. El contenedor hospeda roles web y roles de trabajo.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c466182d12662453ab649ef9e7f4039b0acf967c
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823584"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Uso de un comando de Azure PowerShell para crear un contenedor vacío de servicio en la nube (clásico)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

En este artículo se explica cómo crear rápidamente un contenedor de Cloud Services mediante cmdlets de Azure PowerShell. Siga estos pasos:

1. Instale el cmdlet de Microsoft Azure PowerShell desde la página de [descargas de Azure PowerShell](https://aka.ms/webpi-azps) .
2. Abra un símbolo del sistema de PowerShell.
3. Use [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) para iniciar sesión.

   > [!NOTE]
   > Para más instrucciones acerca de cómo instalar el cmdlet de Azure PowerShell y conectarse a la suscripción de Azure, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/).
   >
   >
4. Use el cmdlet **New-AzureService** para crear un contenedor de servicios en la nube de Azure vacío.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Para invocar el cmdlet, siga este ejemplo:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Para obtener más información sobre cómo crear el servicio en la nube de Azure, ejecute:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Pasos siguientes

* Para administrar la implementación de servicios en la nube, consulte los comandos [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService) y [Set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice). Para más información, también puede consultar [Configuración de servicios en la nube](cloud-services-how-to-configure-portal.md) .
* Para publicar el proyecto de servicio en la nube en Azure, consulte el código de ejemplo de **PublishCloudService.ps1** del [repositorio de servicios en la nube archivado](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).