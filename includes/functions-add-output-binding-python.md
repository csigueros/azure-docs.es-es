---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9c3dd4ecede4e8087b2ec9ad470d5ab415ea6e35
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803505"
---
Actualice *HttpExample\\\_\_init\_\_.py* para que se ajuste al siguiente código y agregue el parámetro `msg` a la definición de la función y `msg.set(name)` en la instrucción `if name:`.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

El parámetro `msg` es una instancia de [`azure.functions.Out class`](/python/api/azure-functions/azure.functions.out). Su método `set` escribe un mensaje de cadena en la cola; en este caso, el nombre que se pasa a la función en la cadena de consulta de la dirección URL.
