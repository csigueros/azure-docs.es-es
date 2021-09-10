---
title: Uso de Azure Functions Core Tools
description: Aprenda a programar y probar funciones de Azure en el símbolo del sistema o el terminal del equipo local antes de ejecutarlas en Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 04e6f9ab74ab30b73f323e5796c159e07655f836
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861670"
---
# <a name="work-with-azure-functions-core-tools"></a>Uso de Azure Functions Core Tools

Azure Functions Core Tools le permite desarrollar y probar funciones en el equipo local desde el símbolo del sistema o terminal. Las funciones locales pueden conectarse a servicios de Azure en directo, y puede depurar sus funciones en el equipo local con el tiempo de ejecución de Functions completo. Incluso puede implementar una aplicación de función en su suscripción a Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Para desarrollar funciones en el equipo local y publicarlas en Azure utilizando Core Tools siga estos pasos básicos:

> [!div class="checklist"]
> * [Instale Core Tools y sus dependencias.](#v2)
> * [Cree un proyecto de aplicación de funciones a partir de una plantilla específica del idioma.](#create-a-local-functions-project)
> * [Registre extensiones de desencadenadores y enlace.](#register-extensions)
> * [Defina el almacenamiento y otras conexiones.](#local-settings)
> * [Cree una función desde un desencadenador y una plantilla específica del idioma.](#create-func)
> * [Ejecute la función de forma local.](#start)
> * [Publique el proyecto en Azure.](#publish)

## <a name="core-tools-versions"></a>Versiones de Core Tools

Hay tres versiones de Azure Functions Core Tools.<sup>*</sup>La versión que use depende del entorno de desarrollo local, la [elección del idioma](supported-languages.md) y el nivel de compatibilidad requerido:

+ [**Versión 3.x o 2.x**](#v2): es compatible con la [versión 3.x o 2.x del entorno de ejecución de Azure Functions](functions-versions.md). Estas versiones son compatibles con [Windows](?tabs=windows#v2), [macOS](?tabs=macos#v2) y [Linux](?tabs=linux#v2). Además, usan administradores de paquetes o específicos de la plataforma o npm para la instalación.

+ **Versión 1.x**: es compatible con la versión 1.x del entorno en tiempo de ejecución de Azure Functions. Esta versión de las herramientas solo se admite en equipos con Windows y se instala desde un [paquete npm](https://www.npmjs.com/package/azure-functions-core-tools).

Solo puede instalar una versión de Core Tools en un equipo determinado. A menos que se indique lo contrario, los ejemplos de este artículo son para la versión 3.x.

<sup>*</sup> Hay disponible una versión experimental de Azure Functions que le permite usar las funciones de C# en la versión preliminar de .NET 6.0. Para más información, consulte la página de la [primera versión preliminar de Azure Functions v4](https://aka.ms/functions-dotnet6earlypreview-wiki).

## <a name="prerequisites"></a>Requisitos previos

Azure Functions Core Tools actualmente depende de la [CLI de Azure](/cli/azure/install-azure-cli) o de [Azure PowerShell](/powershell/azure/install-az-ps) para la autenticación con su cuenta de Azure. Esto significa que debe instalar alguna de estas herramientas para poder [realizar la publicación en Azure](#publish) desde Azure Functions Core Tools. 

## <a name="install-the-azure-functions-core-tools"></a>Instalación de Azure Functions Core Tools

[Azure Functions Core Tools] incluye una versión del mismo tiempo de ejecución de Azure Functions que puede ejecutar en el equipo de desarrollo local. También proporciona comandos para crear funciones, conectarse a Azure e implementar proyectos de funciones.

### <a name="version-3x-and-2x"></a><a name="v2"></a>Versión 3.x y 2.x

La versión 3.x o 2.x de las herramientas usa el entorno de ejecución de Azure Functions que se basa en .NET Core. Esta versión se admite en todas las plataformas que admiten .NET Core, incluidas [Windows](?tabs=windows#v2), [macOS](?tabs=macos#v2) y [Linux](?tabs=linux#v2). 

> [!IMPORTANT]
> Puede omitir el requisito de instalar el SDK de .NET Core con los [conjuntos de extensiones].

# <a name="windows"></a>[Windows](#tab/windows)

En los pasos siguientes se utiliza Windows Installer (MSI) para instalar Core Tools v3.x. Para obtener más información sobre otros instaladores basados en paquetes, que son necesarios para instalar Core Tools V2. x, consulte el [archivo Léame de Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Descargue y ejecute el instalador de Core Tools según su versión de Windows:

    - [v3.x: Windows de 64 bits](https://go.microsoft.com/fwlink/?linkid=2135274) (recomendado. [La depuración de Visual Studio Code](functions-develop-vs-code.md#debugging-functions-locally) requiere 64 bits).
    - [v3.x: Windows de 32 bits](https://go.microsoft.com/fwlink/?linkid=2135275)

1. Si no planea usar los [conjuntos de extensión](functions-bindings-register.md#extension-bundles), instale el [SDK de .NET Core 3.x para Windows](https://dotnet.microsoft.com/download).

# <a name="macos"></a>[macOS](#tab/macos)

Los pasos siguientes utilizan Homebrew para instalar Core Tools en macOS.

1. Instale [Homebrew](https://brew.sh/), si aún no está instalado.

1. Instale el paquete de Core Tools:

    ##### <a name="v3x-recommended"></a>v3.x (recomendado)

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```
    
    ##### <a name="v2x"></a>Versión 2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. Si no planea usar los [conjuntos de extensión](functions-bindings-register.md#extension-bundles), instale el [SDK de .NET Core 3.x para macOS](https://dotnet.microsoft.com/download).

# <a name="linux"></a>[Linux](#tab/linux)

Los siguientes pasos usan [APT](https://wiki.debian.org/Apt) para instalar Core Tools en la distribución de Ubuntu/Debian Linux. Para otras distribuciones de Linux, consulte el [archivo Léame de Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Instale la clave GPG del repositorio de paquetes de Microsoft para validar la integridad del paquete:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Configure la lista de origen de APT antes de actualizar APT.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Compruebe si el archivo `/etc/apt/sources.list.d/dotnetdev.list` contiene una de las cadenas que se indican a continuación con la versión de Linux apropiada:

    | Distribución de Linux | Versión |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Inicie la actualización del origen de APT:

    ```bash
    sudo apt-get update
    ```

1. Instale el paquete de Core Tools:

    ##### <a name="v3x-recommended"></a>v3.x (recomendado)
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```
    
    ##### <a name="v2x"></a>Versión 2.x
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. Si no planea usar los [conjuntos de extensión](functions-bindings-register.md#extension-bundles), instale el [SDK de .NET Core 3.x para Linux](https://dotnet.microsoft.com/download).

---

### <a name="version-1x"></a>Versión 1.x

Si necesita instalar la versión 1.x de Core Tools, que solo se ejecuta en Windows, consulte el [repositorio de GitHub](https://github.com/Azure/azure-functions-core-tools/blob/v1.x/README.md#installing) para obtener más información.

## <a name="create-a-local-functions-project"></a>Creación de un proyecto local de Functions

Un directorio de proyecto de Functions contiene los siguientes archivos y carpetas, independientemente del lenguaje: 

| Nombre de archivo | Descripción |
| --- | --- |
| host.json | Para obtener más información, consulte la [referencia de host.json](functions-host-json.md). |
| local.settings.json | Configuración que usa Core Tools cuando se ejecuta localmente, incluida la configuración de la aplicación. Para obtener más información, consulte [Configuración local](#local-settings). |
| .gitignore | Impide que el archivo local.settings.json se publique accidentalmente en un repositorio de Git. Para obtener más información, consulte [Configuración local](#local-settings).|
| .vscode\extensions.json | Archivo de configuración que se usa al abrir la carpeta del proyecto en Visual Studio Code.  |

Para obtener más información sobre la carpeta del proyecto de Functions, consulte la [Guía para desarrolladores de Azure Functions](functions-reference.md#folder-structure).

En la ventana de terminal o desde un símbolo del sistema, ejecute el siguiente comando para crear el proyecto y el repositorio de Git local:

```
func init MyFunctionProj
```

En este ejemplo se crea un proyecto de Functions en una carpeta nueva `MyFunctionProj`. Se le pedirá que elija un lenguaje predeterminado para el proyecto. 

Tenga en cuenta lo siguiente al inicializar el proyecto:

+ Si no proporciona la opción `--worker-runtime` en el comando, se le pedirá que elija el lenguaje. Para obtener más información, consulte la [referencia de func init](functions-core-tools-reference.md#func-init).

+ Si no proporciona un nombre de proyecto, se inicializa la carpeta actual. 

+ Si tiene previsto publicar el proyecto en un contenedor de Linux personalizado, use la opción `--dockerfile` para asegurarse de que se genere un Dockerfile para el proyecto. Para obtener más información, consulte [Creación de una función en Linux con una imagen personalizada](functions-create-function-linux-custom-image.md). 

Es posible que deba tener en cuenta otros aspectos con algunos lenguajes:

# <a name="c"></a>[C\#](#tab/csharp)

+ De manera predeterminada, con la versión 2.x y las posteriores de Core Tools se crean proyectos de aplicación de funciones para el entorno de ejecución de .NET como [proyectos de clase de C#](functions-dotnet-class-library.md) (.csproj). La versión 3.x también admite la creación de funciones que [se ejecuten en .NET 5.0 en un proceso aislado](dotnet-isolated-process-guide.md). Estos proyectos de C#, que se pueden usar con Visual Studio o con Visual Studio Code, se compilan durante la depuración y al publicar en Azure. 

+ Use el parámetro `--csx` si desea trabajar localmente con archivos de script de C# (.csx). Estos son los mismos archivos que se obtienen al crear funciones en Azure Portal y cuando se usa la versión 1.x de Core Tools. Para obtener más información, consulte la [referencia de func init](functions-core-tools-reference.md#func-init).

# <a name="java"></a>[Java](#tab/java)

+ Java utiliza un arquetipo de Maven para crear el proyecto de funciones local, junto con la primera función desencadenada por HTTP. En lugar de usar `func init` y `func new`, siga los pasos que se describen en la [guía de inicio rápido de la línea de comandos](./create-first-function-cli-java.md).  

# <a name="javascript"></a>[JavaScript](#tab/node)

+ Para usar un valor `--worker-runtime` de `node`, especifique el objeto `--language` como `javascript`. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

No hay que tener en cuenta aspectos adicionales con PowerShell.

# <a name="python"></a>[Python](#tab/python)

+ Debe ejecutar todos los comandos, incluido `func init`, desde dentro de un entorno virtual. Para obtener más información, consulte [Creación y activación de un entorno virtual](create-first-function-cli-python.md#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

+ Para usar un valor `--worker-runtime` de `node`, especifique el objeto `--language` como `javascript`.

+ Consulte la [sección sobre TypeScript de la referencia para desarrolladores de JavaScript](functions-reference-node.md#typescript) para `func init` conocer comportamientos específicos de TypeScript. 

--- 

## <a name="register-extensions"></a>Registro de las extensiones

A partir de la versión 2.x del entorno de ejecución, los enlaces de Functions se implementan como paquetes (NuGet) de extensión de .NET. En el caso de proyectos de C# compilados, basta con que haga referencia a los paquetes de extensión NuGet de los desencadenadores y enlaces específicos que use. Los enlaces HTTP y los desencadenadores de temporizador no requieren extensiones. 

Con el fin de mejorar la experiencia de desarrollo para proyectos que no son de C#, Functions le permite hacer referencia a un conjunto de extensiones con versión en su archivo de proyecto host.json. Los [conjuntos de extensiones](functions-bindings-register.md#extension-bundles) ponen todas las extensiones a disposición de su aplicación y eliminan la posibilidad de que haya problemas de compatibilidad de paquetes entre extensiones. Los conjuntos de extensiones también eliminan el requisito de instalar el SDK de .NET Core 2.x y tener que gestionar el archivo extensions.csproj.

Los conjuntos de extensiones son el enfoque recomendado para los proyectos de Functions que no se han compilado con C#. Para estos proyectos, el valor de conjunto de extensiones se genera en el archivo _host.json_ durante la inicialización. Si este enfoque satisface sus necesidades, puede omitir toda esta sección.  

### <a name="use-extension-bundles"></a>Uso de conjuntos de extensiones

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

 Si el lenguaje lo admite, los conjuntos de extensiones ya deberían estar habilitados después de llamar a `func init`. Debe agregar los conjuntos de extensiones a host.json antes de agregar enlaces al archivo functions.json. Para obtener más información, consulte [Register Azure Functions binding extensions](functions-bindings-register.md#extension-bundles) (Registrar las extensiones de enlace de Azure Functions). 

### <a name="explicitly-install-extensions"></a>Instalación explícita de extensiones

En un proyecto que no sea de .NET, puede haber casos en los que no se puedan usar conjuntos de extensiones, por ejemplo, cuando necesite tener como destino una versión específica de una extensión que no esté en el conjunto. En estos casos excepcionales, puede usar Core Tools para instalar localmente los paquetes de extensión específicos que necesita el proyecto. Para obtener más información, consulte [Instalación explícita de extensiones](functions-bindings-register.md#explicitly-install-extensions).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

De manera predeterminada, estas opciones de configuración no se migran automáticamente cuando el proyecto se publica en Azure. Use la [opción `--publish-local-settings`][func azure functionapp publish] cuando publique para asegurarse de que la configuración se agregue a la aplicación de funciones en Azure. Los valores de la sección `ConnectionStrings` no se publican nunca.

Esta configuración de la aplicación de función también se puede leer en el código como variables de entorno. Para más información, consulte la sección Variables de entorno de estos temas de referencia específicos del lenguaje:

* [C# precompilado](functions-dotnet-class-library.md#environment-variables)
* [Script de C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

Cuando no se establece ninguna cadena de conexión de almacenamiento válida para [`AzureWebJobsStorage`] y no se usa el emulador, se muestra el siguiente mensaje de error:

> Missing value for AzureWebJobsStorage in local.settings.json. This is required for all triggers other than HTTP. You can run 'func azure functionapp fetch-app-settings \<functionAppName\>' or specify a connection string in local.settings.json (Puede ejecutar "func azure functionapp fetch-app-settings" o especificar una cadena de conexión en local settings.json).

### <a name="get-your-storage-connection-strings"></a>Obtención de las cadenas de conexión de almacenamiento

Aunque use el Emulador de Microsoft Azure Storage para tareas de desarrollo, le recomendamos ejecutarlo localmente con una conexión de almacenamiento real. Suponiendo que ya [ha creado una cuenta de almacenamiento](../storage/common/storage-account-create.md), puede obtener una cadena de conexión de almacenamiento válida de una de las maneras siguientes:

# <a name="portal"></a>[Portal](#tab/portal)

1. En [Azure Portal], busque y seleccione **Cuentas de almacenamiento**. 

    ![Selección de cuentas de almacenamiento desde Azure Portal](./media/functions-run-local/select-storage-accounts.png)
  
1.  Seleccione la cuenta de almacenamiento, elija **Claves de acceso** en **Configuración** y, a continuación, copie uno de los valores de **Cadena de conexión**.

    ![Copia de una cadena de conexión desde Azure Portal](./media/functions-run-local/copy-storage-connection-portal.png)

# <a name="core-tools"></a>[Core Tools](#tab/azurecli)

Desde el directorio raíz del proyecto, use uno de los siguientes comandos para descargar la cadena de conexión de Azure:

  + Descargue toda la configuración de una aplicación de función existente:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + Obtenga la cadena de conexión de una cuenta de almacenamiento concreta:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Si aún no ha iniciado sesión en Azure, se le pedirá que lo haga. Estos comandos sobrescriben cualquier configuración existente en el archivo local. settings.json. Para obtener más información, consulte los comandos [`func azure functionapp fetch-app-settings`](functions-core-tools-reference.md#func-azure-functionapp-fetch-app-settings) y [`func azure storage fetch-connection-string`](functions-core-tools-reference.md#func-azure-storage-fetch-connection-string).

# <a name="storage-explorer"></a>[Explorador de Storage](#tab/storageexplorer)

1. Ejecute el [Explorador de Azure Storage](https://storageexplorer.com/). 

1. En el **Explorador**, expanda sus suscripción y, a continuación, **Cuentas de almacenamiento**.

1. Seleccione su cuenta de almacenamiento y copie la cadena de conexión principal o secundaria.

    ![Copia de la cadena de conexión desde el Explorador de Azure Storage](./media/functions-run-local/storage-explorer.png)

---

## <a name="create-a-function"></a><a name="create-func"></a>Creación de una función

Para crear una función en un proyecto existente, ejecute el siguiente comando:

```
func new
```

En la versión 3.x/2.x, cuando ejecute `func new`, se le pedirá que elija una plantilla en el lenguaje predeterminado de su aplicación de funciones. A continuación, se le pedirá que elija un nombre para la función. En la versión 1.x, también se le pedirá que elija el lenguaje. 

También puede especificar el nombre y la plantilla de la función en el comando `func new`. En el siguiente ejemplo se usa la opción `--template` para crear un desencadenador HTTP denominado `MyHttpTrigger`:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

En este ejemplo se crea un desencadenador de Queue Storage denominado `MyQueueTrigger`:

```
func new --template "Queue Trigger" --name MyQueueTrigger
```

Para obtener más información, consulte el [comando `func new`](functions-core-tools-reference.md#func-new).

## <a name="run-functions-locally"></a><a name="start"></a>Ejecución local de funciones

Para ejecutar un proyecto de Functions, ejecute el host de Functions desde el directorio raíz del proyecto. El host habilita desencadenadores para todas las funciones del proyecto. El [comando `start`](functions-core-tools-reference.md#func-start) varía en función del lenguaje del proyecto.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Este comando debe [ejecutarse en un entorno virtual](./create-first-function-cli-python.md).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> En la versión 1.x del runtime de Functions, se requiere `func host start`. Para obtener más información, consulte [Referencia de Azure Functions Core Tools](functions-core-tools-reference.md?tabs=v1#func-start). 

Cuando se inicia el host de Functions, devuelve la dirección URL de las funciones desencadenadas por HTTP, como en el siguiente ejemplo:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Cuando se ejecuta localmente, no se aplica la autorización para puntos de conexión HTTP. Esto significa que todas las solicitudes HTTP locales se tratan como `authLevel = "anonymous"`. Para obtener más información, consulte el artículo sobre [enlaces HTTP](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Paso de datos de prueba a una función

Para probar sus funciones localmente, [inicie el host de Functions](#start) y llame a puntos de conexión del servidor local mediante solicitudes HTTP. El punto de conexión al que llama depende del tipo de función.

>[!NOTE]
> En los ejemplos de este tema se usa la herramienta cURL para enviar solicitudes HTTP desde el terminal o un símbolo del sistema. Puede usar una herramienta de su elección para enviar solicitudes HTTP al servidor local. La herramienta cURL está disponible de forma predeterminada en los sistemas basados en Linux y en la compilación 17063 de Windows 10, y en las posteriores. En las versiones anteriores de Windows, primero debe descargar e instalar la [herramienta cURL](https://curl.haxx.se/).

Para obtener información más general sobre cómo probar funciones, consulte [Estrategias para probar el código en Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funciones desencadenadas por HTTP y webhook

Llama al siguiente punto de conexión para ejecutar de forma local funciones desencadenadas por HTTP y webhook:

```
http://localhost:{port}/api/{function_name}
```

Asegúrese de usar el mismo nombre del servidor y puerto en el que escucha el host de Functions. Puede ver esto en la salida generada al iniciar el host de Functions. Puede llamar a esta dirección URL mediante cualquier método HTTP admitido por el desencadenador.

El siguiente comando cURL desencadena la función de inicio rápido `MyHttpTrigger` desde una solicitud GET con el parámetro _name_ transferido en la cadena de consulta.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

En el siguiente ejemplo está la misma función a la que se llama desde una solicitud POST que transfiere _name_ en el cuerpo de la solicitud:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Puede realizar solicitudes GET desde un explorador que transfiere datos en la cadena de consulta. Para todos los demás métodos HTTP, debe usar cURL, Fiddler, Postman o una herramienta de pruebas HTTP similar que admita solicitudes POST.

#### <a name="non-http-triggered-functions"></a>Funciones no desencadenadas por HTTP

Para todas las funciones que no sean desencadenadores HTTP y Event Grid, puede probar sus funciones localmente mediante REST llamando a un punto de conexión especial denominado _punto de conexión de administración_. Al llamar a este punto de conexión con una solicitud HTTP POST en el servidor local se desencadena esta función. 

Para probar las funciones de Event Grid desencadenadas localmente, consulte [Pruebas locales con la aplicación web de visor](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Opcionalmente, puede transferir los datos de prueba a la ejecución en el cuerpo de la solicitud POST. Esta funcionalidad es similar a la pestaña **Prueba** de Azure Portal.

Se llama al siguiente punto de conexión de administrador para desencadenar funciones ajenas a HTTP:

```
http://localhost:{port}/admin/functions/{function_name}
```

Para transferir datos de prueba al punto de conexión de administrador de una función, debe proporcionar los datos en el cuerpo de un mensaje de solicitud POST. Es necesario que el cuerpo del mensaje tenga el siguiente formato JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

El valor `<trigger_input>` contiene datos en un formato esperado por la función. El siguiente ejemplo de cURL es una solicitud POST dirigida a una función `QueueTriggerJS`. En este caso, la entrada es una cadena que equivale al mensaje que se espera encontrar en la cola.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

Al llamar a un punto de conexión de administración en su aplicación de funciones de Azure, debe proporcionar una clave de acceso. Para obtener más información, consulte [Claves de acceso de función](functions-bindings-http-webhook-trigger.md#authorization-keys).

## <a name="publish-to-azure"></a><a name="publish"></a>Publicación en Azure

Azure Functions Core Tools admite tres tipos de implementación:

| Tipo de implementación | Get-Help | Descripción |
| ----- | ----- | ----- |
| Archivos de proyecto | [`func azure functionapp publish`](functions-core-tools-reference.md#func-azure-functionapp-publish) | Implementa los archivos de proyecto de función directamente en su aplicación de funciones usando la [implementación mediante ZIP](functions-deployment-technologies.md#zip-deploy). |
| Contenedor personalizado | `func deploy` | Implementa SU proyecto en una aplicación de funciones de Linux como contenedor de Docker personalizado.  |
| Clúster de Kubernetes | `func kubernetes deploy` | Implementa la aplicación de funciones de Linux como contenedor de Docker de cliente en un clúster de Kubernetes. | 

### <a name="before-you-publish"></a>Antes de publicar 

>[!IMPORTANT]
>Debe tener la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/install-az-ps) instalados localmente para poder realizar la publicación en Azure desde Core Tools.  

Una carpeta de proyecto puede contener archivos y directorios específicos del idioma que no deben publicarse. Los elementos excluidos se enumeran en un archivo .funcignore en la carpeta raíz del proyecto.  

Tiene que tener [creada una aplicación de funciones en su suscripción de Azure](functions-cli-samples.md#create) para implementar su código. Se deben compilar los proyectos que lo requieran para poder implementar los archivos binarios.

Para obtener información sobre cómo crear una aplicación de función desde el símbolo del sistema o la ventana de Terminal mediante la CLI de Azure o Azure PowerShell, vea [Creación de una aplicación de función para la ejecución de código sin servidor](./scripts/functions-cli-create-serverless.md). 

>[!IMPORTANT]
> Cuando se crea una aplicación de función en Azure Portal, se usa la versión 3.x del entorno de ejecución de Functions de forma predeterminada. Para hacer que la aplicación de función utilice la versión 1.x del entorno de ejecución, siga las instrucciones de [Ejecución en la versión 1.x](functions-versions.md#creating-1x-apps).
> No se puede cambiar la versión del entorno de ejecución de una aplicación de función que tiene funciones existentes.


### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Implementación de los archivos de proyecto

Para publicar su código local en una aplicación de funciones en Azure, use el comando `publish`:

```
func azure functionapp publish <FunctionAppName>
```

Tenga en cuenta los siguientes aspectos con relación a este tipo de implementación:

+ Al publicar, se sobrescriben los archivos existentes en la aplicación de funciones.

+ Use la [opción `--publish-local-settings`][func azure functionapp publish] para crear automáticamente la configuración de aplicación en su aplicación de funciones a partir de los valores del archivo local.settings.json.  

+ En proyectos compilados se lleva a cabo una [compilación remota](functions-deployment-technologies.md#remote-build). Esta acción se puede controlar usando la [opción `--no-build`][func azure functionapp publish].  

+ Su proyecto se implementa de tal forma que [se ejecute desde el paquete de implementación](run-functions-from-deployment-package.md). Para deshabilitar este modo de implementación recomendado, use la [opción `--nozip`][func azure functionapp publish].

+ Java utiliza Maven para publicar el proyecto local en Azure. Para publicar proyectos en Azure, use en su lugar el siguiente comando: `mvn azure-functions:deploy`. Durante la implementación inicial se crean recursos de Azure.

+ Obtendrá un error si intenta publicarla en un `<FunctionAppName>` que no exista en su suscripción. 

### <a name="kubernetes-cluster"></a>Clúster de Kubernetes

Functions también le permite definir su proyecto de la plataforma para que se ejecute en un contenedor de Docker. Use la [opción `--docker`][func init] de `func init` con el fin de generar un Dockerfile para su lenguaje específico. Este archivo se usa posteriormente al crear un contenedor para implementar. 

Core Tools se puede usar para implementar el proyecto como una imagen de contenedor personalizada en un clúster de Kubernetes. El comando necesario depende del tipo de escalador usado en el clúster.  

El siguiente comando usa el Dockerfile para generar un contenedor e implementarlo en un clúster de Kubernetes. 

# <a name="keda"></a>[KEDA](#tab/keda)

```command
func kubernetes deploy --name <DEPLOYMENT_NAME> --registry <REGISTRY_USERNAME> 
```

Para obtener más información, consulte [Implementación de una aplicación de funciones en Kubernetes](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes). 

# <a name="defaultknative"></a>[Predeterminado/KNative](#tab/default)

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <REGISTRY_USERNAME> 
```

En el ejemplo anterior, reemplace `<FUNCTION_APP>` por el nombre de la aplicación de funciones de Azure y `<REGISTRY_USERNAME>` por el nombre de su cuenta de registro, por ejemplo, su nombre de usuario de Docker. El contenedor se compila localmente y se inserta en su cuenta de registro de Docker con un nombre de imagen basado en `<FUNCTION_APP>`. Debe tener instaladas las herramientas de la línea de comandos de Docker.

Para obtener más información, consulte el [comando `func deploy`](functions-core-tools-reference.md#func-deploy).

---

Para obtener información sobre cómo publicar un contenedor personalizado en Azure sin Kubernetes, consulte [Creación de una función en Linux con un contenedor personalizado](functions-create-function-linux-custom-image.md).

## <a name="monitoring-functions"></a>Supervisión de funciones

La forma recomendada de supervisar la ejecución de sus funciones, es usar la integración con Azure Application Insights. También puede transmitir los registros de ejecución al equipo local. Para más información, consulte [Supervisión de Azure Functions](functions-monitoring.md).

### <a name="application-insights-integration"></a>Integración de Application Insights

Al crear la aplicación de funciones en Azure, la integración de Application Insights debe estar habilitada. Si, por alguna razón, la aplicación de funciones no está conectada a una instancia de Application Insights, es fácil llevar a cabo esta integración en Azure Portal. Para más información, consulte [Habilitación de la integración de Application Insights](configure-monitoring.md#enable-application-insights-integration).

### <a name="enable-streaming-logs"></a>Habilitación de los registros de streaming

Puede ver una secuencia de archivos de registro que generan las funciones en una sesión de línea de comandos en el equipo local. 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Este tipo de registros de streaming requiere que se habilite la integración de Application Insights para la aplicación de funciones.   


## <a name="next-steps"></a>Pasos siguientes

Aprenda a desarrollar, probar y publicar funciones de Azure Functions mediante el [módulo de aprendizaje de Microsoft](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) de Azure Functions Core Tools. Azure Functions Core Tools es de [código abierto y se hospeda en GitHub](https://github.com/azure/azure-functions-cli).  
Para notificar un error o realizar una solicitud de característica, [abra un problema de GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[conjuntos de extensiones]: functions-bindings-register.md#extension-bundles
[func azure functionapp publish]: functions-core-tools-reference.md?tabs=v2#func-azure-functionapp-publish
[func init]: functions-core-tools-reference.md?tabs=v2#func-init
