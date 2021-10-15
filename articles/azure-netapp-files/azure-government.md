---
title: Azure NetApp Files para Azure Government  | Microsoft Docs
description: Describe cómo conectarse a Azure Government para usar Azure NetApp Files y la disponibilidad de características de Azure NetApp Files en Azure Government.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: b-juche
ms.openlocfilehash: 4dbdc8086b59553474d05297cdaf030d897d441e
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234079"
---
# <a name="azure-netapp-files-for-azure-government"></a>Azure NetApp Files para Azure Government 

[Microsoft Azure Government](../azure-government/documentation-government-welcome.md) ofrece una nube dedicada que permite a las agencias gubernamentales y sus asociados transformar cargas de trabajo críticas en la nube.  

En este artículo se describe la disponibilidad de características de Azure NetApp Files en Azure Government. También se muestra cómo acceder al servicio Azure NetApp Files en Azure Government.

## <a name="feature-availability"></a>Disponibilidad de características

Para ver las regiones de Azure Government admitidas por Azure NetApp Files, consulte la *[página Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)* .  

Todas las [características de Azure NetApp Files](whats-new.md) disponibles en la nube pública de Azure también están disponibles en las regiones de Azure Government admitidas, ***excepto en las características enumeradas en la tabla siguiente***: 

| Características de Azure NetApp Files | Disponibilidad de la nube pública de Azure |  Disponibilidad de Azure Government |
|:--- |:--- |:--- |
| Replicación de Azure NetApp Files entre regiones | Versión preliminar pública | [Limitados](cross-region-replication-introduction.md#supported-region-pairs) |
| Copia de seguridad de archivos de Azure NetApp Files | Versión preliminar pública | No |

## <a name="portal-access"></a>Acceso al portal

Los usuarios de Azure Government pueden acceder a Azure NetApp Files al apuntar sus exploradores a **portal.azure.us**.El nombre del sitio del portal es **Microsoft Azure Government**.Para obtener más información, consulte [Conexión a Azure Government mediante el portal](../azure-government/documentation-government-get-started-connect-with-portal.md).   

![Captura de pantalla del portal de Azure Government que resalta portal.azure.us como dirección URL](../media/azure-netapp-files/azure-government.jpg)

Desde el portal de Microsoft Azure Government, puede acceder a Azure NetApp Files de la misma manera que lo haría en Azure Portal.Por ejemplo, puede escribir **Azure NetApp Files** en el cuadro de recursos de búsqueda del portal, y a continuación, seleccionar **Azure NetApp Files** en la lista que aparece.  

Puede seguir la documentación de [Azure NetApp Files](/azure/azure-netapp-files/) para obtener más información sobre el uso del servicio.

## <a name="azure-cli-access"></a>Acceso a la CLI de Azure

Puede conectarse a Azure Government estableciendo el nombre de la nube en `AzureUSGovernment` y, a continuación, continuar con el inicio de sesión como lo haría normalmente con el comando `az login`. Después de ejecutar el comando de inicio de sesión, se iniciará un explorador donde especificar las credenciales de Azure Government correspondientes.  

```azurecli 

az cloud set --name AzureUSGovernment 

``` 

Para confirmar que la nube se ha establecido en `AzureUSGovernment`, ejecute: 

```azurecli 

az cloud list --output table 

``` 

Este comando producirá una tabla con ubicaciones en la nube de Azure. La entrada de la columna `isActive` para `AzureUSGovernment` debe decir `true`.  

Consulte [Conexión de Azure Government con la CLI de Azure](../azure-government/documentation-government-get-started-connect-with-cli.md) para obtener más información.

## <a name="rest-api-access"></a>Acceso a la API REST

Los puntos de conexión para Azure Government son diferentes de los puntos de conexión comerciales de Azure. Para obtener una lista de los distintos puntos de conexión, consulte la [Guía para desarrolladores](../azure-government/compare-azure-government-global-azure.md#guidance-for-developers) de Azure Government.

## <a name="powershell-access"></a>Acceso a PowerShell

Al conectarse a Azure Government a través de PowerShell, debe especificar un parámetro de entorno para asegurarse de que se conecta a los puntos de conexión correctos. Desde allí, puede continuar con el uso de Azure NetApp Files como lo haría habitualmente con PowerShell. 

| Tipo de conexión | Get-Help | 
| --- | --- | 
| Comandos de [Azure](/powershell/module/az.accounts/Connect-AzAccount) |`Connect-AzAccount -EnvironmentName AzureUSGovernment` | 
| Comandos de [Azure Active Directory](/powershell/module/azuread/connect-azuread) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` | 
| Comandos de [Azure (modelo de implementación clásica)](/powershell/module/servicemanagement/azure.service/add-azureaccount) |`Add-AzureAccount -Environment AzureUSGovernment` | 
| Comandos de [Azure Active Directory (modelo de implementación clásica)](/previous-versions/azure/jj151815(v=azure.100)) |`Connect-MsolService -AzureEnvironment UsGovernment` | 

Consulte [Conexión a Azure Government con PowerShell](../azure-government/documentation-government-get-started-connect-with-ps.md) para más información.

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es Azure Government?](../azure-government/documentation-government-welcome.md)
* [Novedades de Azure NetApp Files](whats-new.md)
* [Comparación de Azure Government y Azure global](../azure-government/compare-azure-government-global-azure.md)
* [API REST de Azure NetApp Files](azure-netapp-files-develop-with-rest-api.md)
* [API de REST de Azure NetApp Files mediante PowerShell](develop-rest-api-powershell.md)
