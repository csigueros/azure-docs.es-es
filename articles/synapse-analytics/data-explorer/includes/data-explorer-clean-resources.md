---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 633121c21bf09c608c0c40d6b87bf68edfbaf1f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478264"
---
## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando los recursos de Azure ya no sean necesarios, limpie los recursos que implementó eliminando el grupo de recursos. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Limpieza de recursos mediante Azure Portal

Elimine los recursos en Azure Portal siguiendo los pasos en [Limpieza de recursos](../ingest-data/data-explorer-ingest-event-hub-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Limpieza de recursos mediante PowerShell

Si Cloud Shell sigue abierto, no es necesario copiar ni ejecutar la primera línea (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```
