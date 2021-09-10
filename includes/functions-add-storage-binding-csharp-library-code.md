---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 7537ad41aaac6fab1eb591300b561bba4167d85d
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829566"
---
# <a name="in-process"></a>[En proceso](#tab/in-process)

Agregue código que utilice el objeto de enlace de salida `msg` para crear un mensaje de la cola. Agregue este código antes de la devolución del método.

:::code language="csharp" range="28-32" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" :::

En este momento, la función debe tener el aspecto siguiente:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-36":::

# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)

Reemplace la clase `HttpExample` existente por el código siguiente:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="11-32":::

---