---
title: Desarrollo y ejecución de Azure Functions de forma local
description: Aprenda a codificar y probar las funciones de Azure Functions en la máquina local antes de ejecutarlas en Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9a1c760eb2630ddf1c6175625b53b86db9cabc34
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730517"
---
# <a name="code-and-test-azure-functions-locally"></a>Codificación y comprobación de las funciones de Azure Functions en un entorno local

Aunque puede programar y probar las funciones de Azure en [Azure Portal], muchos desarrolladores prefieren una experiencia de desarrollo local. Azure Functions le permite usar el editor de código y las herramientas de desarrollo que prefiera para crear y probar sus funciones en un equipo local. Las funciones locales pueden conectarse a servicios de Azure en directo, y puede depurar sus funciones en el equipo local con el entorno de tiempo de ejecución de Functions completo.

En este artículo se proporcionan vínculos a entornos de desarrollo específicos para su lenguaje preferido. También se proporcionan algunas instrucciones compartidas para el desarrollo local, como trabajar con el [archivo local.settings.json](#local-settings-file). 

## <a name="local-development-environments"></a>Entornos de desarrollo locales

La manera en la que desarrolla las funciones en el equipo local depende del [lenguaje](supported-languages.md) y las herramientas que prefiera. Los entornos de la siguiente tabla admiten el desarrollo local:

|Entorno                              |Lenguajes         |Descripción|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (biblioteca de clases)](functions-dotnet-class-library.md)<br/>[C# (proceso aislado) (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](./create-first-function-vs-code-powershell.md)<br/>[Python](functions-reference-python.md) | La [extensión de Azure Functions para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) agrega a Functions compatibilidad con VS Code. Requiere Core Tools. Admite el desarrollo en Linux, macOS y Windows, cuando se usa la versión 2.x de Core Tools. Para más información, vea [Creación de la primera función mediante Visual Studio Code](./create-first-function-vs-code-csharp.md). |
| [Símbolo del sistema o terminal](functions-run-local.md) | [C# (biblioteca de clases)](functions-dotnet-class-library.md)<br/>[C# (proceso aislado) (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](functions-reference-powershell.md)<br/>[Python](functions-reference-python.md) | [Azure Functions Core Tools] proporciona el entorno de tiempo de ejecución central y las plantillas para crear las funciones, lo que permite desarrollar localmente. La versión 2.x es compatible con el desarrollo en Linux, macOS y Windows. Todos los entornos usan Core Tools para el entorno de tiempo de ejecución local de Functions. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (biblioteca de clases)](functions-dotnet-class-library.md)<br/>[C# (proceso aislado) (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs) | Las herramientas de Azure Functions forman parte de la carga de trabajo de **desarrollo de Azure** de la versión [Visual Studio 2019](https://www.visualstudio.com/vs/) y versiones posteriores. Le permite compilar funciones en una biblioteca de clases y publicar el archivo .dll en Azure. Incluye Core Tools para realizar pruebas locales. Para más información, consulte [Desarrollo de Azure Functions con Visual Studio](functions-develop-vs.md) |
| [Maven](./create-first-function-cli-java.md) (varios) | [Java](functions-reference-java.md) | El arquetipo Maven admite Core Tools para habilitar el desarrollo de funciones con Java. La versión 2.x es compatible con el desarrollo en Linux, macOS y Windows. Para más información, consulte [Creación de la primera función con Java y Maven](./create-first-function-cli-java.md). También es compatible con el desarrollo con [Eclipse](functions-create-maven-eclipse.md) y [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Cada uno de estos entornos de desarrollo local le permite crear proyectos de aplicación de función y usar plantillas de Functions predefinidas para crear nuevas funciones. Cada una usa Core Tools para probar y depurar sus funciones en el entorno de tiempo de ejecución real de Functions en su propia máquina, igual que haría con cualquier otra aplicación. También puede publicar su proyecto de aplicación de función desde cualquiera de estos entornos en Azure.

## <a name="local-settings-file"></a>Archivo de configuración local

El archivo local.settings.json almacena la configuración de la aplicación y la configuración que usan las herramientas locales de desarrollo. La configuración del archivo local.settings.json solo se usa al ejecutar el proyecto de forma local. 

> [!IMPORTANT]  
> Dado que local.settings.json puede contener secretos, como cadenas de conexión, nunca debe almacenarlo en un repositorio remoto. Las herramientas que admiten Functions proporcionan maneras de sincronizar la configuración del archivo local.settings.json con la [configuración de la aplicación](functions-how-to-use-azure-function-app-settings.md#settings) en la aplicación de funciones en la que se implementa el proyecto.

El archivo de configuración local tiene la siguiente estructura:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Esta configuración se admite al ejecutar los proyectos de forma local:

| Configuración      | Descripción                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Cuando la configuración se establece en `true`, todos los valores se cifran con una clave de máquina local. Se usa con los comandos `func settings`. El valor predeterminado es `false`. El archivo local.settings.json se puede cifrar en su equipo local cuando contiene secretos, como las cadenas de conexión de servicios. El host descifra automáticamente la configuración cuando se ejecuta. Use el comando `func settings decrypt` antes de intentar leer la configuración cifrada localmente. |
| **`Values`** | Colección de opciones de configuración de la aplicación que se usan al ejecutar un proyecto de forma local. Estos pares clave-valor (cadena-cadena) corresponden a la configuración de la aplicación en su aplicación de funciones de Azure, como [`AzureWebJobsStorage`]. Muchos desencadenadores y enlaces tienen una propiedad que hace referencia a una configuración de la aplicación de cadena de conexión, por ejemplo, `Connection` para el [desencadenador del almacén de blobs](functions-bindings-storage-blob-trigger.md#configuration). Para estas propiedades, se necesita una configuración de la aplicación definida en la matriz `Values`. Consulte la tabla posterior, donde encontrará una lista de los valores que se usan con más frecuencia. <br/>Los valores deben ser cadenas y no objetos JSON o matrices. Los nombres de la configuración no pueden incluir dos puntos (`:`) ni un subrayado doble (`__`). Los caracteres dobles de subrayado están reservados para el tiempo de ejecución, mientras que el signo de dos puntos está reservado para admitir [la inserción de dependencias](functions-dotnet-dependency-injection.md#working-with-options-and-settings). |
| **`Host`** | La configuración que se muestra en esta sección permite personalizar el proceso de host de Functions al ejecutar los proyectos de forma local. Dicha configuración es independiente de la de host.json, que también se aplica al ejecutar proyectos en Azure. |
| **`LocalHttpPort`** | Establece el puerto predeterminado que se usa cuando al ejecutar el host de Functions local (`func host start` y `func run`). La opción de línea de comandos `--port` tiene prioridad sobre esta configuración. Por ejemplo, cuando trabaje en IDE de Visual Studio, puede cambiar el número de puerto. Para ello, vaya a la ventana "Propiedades de proyecto -> Depurar" y especifique de manera explícita el número de puerto en un comando `host start --port <your-port-number>` que se puede suministrar en el campo "Argumentos de aplicación". |
| **`CORS`** | Define los orígenes permitidos para el [uso compartido de recursos entre orígenes (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Los orígenes se proporcionan en una lista de valores separados por comas y sin espacios. Se admite el valor comodín (\*), lo que permite realizar solicitudes desde cualquier origen. |
| **`CORSCredentials`** |  Al establecer el valor en `true`, se permiten las solicitudes `withCredentials`. |
| **`ConnectionStrings`** | Identificador de colección. No use dicha colección para las cadenas de conexión que empleen enlaces de función. Esta colección solo la usan los marcos que habitualmente obtienen las cadenas de conexión de la sección `ConnectionStrings` de un archivo de configuración, como [Entity Framework](/ef/ef6/). Las cadenas de conexión de este objeto se agregan al entorno con el tipo de proveedor de [System.Data.SqlClient](/dotnet/api/system.data.sqlclient). Los elementos de esta colección no se publican en Azure con otra configuración de aplicación. Debe agregar explícitamente estos valores a la colección `Connection strings` de la configuración de la aplicación de función. Si quiere crear un objeto [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) en el código de la función, debe almacenar el valor de la cadena de conexión con las otras conexiones en **Configuración de la aplicación**, en el portal. |

Los siguientes valores de la aplicación se pueden incluir en la matriz **`Values`** cuando la ejecución se realiza en un entorno local:

| Configuración | Valores | Descripción |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Cadena de conexión de la cuenta de almacenamiento, o bien<br/>`UseDevelopmentStorage=true`| Contiene la cadena de conexión de una cuenta de Azure Storage. Se requiere cuando se utilizan desencadenadores distintos de HTTP. Para más información, consulte la referencia de [`AzureWebJobsStorage`].<br/>Si tiene el [emulador de Azure Storage](../storage/common/storage-use-emulator.md) instalado localmente y ha establecido [`AzureWebJobsStorage`] en `UseDevelopmentStorage=true`, Core Tools usará el emulador. Dicho emulador es útil durante el desarrollo, pero conviene probarlo con una conexión de almacenamiento real antes de la implementación.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Para deshabilitar una función cuando la ejecución se realizar en un entorno local, agregue `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` a la colección, donde `<FUNCTION_NAME>` es el nombre de la función. Para más información, consulte [Deshabilitamiento de funciones en Azure Functions](disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Indica el lenguaje de destino del runtime de Functions. Se requiere para la versión 2.x y superior del runtime de Functions. Este valor lo genera Azure Functions Core Tools para el proyecto. Para más información, consulte la referencia de [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime).|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Indica que PowerShell 7 se debe usar cuando la ejecución se realice localmente. Si no se establece, se usa PowerShell Core 6. Este valor solo se usa cuando la ejecución se realice localmente. Cuando se ejecuta en Azure, la versión del runtime de PowerShell viene determinada por el valor de configuración del sitio `powerShellVersion`, que se puede [establecer en el portal](functions-reference-powershell.md#changing-the-powershell-version). | 

## <a name="next-steps"></a>Pasos siguientes

+ Para obtener más información sobre el desarrollo local de funciones compiladas para C# con Visual Studio 2019, consulte [Desarrollo de Azure Functions con Visual Studio](functions-develop-vs.md).
+ Para más información sobre el desarrollo local de funciones con VS Code en un equipo Mac, Linux o Windows, vea el artículo de introducción de Visual Studio Code para su lenguaje preferido:
    + [Biblioteca de clases de C#](create-first-function-vs-code-csharp.md)
    + [C# (proceso aislado) (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)
    + [Java](create-first-function-vs-code-java.md)
    + [JavaScript](create-first-function-vs-code-node.md)
    + [PowerShell](create-first-function-vs-code-powershell.md)
    + [Python](create-first-function-vs-code-python.md)
    + [TypeScript](create-first-function-vs-code-typescript.md)
+ Para más información sobre cómo desarrollar funciones desde un terminal o el símbolo del sistema, consulte [Uso de Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
