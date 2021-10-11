---
title: 'Creación de una función de C# desde la línea de comandos: Azure Functions'
description: Aprenda a crear una función de C# desde la línea de comandos y, luego, a publicar el proyecto local en el hospedaje sin servidor en Azure Functions.
ms.date: 09/14/2021
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
zone_pivot_groups: runtime-version-programming-functions
ms.openlocfilehash: 5ae176c8f9ebe77a40619a65464594236b94671b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559889"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Inicio rápido: Creación de una función de C# en Azure desde la línea de comandos

[!INCLUDE [functions-runtime-version-dotnet](../../includes/functions-runtime-version-dotnet.md)]

En este artículo, se usan herramientas de la línea de comandos para crear una función de C# que responda a solicitudes HTTP. Después de probar el código localmente, se implementa en el entorno sin servidor de Azure Functions.

[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]

Este inicio rápido supone un pequeño costo en su cuenta de Azure.

También hay una [versión basada en Visual Studio Code](create-first-function-vs-code-csharp.md) de este artículo.

## <a name="configure-your-local-environment"></a>Configuración del entorno local

Antes de empezar, debe disponer de lo siguiente:

::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [functions-cli-dotnet-prerequisites](../../includes/functions-cli-dotnet-prerequisites.md)]
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
# <a name="in-process"></a>[En proceso](#tab/in-process)    

