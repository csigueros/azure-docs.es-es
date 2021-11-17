---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: f576840f87387ba6896c6fda3a5ef663cedaffaf
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132040307"
---
## <a name="supported-versions"></a>Versiones compatibles

Las versiones del entorno en tiempo de ejecución de Functions funcionan con versiones específicas de .NET. Para obtener más información sobre las versiones de Functions, consulte [Introducción a las versiones de tiempo de ejecución de Azure Functions](../articles/azure-functions/functions-versions.md). La compatibilidad con versiones depende de si las funciones se ejecutan en proceso o fuera de proceso (aislado). 

En la tabla siguiente se muestra el nivel más alto de .NET Core o .NET Framework que se puede usar con una versión específica de Functions. 

| Versiones del entorno en tiempo de ejecución de Functions | En proceso<br/>([Biblioteca de clases .NET](../articles/azure-functions/functions-dotnet-class-library.md)) | Fuera de proceso<br/>([.NET aislado](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x | .NET 6.0 | .NET 6.0 |
| Functions 3.x | .NET Core 3.1 | .NET 5.0<sup>1</sup> |
| Functions 2.x | .NET Core 2.1<sup>2</sup> | N/D |
| Functions 1.x | .NET Framework 4.8 | N/D |


<sup>1</sup> El proceso de compilación también requiere el [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download).   
<sup>2</sup> Para más información, consulte [Consideraciones sobre Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).     

Para obtener las últimas noticias sobre las versiones de Azure Functions, incluida la eliminación de versiones secundarias específicas anteriores, revise los [anuncios de Azure App Service](https://github.com/Azure/app-service-announcements/issues).
