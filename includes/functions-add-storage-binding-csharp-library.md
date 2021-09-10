---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 4f2aa82388882c192213b168faedd4f61069ae64
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829565"
---
En un proyecto de C#, los enlaces se definen como atributos de enlace en el método de función. Las definiciones específicas dependen de si la aplicación se ejecuta dentro de proceso (biblioteca de clases de C#) o en un proceso aislado.  

# <a name="in-process"></a>[En proceso](#tab/in-process)

Abra el archivo de proyecto *HttpExample.cs* y agregue el parámetro siguiente a la definición del método `Run`:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

El parámetro `msg` es de tipo `ICollector<T>`, que representa una colección de mensajes escritos en un enlace de salida cuando se completa la función. En este caso, la salida es una cola de almacenamiento denominada `outqueue`. La cadena de conexión de la cuenta de Storage la establece `StorageAccountAttribute`. Este atributo indica la configuración que contiene la cadena de conexión de la cuenta de Storage y se puede aplicar en el nivel de clase, método o parámetro. En este caso, puede omitir `StorageAccountAttribute`, puesto que ya está usando la cuenta de almacenamiento predeterminada.

La definición del método Run debe ahora parecerse a la siguiente:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::

# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)

Abra el archivo de proyecto *HttpExample.cs* y agregue la siguiente clase `MultiResponse`:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="33-38":::

La clase `MultiResponse` permite escribir en una cola de almacenamiento denominada `outqueue` y en un mensaje HTTP de operación correcta. Dado que el atributo `QueueOutput` se aplica a una matriz de cadenas, se podrían enviar varios mensajes a la cola. 

La cadena de conexión de la cuenta de Storage se establece con la propiedad `Connection`. En este caso, puede omitir `Connection`, puesto que ya está usando la cuenta de almacenamiento predeterminada.

---