+ [SDK de .NET 6.0](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](./functions-run-local.md#v2), versión 4.x.

+ Una de las siguientes herramientas para crear recursos de Azure:

    + [CLI de Azure](/cli/azure/install-azure-cli), versión 2.4 o posterior.

    + [Azure PowerShell](/powershell/azure/install-az-ps), versión 5.0 o posterior.

# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)

+ [SDK de .NET 6.0](https://dotnet.microsoft.com/download/dotnet/6.0)

+ [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download). Requerido por el proceso de compilación.

+ [Azure Functions Core Tools](./functions-run-local.md#v2), versión 4.x.

+ Una de las siguientes herramientas para crear recursos de Azure:

    + [CLI de Azure](/cli/azure/install-azure-cli), versión 2.4 o posterior.

    + [Azure PowerShell](/powershell/azure/install-az-ps), versión 5.0 o posterior.
---
::: zone-end

También necesita una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

### <a name="prerequisite-check"></a>Comprobación de requisitos previos

Compruebe los requisitos previos, que dependen de si usa la CLI de Azure o Azure PowerShell para crear recursos de Azure:

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

+ En una ventana de terminal o de comandos, ejecute `func --version` para comprobar que la versión de Azure Functions Core Tools es la 3.x.

+ Ejecute `dotnet --list-sdks` para comprobar que estén instaladas las versiones necesarias.

+ Ejecute `az --version` para comprobar que la versión de la CLI de Azure es la 2.4 o posterior.

+ Ejecute `az login` para iniciar sesión en Azure y comprobar una suscripción activa.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ En una ventana de terminal o de comandos, ejecute `func --version` para comprobar que la versión de Azure Functions Core Tools es la 3.x.

+ Ejecute `dotnet --list-sdks` para comprobar que estén instaladas las versiones necesarias.

+ Ejecute `(Get-Module -ListAvailable Az).Version` y compruebe que es la versión 5.0 o posterior. 

+ Ejecute `Connect-AzAccount` para iniciar sesión en Azure y comprobar una suscripción activa.

---

## <a name="create-a-local-function-project"></a>Creación de un proyecto de función local

En Azure Functions, un proyecto de función es un contenedor para una o varias funciones individuales que responden a un desencadenador específico. Todas las funciones de un proyecto comparten las mismas configuraciones locales y de hospedaje. En esta sección, se crea un proyecto de función que contiene una sola función.

1. Ejecute el comando `func init`, de la manera siguiente, para crear un proyecto de funciones en una carpeta llamada *LocalFunctionProj* con el entorno de ejecución especificado:  

    # <a name="in-process"></a>[En proceso](#tab/in-process) 

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

    # <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)

    ```csharp
    func init LocalFunctionProj --dotnet-isolated
    ```
    ---

1. Vaya a la carpeta del proyecto:

    ```console
    cd LocalFunctionProj
    ```

    Esta carpeta contiene varios archivos del proyecto, incluidos los archivos de configuración [local.settings.json](functions-develop-local.md#local-settings-file) y [host.json](functions-host-json.md). Como *local.settings.json* puede contener secretos descargados de Azure, el archivo se excluye del control de código fuente de forma predeterminada en el archivo *.gitignore*.

1. Agregue una función al proyecto mediante el comando siguiente, donde el argumento `--name` es el nombre único de la función (HttpExample) y el argumento `--template` especifica el desencadenador de esta (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` crea un archivo de código HttpExample.cs.

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examen del contenido del archivo

Si lo desea, puede ir a [Ejecución local de la función](#run-the-function-locally) y examine el contenido del archivo posteriormente.

#### <a name="httpexamplecs"></a>HttpExample.cs

El código de función generado a partir de la plantilla depende del tipo de proyecto de C# compilado.  

# <a name="in-process"></a>[En proceso](#tab/in-process) 

*HttpExample.cs* contiene un método `Run` que recibe datos de solicitud en la variable `req` de tipo [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) que está decorado con **HttpTriggerAttribute**, que define el comportamiento del desencadenador.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

El objeto de devolución es un objeto [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) que devuelve un mensaje de respuesta [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) o [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). 

# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)

*HttpExample.cs* contiene un método `Run` que recibe los datos de la solicitud en la variable `req` y es un objeto [HttpRequest](/dotnet/api/microsoft.azure.functions.worker.http.httprequestdata) que está decorado con **HttpTriggerAttribute**, que define el comportamiento del desencadenador. Debido al modelo de proceso aislado, `HttpRequestData` es una representación del objeto `HttpRequest` real y no del propio objeto de la solicitud. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs":::

El objeto devuelto es un objeto [HttpResponseData](/dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata) que contiene los datos que se devuelven a la respuesta HTTP. 

---

Para más información, vea [Enlaces y desencadenadores HTTP de Azure Functions](./functions-bindings-http-webhook.md?tabs=csharp).

## <a name="run-the-function-locally"></a>Ejecución local de la función

1. Para ejecutar una función, inicie el host en tiempo de ejecución local de Azure Functions desde la carpeta *LocalFunctionProj*:

    ```
    func start
    ```

    Hacia el final de la salida, deberían aparecer las líneas siguientes: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Si HttpExample no aparece como se ha mostrado arriba, es probable que haya iniciado el host desde fuera de la carpeta raíz del proyecto. En ese caso, use **Ctrl**+**C** para detener el host, vaya a la carpeta raíz del proyecto y vuelva a ejecutar el comando anterior.

1. Copie la dirección URL de la función `HttpExample` de esta salida en un explorador:

    # <a name="in-process"></a>[En proceso](#tab/in-process)
    
     En la dirección URL de la función, anexe la cadena de consulta `?name=<YOUR_NAME>`, lo que hace que la dirección URL completa sea `http://localhost:7071/api/HttpExample?name=Functions`. El explorador debe mostrar un mensaje de respuesta que devuelve el valor de la cadena de consulta. El terminal en el que inició el proyecto también muestra la salida del registro cuando realiza solicitudes.

    # <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)

    Vaya a la dirección URL de la función y debería recibir el mensaje _Bienvenido a Azure Functions_.

    ---

1. Cuando termine, presione **Ctrl**+**C** y seleccione `y` para detener el host de Functions.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Cree la aplicación de funciones en Azure:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli/in-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    El comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) crea la aplicación de funciones en Azure. 

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli/isolated-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet-isolated --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ``` 
    
    El comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) crea la aplicación de funciones en Azure. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/in-process)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location '<REGION>'
    ```

    El cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) crea la aplicación de funciones en Azure. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/isolated-process)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet-isolated -FunctionsVersion 3 -Location '<REGION>'
    ```

    El cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) crea la aplicación de funciones en Azure. 

    ---

    ::: zone pivot="programming-runtime-functions-v4"
    > [!NOTE]
    > Este comando crea una aplicación de funciones con la versión 3.x del entorno de ejecución de Azure Functions. El comando `func azure functionapp publish` que ejecutará más adelante actualiza la aplicación a la versión 4.x.
    ::: zone-end
    
    En el ejemplo anterior, reemplace `<STORAGE_NAME>` por el nombre de la cuenta que usó en el paso anterior y reemplace `<APP_NAME>` por un nombre único global que le resulte adecuado. `<APP_NAME>` también es el dominio DNS predeterminado de la aplicación de función. 
    
    Este comando crea una aplicación de funciones que se ejecuta en el entorno de ejecución del lenguaje especificado en el [plan de consumo de Azure Functions](consumption-plan.md), que es gratuito para la cantidad de uso que se realiza aquí. El comando también aprovisiona una instancia asociada de Azure Application Insights en el mismo grupo de recursos con la que puede supervisar la aplicación de funciones y ver registros. Para más información, consulte [Supervisión de Azure Functions](functions-monitoring.md). La instancia no incurrirá en ningún costo hasta que se active.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

## <a name="invoke-the-function-on-azure"></a>Invocación de la función en Azure

Como la función usa un desencadenador HTTP y admite solicitudes GET, realice una solicitud HTTP a su dirección URL para invocarla. Es más fácil hacerlo en un explorador.  

# <a name="in-process"></a>[En proceso](#tab/in-process) 

Copie la **dirección URL de invocación** completa que se muestra en la salida del comando de publicación en una barra de direcciones del explorador, y anexe el parámetro de consulta `?name=Functions`. Cuando vaya a esta dirección URL, el explorador debe mostrar una salida similar a cuando ejecutó la función localmente.

# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)

Copie el valor completo de la **dirección URL de invocación** que se muestra en la salida del comando de publicación en una barra de direcciones del explorador. Cuando vaya a esta dirección URL, el explorador debe mostrar una salida similar a cuando ejecutó la función localmente.

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Pasos siguientes

# <a name="in-process"></a>[En proceso](#tab/in-process) 

> [!div class="nextstepaction"]
> [Conexión a Azure Queue Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=in-process)

# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)

> [!div class="nextstepaction"]
> [Conexión a Azure Queue Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=isolated-process)

---