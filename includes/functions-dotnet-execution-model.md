---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: ed981abd0ff849fb2a88164b2814794a48603ce7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559913"
---
En este artículo se admite la creación de ambos tipos de funciones de C# compiladas: 

| Modelo de ejecución | Descripción |
| --- | --- |
| **[En proceso](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| El código de función se ejecuta en el mismo proceso que el proceso de host de Functions. Para más información, consulte [Desarrollo de funciones de la biblioteca de clases de C# con Azure Functions](../articles/azure-functions/functions-dotnet-class-library.md). |
| **[Proceso aislado](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| El código de función se ejecuta en un proceso de trabajo de .NET independiente. Para más información, consulte la [Guía para ejecutar funciones en .NET 5.0 en Azure](../articles/azure-functions/dotnet-isolated-process-guide.md). |