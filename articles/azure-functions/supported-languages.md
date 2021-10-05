---
title: Lenguajes admitidos en Azure Functions
description: Obtenga información sobre los lenguajes admitidos (GA) y los que se encuentran en versión preliminar, y las maneras de ampliar el desarrollo de Functions a otros lenguajes.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: d0db327a1f5b4189f9715687e3efa8fbe5b668db
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599955"
---
# <a name="supported-languages-in-azure-functions"></a>Lenguajes admitidos en Azure Functions

En este artículo se explican los niveles de soporte que se ofrecen para los lenguajes que puede usar con Azure Functions. También se describen las estrategias para crear funciones mediante lenguajes no admitidos de forma nativa.

[!INCLUDE [functions-support-levels](../../includes/functions-support-levels.md)]

## <a name="languages-by-runtime-version"></a>Lenguaje por versión de entorno de tiempo de ejecución 

Hay disponibles [varias versiones del entorno en tiempo de ejecución de Azure Functions](functions-versions.md). En la tabla siguiente se indica qué lenguajes se admiten en cada versión del sistema de tiempo de ejecución.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="language-major-version-support"></a>Compatibilidad con la versión principal del lenguaje

Azure Functions garantiza la compatibilidad con las versiones principales de los lenguajes de programación admitidos. En la mayoría de los lenguajes, se publican versiones secundarias o revisiones para actualizar una versión principal compatible. Algunos ejemplos de versiones secundarias o revisiones son Python 3.9.1 y Node 14.17. Una vez que estén disponibles nuevas versiones secundarias de los lenguajes admitidos, las versiones secundarias empleadas por las aplicaciones de Functions se actualizarán automáticamente a estas versiones secundarias o revisiones más recientes. 

> [!NOTE]
>Dado que Azure Functions puede eliminar la compatibilidad con versiones secundarias anteriores en cualquier momento después de que esté disponible una nueva versión secundaria, no debe vincular las aplicaciones de Functions a una versión secundaria o revisión específica de un lenguaje de programación.  
>

## <a name="custom-handlers"></a>Controladores personalizados

Los controladores personalizados son servidores web ligeros que reciben eventos del host de Azure Functions. Cualquier lenguaje que admita primitivas de HTTP puede implementar un controlador personalizado. Esto significa que los controladores personalizados se pueden usar para crear funciones en lenguajes que no se admiten oficialmente. Para obtener más información, consulte [Controladores personalizados de Azure Functions](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Extensibilidad de lenguaje

A partir de la versión 2.x, el entorno de tiempo de ejecución está diseñado para ofrecer [extensibilidad de lenguaje](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Los lenguajes JavaScript y Java en el entorno de ejecución 2.x están incluidos con esta extensibilidad.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo desarrollar funciones en los lenguajes admitidos, consulte los siguientes recursos:

+ [Referencia para desarrolladores de la biblioteca de clases C#](functions-dotnet-class-library.md)
+ [Referencia para desarrolladores de scripts en C#](functions-reference-csharp.md)
+ [Referencia para desarrolladores de Java](functions-reference-java.md)
+ [Referencia para desarrolladores de JavaScript](functions-reference-node.md)
+ [Referencia para desarrolladores de PowerShell](functions-reference-powershell.md)
+ [Referencia para desarrolladores de Python](functions-reference-python.md)
+ [Referencia para desarrolladores de TypeScript](functions-reference-node.md#typescript)
