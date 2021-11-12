---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 70544909ae419cdf215bdad88c3ac19968f6e4f0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520303"
---
## <a name="add-to-your-functions-app"></a>Adición a la aplicación de Functions

### <a name="functions-2x-and-higher"></a>Functions 2.x y superiores

Para trabajar con el desencadenador y los enlaces, es necesario hacer referencia al paquete adecuado. En las bibliotecas de clases de .NET se usa el paquete NuGet, mientras que en los demás tipos de aplicaciones se emplea el conjunto de extensiones.

| Idioma                                        | Agregar mediante...                                   | Observaciones 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalación del [paquete NuGet], versión 3.x | |
| Script de C#, Java, JavaScript, Python, PowerShell | Registro de [conjunto de extensiones]          | Se recomienda usar la [extensión Azure Tools] con Visual Studio Code. |
| Script de C# (solo en línea en Azure Portal)         | Adición de un enlace                            | Para actualizar extensiones de enlace existentes sin tener que volver a publicar la aplicación de funciones, consulte [Actualización de las extensiones]. |

[Paquete NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[conjunto de extensiones]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Actualización de las extensiones]: ../articles/azure-functions/functions-bindings-register.md
[Extensión Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Extensión 5.x y superior de Event Hubs

Ahora está disponible una nueva versión de la extensión de enlaces de Event Hubs. Presenta la posibilidad de [conectarse con una identidad en lugar de un secreto](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection). Para obtener un tutorial sobre cómo configurar las aplicaciones de funciones con identidades administradas, consulte el tutorial [Creación de una aplicación de funciones con conexiones basadas en identidades](../articles/azure-functions/functions-identity-based-connections-tutorial.md). En el caso de las aplicaciones .NET, también cambian los tipos con los que se puede enlazar; así, los tipos `Microsoft.Azure.EventHubs` se reemplazan por otros tipos más recientes de [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs).

Esta versión de la extensión está disponible mediante la instalación de un [paquete NuGet], versión 5.x, o se puede agregar desde el conjunto de extensiones v3 mediante la incorporación de lo siguiente al archivo `host.json`:

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[3.3.0, 4.0.0)"
  }
}
```

Para obtener más información, consulte [Actualización de las extensiones].

[core tools]: ./functions-run-local.md
[conjunto de extensiones]: ./functions-bindings-register.md#extension-bundles
[Paquete NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Actualización de las extensiones]: ./functions-bindings-register.md
[Extensión Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Las aplicaciones de Functions 1.x tienen automáticamente una referencia al paquete NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), versión 2.x.

## <a name="hostjson-settings"></a>configuración de host.json
<a name="host-json"></a>

El archivo [host.json](../articles/azure-functions/functions-host-json.md#eventhub) contiene opciones de configuración que controlan el comportamiento del desencadenador de Event Hubs. La configuración varía en función de la versión de Azure Functions.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]