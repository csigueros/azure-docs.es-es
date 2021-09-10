---
title: Creación de funciones de Azure en Linux mediante una imagen personalizada
description: Aprenda a crear funciones de Azure que se ejecutan en una imagen de Linux personalizada.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: a6e3ad07f9ba46bd15fe662f370ae2ffc3deb4a8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830561"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Creación de una función en Linux con un contenedor personalizado

En este tutorial, va a crear e implementar código en Azure Functions como un contenedor de Docker personalizado mediante una imagen base de Linux. Normalmente usa una imagen personalizada cuando las funciones necesitan una versión de lenguaje determinada o requieren una dependencia o configuración específica que no se proporciona en la imagen integrada.

::: zone pivot="programming-language-other"
Azure Functions admite cualquier lenguaje o entorno de ejecución que use [controladores personalizados](functions-custom-handlers.md). En algunos lenguajes, como el lenguaje de programación R que se usa en este tutorial, se deben instalar el entorno de ejecución o las bibliotecas adicionales como dependencias que requieren un contenedor personalizado.
::: zone-end

Para implementar el código de función de un contenedor de Linux personalizado, se necesita un [plan premium](functions-premium-plan.md) o el hospedaje de un [plan dedicado (App Service)](dedicated-plan.md). La realización de este tutorial generará un pequeño costo en la cuenta de Azure, que puede minimizar con la [limpieza de los recursos](#clean-up-resources) cuando haya acabado.

También puede usar un contenedor de Azure App Service predeterminado como se indica en [Creación de su primera función hospedada en Linux](./create-first-function-cli-csharp.md?pivots=programming-language-python). Se pueden encontrar imágenes base admitidas para Azure Functions en el [repositorio de imágenes base de Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

En este tutorial, aprenderá a:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Crear una aplicación de funciones y un archivo Dockerfile mediante Azure Functions Core Tools.
> * Crear una imagen personalizada con Docker
> * Publicar una imagen personalizada en un registro de contenedor
> * Crear recursos auxiliares en Azure para la aplicación de funciones
> * Implementar una aplicación de función desde Docker Hub
> * Agregar la configuración de aplicación a la aplicación de función
> * Habilite la implementación continua.
> * Habilitar las conexiones SSH al contenedor.
> * Agregar un enlace de salida de Queue Storage. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Crear una aplicación de funciones y un archivo Dockerfile mediante Azure Functions Core Tools.
> * Crear una imagen personalizada con Docker
> * Publicar una imagen personalizada en un registro de contenedor
> * Crear recursos auxiliares en Azure para la aplicación de funciones
> * Implementar una aplicación de función desde Docker Hub
> * Agregar la configuración de aplicación a la aplicación de función
> * Habilite la implementación continua.
> * Habilitar las conexiones SSH al contenedor.
::: zone-end

Puede seguir este tutorial en cualquier equipo que ejecute Windows, macOS o Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Un [identificador de Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Creación y prueba de un proyecto local de Functions

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
En un terminal o símbolo del sistema, ejecute el siguiente comando para el lenguaje elegido a fin de crear un proyecto de aplicación de funciones en la carpeta actual.  
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[En proceso](#tab/in-process)
```console
func init --worker-runtime dotnet --docker
```

# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)
```console
func init --worker-runtime dotnet-isolated --docker
```
---
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
En una carpeta vacía, ejecute el comando siguiente para generar el proyecto de Functions desde un [arquetipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

El parámetro `-DjavaVersion` indica al entorno de ejecución de Functions qué versión de Java debe usar. Use `-DjavaVersion=11` si quiere que las funciones se ejecuten en Java 11. Cuando no se especifica `-DjavaVersion`, el valor predeterminado de Maven es Java 8. Para obtener más información, consulte [Versiones de Java](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> Para llevar a cabo los pasos de este artículo, la variable de entorno `JAVA_HOME` se debe establecer en la ubicación de instalación de la versión correcta del JDK.

Maven le pide los valores necesarios para finalizar la generación del proyecto en la implementación.   
Indique los siguientes valores cuando se le solicite:

| Prompt | Value | Descripción |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Un valor que identifica de forma única su proyecto entre todos los demás y que sigue las [reglas de nomenclatura de paquetes](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) de Java. |
| **artifactId** | `fabrikam-functions` | Un valor que es el nombre del archivo jar, sin un número de versión. |
| **version** | `1.0-SNAPSHOT` | Elija el valor predeterminado. |
| **package** | `com.fabrikam.functions` | Un valor que es el paquete de Java para el código de función generado. Use el valor predeterminado. |

Escriba `Y` o presione Entrar para confirmar.

Maven crea los archivos del proyecto en una carpeta nueva llamada _artifactId_ que, en este ejemplo, es `fabrikam-functions`. 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init --worker-runtime custom --docker
```
::: zone-end

La opción `--docker` genera un archivo `Dockerfile` para el proyecto que permite definir un contenedor personalizado adecuado para su uso con Azure Functions y el entorno de ejecución seleccionado.

::: zone pivot="programming-language-java"  
Vaya a la carpeta del proyecto:

```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[En proceso](#tab/in-process)
No se necesita ningún cambio en el archivo Dockerfile.
# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)
Abra el archivo y agregue las líneas siguientes después de la primera instrucción `FROM`, si aún no está presente:

```docker
# Build requires 3.1 SDK
COPY --from=mcr.microsoft.com/dotnet/core/sdk:3.1 /usr/share/dotnet /usr/share/dotnet
```
---
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Agregue una función al proyecto mediante el comando siguiente, donde el argumento `--name` es el nombre único de la función y el argumento `--template` especifica el desencadenador de esta. `func new` crea un archivo de código de C# en el proyecto.

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end

::: zone pivot="programming-language-other,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Agregue una función al proyecto mediante el comando siguiente, donde el argumento `--name` es el nombre único de la función y el argumento `--template` especifica el desencadenador de esta. `func new` crea una subcarpeta que coincide con el nombre de función que contiene un archivo de configuración denominado *function.json*.

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end  
::: zone pivot="programming-language-other" 
En un editor de texto, cree un archivo en la carpeta de proyecto denominada *handler.R*. Agregue lo siguiente como contenido del archivo.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

En *host.json*, modifique la sección `customHandler` para configurar el comando de inicio del controlador personalizado.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Para probar la función localmente, inicie el host en tiempo de ejecución de Azure Functions local en la carpeta raíz del proyecto: 
::: zone pivot="programming-language-csharp"  
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

Una vez que vea el punto de conexión `HttpExample` aparecer en la salida, vaya a `http://localhost:7071/api/HttpExample?name=Functions`. El explorador debe mostrar un mensaje "hello" que devuelve `Functions`, el valor proporcionado al parámetro de consulta `name`.

Use **Ctrl**-**C** para detener el host.

## <a name="build-the-container-image-and-test-locally"></a>Creación de la imagen de contenedor y prueba local

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Opcional) Examine el archivo *Dockerfile* en la carpeta raíz del proyecto. Este archivo describe el entorno necesario para ejecutar la aplicación de funciones en Linux.  La lista completa de imágenes base admitidas para Azure Functions se puede encontrar en la [página de imágenes base de Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Examine el archivo *Dockerfile* en la carpeta raíz del proyecto. Este archivo describe el entorno necesario para ejecutar la aplicación de funciones en Linux. Las aplicaciones de controlador personalizado usan la imagen `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` como base.

Modifique el archivo *Dockerfile* para instalar R. Reemplace el contenido de *Dockerfile* por el siguiente.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

En la carpeta raíz del proyecto, ejecute el comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) y especifique un nombre, `azurefunctionsimage`, y una etiqueta, `v1.0.0`. Reemplace `<DOCKER_ID>` por el identificador de su cuenta de Docker Hub. Este comando compila la imagen de Docker del contenedor.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Cuando el comando se complete, podrá ejecutar el nuevo contenedor de forma local.
    
Para probar la compilación, ejecute la imagen en un contenedor local con el comando [docker run](https://docs.docker.com/engine/reference/commandline/run/). Para ello, reemplace de nuevo `<DOCKER_ID` por el identificador de Docker y agregue el argumento de puertos, `-p 8080:80`:

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp"
# <a name="in-process"></a>[En proceso](#tab/in-process)
Una vez que la imagen se inicie en el contenedor local, vaya a `http://localhost:8080/api/HttpExample?name=Functions`, donde debería aparecer el mismo mensaje "hello" que antes. Dado que la función desencadenada por HTTP que creó usa la autorización anónima, puede llamar a la función que se ejecuta en el contenedor sin tener que obtener una clave de acceso. Para más información, consulte [Claves de autorización].
# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)
Una vez que la imagen se inicie en el contenedor local, vaya a `http://localhost:8080/api/HttpExample`, donde debería aparecer el mismo mensaje de saludo que antes. Dado que la función desencadenada por HTTP que creó usa la autorización anónima, puede llamar a la función que se ejecuta en el contenedor sin tener que obtener una clave de acceso. Para más información, consulte [Claves de autorización].

---
::: zone-end
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Una vez que la imagen se inicie en el contenedor local, vaya a `http://localhost:8080/api/HttpExample?name=Functions`, donde debería aparecer el mismo mensaje "hello" que antes. Dado que la función desencadenada por HTTP que creó usa la autorización anónima, puede llamar a la función que se ejecuta en el contenedor sin tener que obtener una clave de acceso. Para más información, consulte [Claves de autorización].
::: zone-end  

Después de haber comprobado la aplicación de funciones en el contenedor, detenga Docker con **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Inserción de la imagen en Docker Hub

Docker Hub es un registro de contenedor que hospeda imágenes y proporciona servicios de imágenes y contenedores. Para compartir la imagen, lo cual incluye la implementación en Azure, debe insertarla en un registro.

1. Si aún no ha iniciado sesión en Docker, hágalo con el comando [docker login](https://docs.docker.com/engine/reference/commandline/login/), reemplazando `<docker_id>` por el identificador de Docker. Este comando le solicita su nombre de usuario y contraseña. Un mensaje de inicio de sesión correcto confirmará que ha iniciado sesión.

    ```console
    docker login
    ```
    
1. Cuando haya iniciado sesión, inserte la imagen en Docker Hub mediante el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) y reemplazando de nuevo `<docker_id>` por el identificador de Docker.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Según la velocidad de la red, la inserción de la imagen la primera vez puede tardar unos minutos (la inserción de los cambios posteriores es mucho más rápida). Mientras espera, puede continuar con la sección siguiente y crear recursos de Azure en otro terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Creación de recursos auxiliares de Azure para la función

Antes de poder implementar el código de la función en Azure, debe crear tres recursos:

- Un [grupo de recursos](../azure-resource-manager/management/overview.md), que es un contenedor lógico de recursos relacionados.
- Una [cuenta de almacenamiento](../storage/common/storage-account-create.md), que se usa para mantener el estado y otra información sobre sus funciones.
- Una aplicación de funciones, que proporciona el entorno para ejecutar el código de función. Una aplicación de funciones se asigna al proyecto de funciones y le permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación y el uso compartido de recursos.

Utilice los comandos siguientes para crear los elementos. Se admiten las CLI de Azure y de PowerShell.

1. Si todavía no lo ha hecho, inicie sesión en Azure:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    El comando [az login](/cli/azure/reference-index#az_login) inicia sesión en su cuenta de Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    El cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) inicia sesión en su cuenta de Azure.

    ---

1. Cree un grupo de recursos denominado `AzureFunctionsContainers-rg` en la región elegida:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location <REGION>
    ```
 
    El comando [az group create](/cli/azure/group#az_group_create) crea un grupo de recursos. En el comando anterior, reemplace `<REGION>` por una región cercana mediante un código de región disponible devuelto por el comando [az account list-locations](/cli/azure/account#az_account_list_locations).

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsContainers-rg -Location <REGION>
    ```

    El comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) crea un grupo de recursos. Por lo general, los recursos y los grupos de recursos se crean en una región cerca de usted, y se utiliza alguna de las regiones devueltas por el cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

1. Cree una cuenta de almacenamiento de uso general en su grupo de recursos y región:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```

    El comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) crea la cuenta de almacenamiento. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsContainers-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    El cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) crea la cuenta de almacenamiento.

    ---

    En el ejemplo anterior, reemplace `<STORAGE_NAME>` por un nombre que sea apropiado para usted y único en Azure Storage. Los nombres deben contener entre 3 y 24 caracteres y solo letras minúsculas. `Standard_LRS` especifica una cuenta de uso general, que es [compatible con Functions](storage-considerations.md#storage-account-requirements).
    
1. Use el comando para crear un plan Premium para Azure Functions denominado `myPremiumPlan` en el plan de tarifa **Premium elástico 1** (`--sku EP1`), en su `<REGION>`, y en un contenedor de Linux (`--is-linux`).

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location <REGION> --number-of-workers 1 --sku EP1 --is-linux
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```powershell
    New-AzFunctionAppPlan -ResourceGroupName AzureFunctionsContainers-rg -Name MyPremiumPlan -Location <REGION> -Sku EP1 -WorkerType Linux
    ```
    ---
    Aquí se usa el plan Premium, que se puede escalar según sea necesario. Para obtener más información sobre el hospedaje, consulte [Comparación de los planes de hospedaje de Azure Functions](functions-scale.md). Para calcular los costos, consulte la [página de precios de Functions](https://azure.microsoft.com/pricing/details/functions/).

    El comando también aprovisiona una instancia asociada de Azure Application Insights en el mismo grupo de recursos con la que puede supervisar la aplicación de funciones y ver registros. Para más información, consulte [Supervisión de Azure Functions](functions-monitoring.md). La instancia no incurrirá en ningún costo hasta que se active.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Creación y configuración de una aplicación de funciones en Azure con la imagen

Una aplicación de funciones en Azure administra la ejecución de las funciones en el plan de hospedaje. En esta sección, usará los recursos de Azure de la sección anterior para crear una aplicación de funciones a partir de una imagen en Docker Hub y configurarla con una cadena de conexión a Azure Storage.

1. Cree una aplicación de funciones con el siguiente comando:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    ```azurecli
    az functionapp create --name <APP_NAME> --storage-account <STORAGE_NAME> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```

    En el comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create), el parámetro *deployment-container-image-name* especifica la imagen que se usará para la aplicación de funciones. Puede usar el comando [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) para ver información sobre la imagen que se utilizó en la implementación. También puede usar el comando [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) para realizar la implementación desde una imagen diferente.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -PlanName myPremiumPlan -StorageAccount <STORAGE_NAME> -DockerImageName <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```
    ---
    
    En el ejemplo siguiente, reemplace `<STORAGE_NAME>` por el nombre que utilizó en la sección anterior para la cuenta de almacenamiento. Reemplace también `<APP_NAME>` por un nombre único global que sea adecuado y `<DOCKER_ID>` por el identificador de DockerHub.    
    
    > [!TIP]  
    > Puede usar el [valor `DisableColor`](functions-host-json.md#console) del archivo host.json para evitar que se escriban caracteres de control ANSI en los registros de contenedor. 

1. Use el comando siguiente para obtener la cadena de conexión de la cuenta de almacenamiento que ha creado:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv
    ```

    La cadena de conexión de la cuenta de almacenamiento se devuelve mediante el comando [az storage account show-connection-string](/cli/azure/storage/account). 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    $storage_name = "glengagtestdockerstorage"
    $key = (Get-AzStorageAccountKey -ResourceGroupName AzureFunctionsContainers-rg -Name $storage_name)[0].Value
    $string = "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=" + $storage_name + ";AccountKey=" + $key
    Write-Output($string) 
    ```
    La clave devuelta por el cmdlet [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) se usa para construir la cadena de conexión de la cuenta de almacenamiento.

    ---    

    Reemplace `<STORAGE_NAME>` por el nombre de la cuenta de almacenamiento que creó anteriormente.

1. Agregue esta configuración a la aplicación de funciones mediante el comando siguiente: 
 
    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    ```azurecli
    az functionapp config appsettings set --name <APP_NAME> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<CONNECTION_STRING>
    ```
    El comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) crea la configuración. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"AzureWebJobsStorage"="<CONNECTION_STRING>"}
    ```
    El cmdlet [Update-AzFunctionAppSetting](/powershell/module/az.functions/update-azfunctionappsetting) crea la configuración.

    ---

    En este comando, reemplace `<APP_NAME>` por el nombre de la aplicación de funciones y `<CONNECTION_STRING>` por la cadena de conexión del paso anterior. La conexión debe ser una cadena codificada larga que comience por `DefaultEndpointProtocol=`.
 

1. La función ahora puede usar esta cadena de conexión para acceder a la cuenta de almacenamiento.

> [!NOTE]    
> Si publica la imagen personalizada en una cuenta de contenedor privada, debe usar, en su lugar, variables de entorno en el archivo Dockerfile para la cadena de conexión. Para más información, consulte la [instrucción ENV](https://docs.docker.com/engine/reference/builder/#env). También debe establecer las variables `DOCKER_REGISTRY_SERVER_USERNAME` y `DOCKER_REGISTRY_SERVER_PASSWORD`. Para usar los valores, debe recompilar la imagen, insertarla en el registro y, a continuación, reiniciar la aplicación de funciones en Azure.

## <a name="verify-your-functions-on-azure"></a>Comprobación de las funciones en Azure

Con la imagen implementada en la aplicación de funciones en Azure, ya puede invocar la función como antes mediante solicitudes HTTP.
En el explorador, vaya a una dirección URL como la siguiente:

::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`  
::: zone-end  
::: zone pivot="programming-language-csharp"  
# <a name="in-process"></a>[En proceso](#tab/in-process) 
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`
# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)
`https://<APP_NAME>.azurewebsites.net/api/HttpExample`

---
:::zone-end  

Reemplace `<APP_NAME>` por el nombre de la aplicación de función. Cuando navegue a esta dirección URL, el explorador debe mostrar una salida similar a cuando ejecutó la función localmente.

## <a name="enable-continuous-deployment-to-azure"></a>Habilitar implementación continua en Azure

Puede habilitar Azure Functions para que actualice automáticamente la implementación de una imagen cada vez que actualice la imagen en el registro.

1. Habilite la implementación continua y obtenga la dirección URL del webhook mediante los siguientes comandos:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <APP_NAME> --resource-group AzureFunctionsContainers-rg
    ```
    
    El comando [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) permite habilitar la implementación continua y devuelve la dirección URL del webhook de implementación. Puede recuperar esta dirección URL en cualquier momento posterior mediante el comando [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url).

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"DOCKER_ENABLE_CI" = "true"}
    Get-AzWebAppContainerContinuousDeploymentUrl -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg
    ```
    
    La configuración de la aplicación `DOCKER_ENABLE_CI` controla si la implementación continua se habilita desde el repositorio de contenedores.  El cmdlet [Get-AzWebAppContainerContinuousDeploymentUrl](/powershell/module/az.websites/get-azwebappcontainercontinuousdeploymenturl) devuelve la dirección URL del webhook de implementación.

    ---    

    Como antes, reemplace `<APP_NAME>` por el nombre de la aplicación de funciones. 

1. Copie la dirección URL del webhook de implementación en el portapapeles.

1. Abra [Docker Hub](https://hub.docker.com/), inicie sesión y seleccione **Repositorios** en la barra de navegación. Busque y seleccione la imagen, seleccione la pestaña **Webhooks**, especifique un **nombre de webhook**, pegue la dirección URL en **Dirección URL de webhook** y, finalmente, seleccione **Crear**:

    ![Agregar el webhook al repositorio de DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Una vez establecido el webhook, Azure Functions volverá a implementar la imagen cada vez que la actualice en Docker Hub.

## <a name="enable-ssh-connections"></a>Habilitación de las conexiones SSH

SSH habilita la comunicación segura entre un contenedor y un cliente. Con SSH habilitado, puede conectarse al contenedor mediante las herramientas avanzadas de App Service (Kudu). Para facilitar la conexión al contenedor mediante SSH, Azure Functions proporciona una imagen base que ya tiene SSH habilitado. Solo necesita editar el archivo de Dockerfile y, después, recompilar y volver a implementar la imagen. Después, puede conectar el contenedor mediante Herramientas avanzadas (Kudu)

1. En el archivo de Dockerfile, anexe la cadena `-appservice` a la imagen base en la instrucción `FROM`:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. Vuelva a recompilar la imagen mediante el comando `docker build`, sustituyendo `<docker_id>` por el identificador de Docker:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Inserte la imagen actualizada en Docker Hub, lo cual ahora debe tardar mucho menos tiempo que la primera vez ya que solo se cargarán los segmentos actualizados de la imagen.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions vuelve a implementar automáticamente la imagen en la aplicación de funciones. El proceso se realiza en menos de un minuto.

1. En un explorador, abra `https://<app_name>.scm.azurewebsites.net/`, sustituyendo `<app_name>` por su nombre único. Esta dirección URL es el punto de conexión de Herramientas avanzadas (KUDU) para el contenedor de la aplicación de funciones.

1. Inicie sesión en su cuenta de Azure y, después, seleccione **SSH** para establecer una conexión con el contenedor. La conexión puede tardar unos minutos si Azure todavía está actualizando la imagen de contenedor.

1. Una vez establecida la conexión con el contenedor, ejecute el comando `top` para ver los procesos que se están ejecutando actualmente. 

    ![Comando top de Linux en ejecución en una sesión SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-azure-queue-storage"></a>Escritura en Azure Queue Storage

Azure Functions permite conectar funciones a otros servicios y recursos de Azure sin tener que escribir su propio código de integración. Estos *enlaces*, que representan la entrada y la salida, se declaran dentro de la definición de función. Los datos de los enlaces se proporcionan a la función como parámetros. Un *desencadenador* es un tipo especial de enlace de entrada. Si bien una función tiene un único desencadenador, puede tener varios enlaces de entrada y salida. Para más información, consulte [Conceptos básicos sobre los enlaces y desencadenadores de Azure Functions](functions-triggers-bindings.md).

En esta sección se muestra cómo integrar la función con una instancia de Azure Queue Storage. El enlace de salida que se agrega a esta función escribe datos de una solicitud HTTP en un mensaje de la cola.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Registro de extensiones de enlace
::: zone-end 

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Adición de código para usar el enlace de salida

Con el enlace de cola definido, ahora puede actualizar la función para escribir mensajes en la cola mediante el parámetro de enlace.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Actualización de la imagen en el registro

1. En la carpeta raíz, ejecute de nuevo `docker build` y, en esta ocasión, actualice la versión que está en la etiqueta a `v1.0.1`. Igual que antes, reemplace `<docker_id>` por el identificador de la cuenta de Docker Hub:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Inserte la imagen actualizada de nuevo en el repositorio con `docker push`:

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Dado que configuró la entrega continua, si actualiza la imagen en el registro se actualizará automáticamente la aplicación de funciones de nuevo en Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visualización del mensaje en la cola de Azure Storage

En un explorador, use la misma dirección URL de antes para invocar la función. El explorador debe mostrar la misma respuesta que antes, ya que no se modificó esa parte del código de la función. No obstante, el mensaje agregado escribió un mensaje mediante el parámetro `name` de URL en la cola de almacenamiento `outqueue`.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

Si desea seguir trabajando con Azure Functions y con los recursos que creó en este tutorial, puede dejar todos esos recursos activos. Como ha creado un plan Premium para Azure Functions, incurrirá en un pequeño costo continuo por día.

Para evitar estos costos, elimine el grupo de recursos `AzureFunctionsContainer-rg` para limpiar todos los recursos de ese grupo: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Pasos siguientes

+ [Supervisión de funciones](functions-monitoring.md)
+ [Opciones de escalado y hospedaje](functions-scale.md)
+ [Hospedaje sin servidor basado en Kubernetes](functions-kubernetes-keda.md)

[Claves de autorización]: functions-bindings-http-webhook-trigger.md#authorization-keys