---
title: 'PowerShell: Creación de un rol personalizado en un laboratorio'
description: En este artículo se proporciona un script de Azure PowerShell que crea un rol personalizado en un laboratorio de Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: f217ac5a0e65d0952942a15b30f1f6727af24599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654236"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Usar PowerShell para crear un rol personalizado en un laboratorio de Azure DevTest Labs

Este script de PowerShell de ejemplo crea un rol personalizado para usarlo en un laboratorio de Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisites
* **Un laboratorio**. Este script requiere que disponga de un laboratorio existente. 

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos: 

| Get-Help | Notas |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Obtiene las operaciones de un proveedor de recursos de Azure que son protegibles con RBAC de Azure. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Enumera todos los roles de Azure disponibles para su asignación. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Crea un rol personalizado. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/).

Encontrará más ejemplos de script de PowerShell de Azure Lab Services en los [ejemplos de PowerShell de Azure Lab Services](../samples-powershell.md).
