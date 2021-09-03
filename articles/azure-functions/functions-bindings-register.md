---
title: Registro de las extensiones de enlace de Azure Functions
description: Aprenda a registrar una extensión de enlace de Azure Functions basada en su entorno.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: bfcefb23fa68e2aa70d4680fcca2462b24322014
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730520"
---
# <a name="register-azure-functions-binding-extensions"></a>Registro de las extensiones de enlace de Azure Functions

A partir de Azure Functions versión 2.x, el entorno de ejecución de Functions solo incluye desencadenadores HTTP y de temporizador de forma predeterminada. Hay disponibles otros [desencadenadores y enlaces](./functions-triggers-bindings.md) como paquetes independientes.

Las aplicaciones de funciones de la biblioteca de clases .NET usan enlaces que se instalan en el proyecto como paquetes NuGet. Los conjuntos de extensiones permiten a las aplicaciones de funciones que no son .NET utilizar los mismos enlaces sin tener que tratar con la infraestructura de .NET.

En la tabla siguiente se indica cuándo y cómo registrar los enlaces.

| Entorno de desarrollo |Registro<br/> en Functions 1.x  |Registro<br/> en Functions 3.x/2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal de Azure|Automático|Automático<sup>*</sup>|
|Lenguajes que no son .NET|Automático|Use [conjuntos de extensiones](#extension-bundles) (recomendado) o [instale las extensiones de forma explícita](#explicitly-install-extensions).|
|Biblioteca de clases de C# con Visual Studio|[Uso de herramientas NuGet](#vs)|[Uso de herramientas NuGet](#vs)|
|Biblioteca de clases de C# con Visual Studio Code|N/D|[Uso de la CLI de .NET Core](#vs-code)|

<sup>*</sup> El portal usa conjuntos de extensiones.

## <a name="access-extensions-in-non-net-languages"></a>Acceso a las extensiones en lenguajes que no son .NET

Para Java, JavaScript, PowerShell, Python y las aplicaciones de funciones con controlador personalizado, se recomienda usar conjuntos de extensiones para acceder a los enlaces. En los casos en los que no se pueden usar conjuntos de extensiones, puede instalar explícitamente las extensiones de enlaces.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Conjuntos de extensiones

Los conjuntos de extensiones son una manera de agregar un conjunto compatible de extensiones de enlace a la aplicación de funciones. Los conjuntos de extensiones se habilitan en el archivo *host.json* de la aplicación.

Puede usar agrupaciones de extensiones con la versión 2.x y versiones posteriores del tiempo de ejecución de Functions.

Los conjuntos de extensiones tienen control de versiones. Cada versión contiene un conjunto específico de extensiones de enlace que se ha comprobado que funcionan juntas. Seleccione una versión del conjunto en función de las extensiones que necesite en la aplicación.

Para agregar un conjunto de extensiones a la aplicación de funciones, agregue la sección `extensionBundle` al archivo *host.json*. En muchos casos, Visual Studio Code y Azure Functions Core Tools lo agregarán automáticamente.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

En la tabla siguiente se enumeran las versiones disponibles actualmente del conjunto *Microsoft.Azure.Functions.ExtensionBundle* predeterminado y los vínculos a las extensiones que incluyen.

| Versión del conjunto | Versión en host.json | Extensiones incluidas |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | Consulte el archivo [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) utilizado para generar el conjunto. |
| 2.x | `[2.*, 3.0.0)` | Consulte el archivo [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) utilizado para generar el conjunto. |

> [!NOTE]
> Aunque puede especificar un intervalo de versiones personalizado en el archivo host.json, se recomienda usar un valor de versión de esta tabla.

### <a name="explicitly-install-extensions"></a>Instalación explícita de extensiones

Si no puede usar paquetes de extensión, utilice Azure Functions Core Tools localmente para instalar los paquetes de extensión específicos que requiere el proyecto.

> [!IMPORTANT]
> No se pueden instalar extensiones explícitamente en una aplicación de función que use conjuntos de extensiones. Antes de instalar las extensiones explícitamente, quite la sección `extensionBundle` en *host.json*.

En los siguientes elementos se describen algunos de los motivos por los que es posible que tenga que instalar las extensiones manualmente:

* Debe tener acceso a una versión específica de una extensión que no está disponible en un conjunto.
* Debe tener acceso a una extensión personalizada que no está disponible en un conjunto.
* Debe tener acceso a una combinación específica de extensiones que no está disponible en un único conjunto.

> [!NOTE]
> Para instalar manualmente extensiones mediante Core Tools, debe tener instalado el [SDK de .NET Core 2.x](https://dotnet.microsoft.com/download). Azure Functions Core Tools usa el SDK de .NET Core para instalar extensiones de NuGet. No es necesario tener conocimientos de .NET para usar las extensiones de Azure Functions.

Cuando se instalan las extensiones explícitamente, se agrega un archivo de proyecto .NET denominado extensions.csproj a la raíz del proyecto. Este archivo define el conjunto de paquetes NuGet requeridos por las funciones. Aunque puede trabajar con las [referencias de paquetes de NuGet](/nuget/consume-packages/package-references-in-project-files) en este archivo, las herramientas principales le permiten instalar extensiones sin tener que editar manualmente el archivo.

Hay varias maneras de usar las herramientas principales para instalar las extensiones necesarias en el proyecto local. 

#### <a name="install-all-extensions"></a>Instalación de todas las extensiones 

Utilice el comando siguiente para agregar automáticamente todos los paquetes de extensiones que usan los enlaces del proyecto local:

```command
func extensions install
```

El comando lee el archivo *function.json* para ver qué paquetes necesita, los instala y, luego, recompila el proyecto de extensiones. Agrega los nuevos enlaces en la versión actual, pero no actualiza los enlaces existentes. Use la opción `--force` para actualizar los enlaces existentes a la versión más reciente cuando instale otros nuevos. Para obtener más información, vea el [comando `func extensions install`](functions-core-tools-reference.md#func-extensions-install).

Si la aplicación de función usa enlaces que no reconoce Core Tools, debe instalar manualmente la extensión específica.

#### <a name="install-a-specific-extension"></a>Instalación de una extensión específica

Utilice el siguiente comando para instalar un paquete de extensiones específico en una versión específica, en este caso la extensión de almacenamiento:

```command
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```

Para obtener más información, vea el [comando `func extensions install`](functions-core-tools-reference.md#func-extensions-install).

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Instalación de las extensiones desde NuGet en los lenguajes .NET

En un proyecto de funciones basados en la biblioteca de clases de C#, se deben instalar las extensiones directamente. Los conjuntos de extensiones se han diseñado específicamente para proyectos que no están basados en la biblioteca de clases de C#.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> Biblioteca de clases de C\# con Visual Studio

En **Visual Studio**, puede instalar paquetes desde la Consola del Administrador de paquetes mediante el comando [Install-Package](/nuget/tools/ps-ref-install-package), tal como se muestra en el ejemplo siguiente:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

El nombre del paquete que se usa para un enlace determinado se proporciona en el artículo de referencia de ese enlace. Para obtener un ejemplo, consulte la [sección de paquetes del artículo de referencia de enlace de Service Bus](functions-bindings-service-bus.md#functions-1x).

Reemplace `<TARGET_VERSION>` en el ejemplo con una versión específica del paquete, como `3.0.0-beta5`. Las versiones válidas se enumeran en las páginas individuales del paquete en [NuGet.org](https://nuget.org). Las versiones principales que corresponden al tiempo de ejecución de Functions 1.x o 2.x se especifican en el artículo de referencia del enlace.

Si usa `Install-Package` para hacer referencia a un enlace, no es necesario usar [agrupaciones de extensiones](#extension-bundles). Este enfoque es específico de las bibliotecas de clases compiladas en Visual Studio.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Biblioteca de clases de C# con Visual Studio Code

En **Visual Studio Code**, instale paquetes para un proyecto de biblioteca de clases de C# desde el símbolo del sistema mediante el comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) de la CLI de .NET Core. En el ejemplo siguiente se muestra cómo agregar un enlace:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

La CLI de .NET Core solo puede utilizarse para el desarrollo de Azure Functions 2.x.

Reemplace `<BINDING_TYPE_NAME>` por el nombre del paquete que contiene el enlace que necesita. Puede encontrar el artículo de referencia de enlace deseado en la [lista de enlaces admitidos](./functions-triggers-bindings.md#supported-bindings).

Reemplace `<TARGET_VERSION>` en el ejemplo con una versión específica del paquete, como `3.0.0-beta5`. Las versiones válidas se enumeran en las páginas individuales del paquete en [NuGet.org](https://nuget.org). Las versiones principales que corresponden al tiempo de ejecución de Functions 1.x o 2.x se especifican en el artículo de referencia del enlace.

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Ejemplo de desencadenador y enlace de Azure Functions](./functions-bindings-example.md)
