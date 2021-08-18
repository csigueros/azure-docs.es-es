---
title: Conexión a Azure Alemania mediante PowerShell | Microsoft Docs
description: Información acerca de la administración de suscripciones en Azure Alemania mediante PowerShell
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs, devx-track-azurepowershell
ms.openlocfilehash: 3f5caa51b992d348fa077a0dbb96e210aa18f4de
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2021
ms.locfileid: "117029159"
---
# <a name="connect-to-azure-germany-by-using-powershell"></a>Conexión a Azure Alemania mediante PowerShell

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Para usar Azure PowerShell con Azure Alemania, es preciso conectarse a Azure Alemania, en lugar de a Azure global. Azure PowerShell se puede usar para administrar una suscripción de gran tamaño a través de un script o para acceder a características que no están actualmente disponibles en Azure Portal. Si ha usado PowerShell en Azure global, básicamente es lo mismo. Las diferencias en Azure Alemania son:

* Conexión a la cuenta
* Nombres de región

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Si aún no ha usado PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/).

Al iniciar PowerShell, tiene que indicar a Azure PowerShell que se conecte a Azure Alemania mediante la especificación de un parámetro de entorno. El parámetro garantiza que PowerShell se conecta a los puntos de conexión correctos. La colección de puntos de conexión se determina cuando se establece la conexión con la cuenta. Diferentes API requieren versiones diferentes del conmutador de entorno:

| Tipo de conexión | Get-Help |
| --- | --- |
| Comandos de [Azure (modelo de implementación clásica)](/powershell/azure) |`Add-AzureAccount -Environment AzureGermanCloud` |
| Comandos de [Azure (modelo de implementación de Resource Manager)](/powershell/azure) |`Connect-AzAccount -EnvironmentName AzureGermanCloud` |
| Comandos de [Azure Active Directory (modelo de implementación clásica)](/previous-versions/azure/jj151815(v=azure.100)) |`Connect-MsolService -AzureEnvironment AzureGermanyCloud` |
| Comandos de [Azure Active Directory (modelo de implementación Resource Manager)](../azure-resource-manager/management/deployment-models.md) |`Connect-AzureAD -AzureEnvironmentName AzureGermanyCloud` |

También puede usar el modificador `Environment` al conectarse a una cuenta de almacenamiento mediante `New-AzStorageContext` y, después, especificar `AzureGermanCloud`.

## <a name="determining-region"></a>Determinación de la región
Después de estar conectado, hay una diferencia adicional: las regiones que se usan como destino de un servicio. Cada servicio en la nube de Azure tiene una región diferente. Puede verlas en la página de disponibilidad del servicio. Normalmente, se usa la región en el parámetro `Location` de un comando.


| Nombre común | Nombre para mostrar | Nombre de la ubicación |
| --- | --- | --- |
| Centro de Alemania |`Germany Central` | `germanycentral` |
| Nordeste de Alemania |`Germany Northeast` | `germanynortheast` |


> [!NOTE]
> Como ocurre con PowerShell para Azure global, puede usar el nombre para mostrar o el de ubicación para el parámetro `Location`.
>
>

Si desea validar las regiones disponibles en Azure Alemania, puede ejecutar los siguientes comandos e imprimir la lista actual. Para las implementaciones clásicas, use el primer comando. Para las implementaciones con Resource Manager, use el segundo comando.

```azurepowershell
Get-AzureLocation
Get-AzLocation
```

Si desea conocer los entornos disponibles en Azure, puede ejecutar:

```azurepowershell
Get-AzureEnvironment
Get-AzEnvironment
```

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de cómo conectarse a Azure Alemania, consulte los siguientes recursos:

* [Conexión a Azure Alemania mediante la CLI de Azure](./germany-get-started-connect-with-cli.md)
* [Conexión a Azure Alemania mediante Visual Studio](./germany-get-started-connect-with-vs.md)
* [Conexión a Azure Alemania mediante Azure Portal](./germany-get-started-connect-with-portal.md)
