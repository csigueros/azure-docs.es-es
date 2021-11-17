---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: 7257f70acb8b931791f3f08437ba61b05e1b4f6d
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026699"
---
En este artículo se admite la creación de ambos tipos de funciones de C# compiladas: 

| Modelo de ejecución | Descripción |
| --- | --- |
| **[En proceso](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| El código de función se ejecuta en el mismo proceso que el proceso de host de Functions. Admite .NET Core 3.1 y .NET 6.0. Para más información, consulte [Desarrollo de funciones de la biblioteca de clases de C# con Azure Functions](../articles/azure-functions/functions-dotnet-class-library.md). |
| **[Proceso aislado](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| El código de función se ejecuta en un proceso de trabajo de .NET independiente. Admite .NET 5.0 y .NET 6.0. Para más información, consulte [Guía para la ejecución de funciones en .NET 5.0 en Azure](../articles/azure-functions/dotnet-isolated-process-guide.md). |