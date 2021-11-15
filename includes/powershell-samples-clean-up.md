---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/19/2021
ms.author: cephalin
ms.openlocfilehash: a1128c7e2e8a89c8d60e629437a12e2c1378babc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456010"
---
## <a name="clean-up-resources"></a>Limpieza de recursos

En los pasos anteriores, creó recursos de Azure en un grupo de recursos. Si prevé que no necesitará estos recursos en el futuro, ejecute el siguiente comando de PowerShell para eliminar el grupo de recursos:

```azurecli-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Este comando puede tardar varios segundos en ejecutarse.