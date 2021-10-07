---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: 729275766806bf5fe47d35b04ddc58b11c58217b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908580"
---
## <a name="supported-versions"></a>Versiones compatibles

Las versiones del entorno en tiempo de ejecución de Functions funcionan con versiones específicas de .NET. Para obtener más información sobre las versiones de Functions, consulte [Introducción a las versiones de tiempo de ejecución de Azure Functions](../articles/azure-functions/functions-versions.md). La compatibilidad con versiones depende de si las funciones se ejecutan en proceso o fuera de proceso (aislado). 

En la tabla siguiente se muestra el nivel más alto de .NET Core o .NET Framework que se puede usar con una versión específica de Functions. 

| Versiones del entorno en tiempo de ejecución de Functions | En proceso<br/>([Biblioteca de clases .NET](../articles/azure-functions/functions-dotnet-class-library.md)) | Fuera de proceso<br/>([.NET aislado](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x (versión preliminar) | .NET 6.0 (versión preliminar)| .NET 6.0 (versión preliminar)<sup>2</sup> |
| Functions 3.x | .NET Core 3.1 | .NET 5.0 |
| Functions 2.x | .NET Core 2.1<sup>1</sup> | N/D |
| Functions 1.x | .NET Framework 4.8 | N/D |

<sup>1</sup> Para más información, consulte [Consideraciones sobre Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).    
<sup>2</sup> Actualmente, solo puede crear funciones de proceso aisladas mediante Azure Functions Core Tools. Para más información, consulte [Inicio rápido: Creación de una función de C# en Azure desde la línea de comandos](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process).  

Para obtener las últimas noticias sobre las versiones de Azure Functions, incluida la eliminación de versiones secundarias específicas anteriores, revise los [anuncios de Azure App Service](https://github.com/Azure/app-service-announcements/issues).
