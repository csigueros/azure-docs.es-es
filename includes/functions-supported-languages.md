---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6f0978a5177a499ccc48eda83c10b327d92b8b94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599968"
---
|Idioma                                 |1.x         |2.x| 3.x | 4.x (versión preliminar) |
|-----------------------------------------|------------|---| --- | --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)|Disponibilidad general (.NET Framework 4.8)|Disponibilidad general (.NET Core 2.1<sup>1</sup>)| Disponibilidad general (.NET Core 3.1)<br/>[Disponibilidad general (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) | Versión preliminar (.NET 6.0) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|Disponibilidad general (nodo 6)|Disponibilidad general (nodos 10 y 8)| Disponibilidad general (nodos 14, 12 y 10) | Versión preliminar (nodo 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|Disponibilidad general (.NET Framework 4.8)|Disponibilidad general (.NET Core 2.1<sup>1</sup>)| Disponibilidad general (.NET Core 3.1) | Versión preliminar (.NET 6.0) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/D|Disponibilidad general (Java 8)| Disponibilidad general (Java 11 y 8)| Versión preliminar (Java 11 y 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/D|Disponibilidad general (PowerShell Core 6)| Disponibilidad general (PowerShell 7.0 y Core 6)| Versión preliminar (PowerShell 7.0)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/D|Disponibilidad general (Python 3.7 y 3.6)| Disponibilidad general (Python 3.9, 3.8, 3.7, y 3.6)| Versión preliminar (Python 3.9, 3.8)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript)<sup>2</sup> |N/D|GA| GA | Versión preliminar |

<sup>1</sup> Las aplicaciones de biblioteca de clases de .NET dirigidas a la versión del entorno de ejecución 2.x se ejecutan en .NET Core 3.1 en el modo de compatibilidad de .NET Core 2.x. Para más información, consulte [Consideraciones sobre Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Se admite mediante la transpilación de JavaScript.

Para más información sobre las versiones del lenguaje que se admiten, consulte el artículo de la guía para desarrolladores específica del lenguaje.   
Para información sobre los cambios planeados en la compatibilidad de lenguaje, consulte [Azure roadmap](https://azure.microsoft.com/roadmap/?tag=functions).
