---
title: 'Ejemplo de script de Azure PowerShell: creación de un tema personalizado | Microsoft Docs'
description: En este artículo se proporciona un script de Azure PowerShell de ejemplo que muestra cómo crear un tema personalizado de Azure Event Grid.
services: event-grid
documentationcenter: na
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2021
ms.openlocfilehash: 86ecab935209a21fbb07ae670b338f85ca455deb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680478"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>Creación de un tema personalizado de Event Grid con PowerShell

Este script crea un tema personalizado de Event Grid.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear el tema personalizado. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [New-AzEventGridTopic](/powershell/module/az.eventgrid/new-azeventgridtopic) | Permite crear un tema personalizado de Event Grid. |

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](../overview.md).
* Para más información sobre PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/get-started-azureps).
