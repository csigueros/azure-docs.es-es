---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/23/2021
ms.author: glenga
ms.openlocfilehash: 8d580bbfd87f1d1df9d36be95a98c65d80736806
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224321"
---
## <a name="language-support-details"></a>Detalles de la compatibilidad con idiomas 

En la tabla siguiente se muestran los idiomas admitidos por Functions que se pueden ejecutar en Linux o Windows. También indica si el idioma admite la edición en Azure Portal. El idioma se basa en la opción **Pila del entorno en tiempo de ejecución** que elija al [crear la aplicación de función en Azure Portal](../articles/azure-functions/functions-create-function-app-portal.md#create-a-function-app). Esto es igual que la opción `--worker-runtime` cuando se usa el comando `func init` en Azure Functions Core Tools. 

| Idioma | Pila en tiempo de ejecución | Linux | Windows | Edición en el portal<sup>1</sup> |
|:--- |:-- |:--|:--- |:--- |
| [Biblioteca de clases C#](../articles/azure-functions/functions-dotnet-class-library.md)<sup>2</sup> |.NET|✓ |✓ | | 
| [Script de C#](../articles/azure-functions/functions-reference-csharp.md) | .NET | ✓ |✓ |✓ |
| [JavaScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ | ✓ |
| [Python](../articles/azure-functions/functions-reference-python.md) | Python |✓ | | |
| [Java](../articles/azure-functions/functions-reference-java.md) | Java |✓ |✓ | |
| [PowerShell](../articles/azure-functions/functions-reference-powershell.md) |PowerShell Core | |✓ |✓ |
| [TypeScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ |  |
| [Go/Rust/otro](../articles/azure-functions/functions-custom-handlers.md) | Controladores personalizados |✓ |✓ | |

<sup>1</sup> Cuando se ejecuta en Linux, la edición en el portal solo se admite en un [plan dedicado (App Service)](../articles/azure-functions/dedicated-plan.md).   
<sup>2</sup> Actualmente no se pueden crear en el portal aplicaciones de función que se ejecuten en .NET 5.0. Para más información, consulte [Desarrollo y publicación de funciones de .NET 5 mediante Azure Functions](../articles/azure-functions/dotnet-isolated-process-guide.md). 

Para más información, consulte [Compatibilidad con sistema operativo/entorno de tiempo de ejecución](../articles/azure-functions/functions-scale.md#operating-systemruntime). 

Cuando la edición en el portal no está disponible, debe [desarrollar las funciones localmente](../articles/azure-functions/functions-develop-local.md#local-development-environments) en su lugar.