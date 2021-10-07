---
title: 'Script de PowerShell: establecer los tamaños de máquina virtual permitidos'
description: Este artículo incluye un script de PowerShell de ejemplo que establece los tamaños de máquina virtual en Azure Lab Services.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: ef5e755caf5b5f1477b947798869a67663f71ee6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557694"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Uso de PowerShell para establecer los tamaños de máquina virtual permitidos en Azure Lab Services

Este script de PowerShell de ejemplo establece los tamaños de máquina virtual en Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisites
* **Un laboratorio**. Este script requiere que disponga de un laboratorio existente. 

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos: 

| Get-Help | Notas |
|---|---|
| Find-AzResource | Busca recursos en función de los parámetros especificados. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtiene recursos. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica un recurso. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Crea un recurso. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/).

Encontrará más ejemplos de script de PowerShell de Azure Lab Services en los [ejemplos de PowerShell de Azure Lab Services](../samples-powershell.md).
