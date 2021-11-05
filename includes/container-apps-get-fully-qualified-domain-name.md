---
author: craigshoemaker
ms.service: app-service
ms.topic: include
ms.date: 10/25/2021
ms.author: cshoe
ms.openlocfilehash: 67821760435fdac37c51400190c1afe44996d014
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090863"
---
### <a name="get-fully-qualified-domain-name"></a>Obtención de un nombre de dominio completo

El comando `az containerapp show` devuelve el nombre de dominio completo de una aplicación contenedora.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --query configuration.ingress.fqdn
```

---

En este ejemplo, reemplace los marcadores de posición rodeados por `<>` por sus valores.

El valor devuelto por este comando es similar a un nombre de dominio como el del ejemplo siguiente:

```console
myapp.happyhill-70162bb9.canadacentral.azurecontainerapps.io
```
