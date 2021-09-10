---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 0198bcdd34cd92822c779329affeb1a405264bae
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830559"
---
## <a name="configure-your-local-environment"></a>Configuración del entorno local

Antes de empezar, debe disponer de lo siguiente:

::: zone pivot="programming-language-csharp"
[!INCLUDE [functions-cli-dotnet-prerequisites](functions-cli-dotnet-prerequisites.md)]
::: zone-end  
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other" 
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2).

+ [CLI de Azure](/cli/azure/install-azure-cli), versión 2.4 o posterior. 
:::zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Versiones de [Node.js](https://nodejs.org/), Active LTS y Maintenance LTS (se recomiendan 8.11.1 y 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 bits)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bits)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bits)](https://www.python.org/downloads/release/python-368/), que son compatibles con Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download).
::: zone-end
::: zone pivot="programming-language-java"  
+ [Kit para desarrolladores de Java](/azure/developer/java/fundamentals/java-jdk-long-term-support), versión 8 u 11. 

+ [Apache Maven](https://maven.apache.org), versión 3.0 o posterior.

::: zone-end
::: zone pivot="programming-language-other"
+ Herramientas de desarrollo para el lenguaje que utilice. En este tutorial se usa el [lenguaje de programación R](https://www.r-project.org/) como ejemplo.
::: zone-end

También necesita una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).