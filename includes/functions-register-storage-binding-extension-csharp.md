---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/16/2021
ms.author: glenga
ms.openlocfilehash: bda70e418b86c44750e9c826144fce8bbc027cbb
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830558"
---
::: zone pivot="programming-language-csharp"  

A excepción de los desencadenadores HTTP y el temporizador, los enlaces se implementan como paquetes de extensión. Ejecute el siguiente comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) en la ventana Terminal para agregar el paquete de extensión de Storage al proyecto.

# <a name="in-process"></a>[En proceso](#tab/in-process) 
```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage 
```
# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)
```bash
dotnet add package Microsoft.Azure.Functions.Worker.Extensions.Storage.Queues --prerelease
```
---
Ahora podrá agregar el enlace de salida de almacenamiento al proyecto.  
::: zone-end  
