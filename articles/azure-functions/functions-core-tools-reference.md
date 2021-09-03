---
title: Referencia de Azure Functions Core Tools
description: Documentación de referencia que admite Azure Functions Core Tools (func.exe).
ms.topic: reference
ms.date: 07/13/2021
ms.openlocfilehash: 90d4a9575c2fe6bb2dc4fbd18132e3bc21e4a07c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781356"
---
# <a name="azure-functions-core-tools-reference"></a>Referencia de Azure Functions Core Tools

En este artículo se proporciona documentación de referencia para Azure Functions Core Tools, que le permite desarrollar, administrar e implementar proyectos de Azure Functions desde el equipo local. Para más información sobre Core Tools, consulte [Uso de Azure Functions Core Tools](functions-run-local.md). 

Los comandos de Core Tools se organizan en los contextos siguientes y cada uno de ellos proporciona un conjunto único de acciones.

| Contexto de comando | Descripción |
| ----- | ----- |
| [`func`](#func-init) | Comandos que se usan para crear y ejecutar funciones en el equipo local. |
| [`func azure`](#func-azure-functionapp-fetch-app-settings) | Comandos para usar los recursos de Azure, incluida la publicación. |
| [`func durable`](#func-durable-delete-task-hub)    | Comandos para usar [Durable Functions](./durable/durable-functions-overview.md). |
| [`func extensions`](#func-extensions-install) | Comandos para instalar y administrar extensiones. |
| [`func kubernetes`](#func-kubernetes-deploy) | Comandos para usar Kubernetes y Azure Functions. |
| [`func settings`](#func-settings-decrypt)   | Comandos para administrar la configuración del entorno para el host local de Functions. |
| [`func templates`](#func-templates-list)  | Comandos para enumerar las plantillas de función disponibles. |

Antes de usar los comandos de este artículo, debe [instalar Core Tools](functions-run-local.md#install-the-azure-functions-core-tools). 

## <a name="func-init"></a>func init 

Crea un nuevo proyecto de Functions en un lenguaje específico.

```command
func init <PROJECT_FOLDER>
```

Cuando se proporciona `<PROJECT_FOLDER>`, el proyecto se crea en una nueva carpeta con este nombre. En caso contrario, se utiliza la carpeta actual.

`func init` admite las siguientes opciones, que corresponden solo a la versión 3.x o 2.x, a menos que se indique lo contrario:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Crea funciones de .NET como script de C#, que es el comportamiento de la versión 1.x. Solo es válido con `--worker-runtime dotnet`. |
| **`--docker`** | Crea un archivo Dockerfile para un contenedor con una imagen base en función del elemento `--worker-runtime` elegido. Use esta opción cuando vaya a publicar en un contenedor Linux personalizado. |
| **`--docker-only`** |  Agrega un Dockerfile a un proyecto existente. Solicita el entorno de ejecución de trabajo si no se especifica o se establece en local.settings.json. Use esta opción cuando vaya a publicar un proyecto existente en un contenedor de Linux personalizado. |
| **`--force`** | Inicializa el proyecto incluso cuando hay archivos existentes en el proyecto. Este valor sobrescribe los archivos existentes con el mismo nombre. Los otros archivos de la carpeta del proyecto no se ven afectados. |
| **`--language`** | Inicializa un proyecto específico del lenguaje. Se admite actualmente cuando `--worker-runtime` se establece en `node`. Las opciones son `typescript` y `javascript`. También puede usar `--worker-runtime javascript` o `--worker-runtime typescript`.  |
| **`--managed-dependencies`**  | Instala las dependencias administradas. Actualmente, solo admite esta funcionalidad el entorno de ejecución de trabajos de PowerShell. |
| **`--source-control`** | Controla si se crea un repositorio de git. De forma predeterminada, no se crea un repositorio. Cuando es `true`, se crea un repositorio. |
| **`--worker-runtime`** | Establece el entorno de ejecución del lenguaje del proyecto. Los valores admitidos son: `csharp`, `dotnet`, `dotnet-isolated`, `javascript`,`node` (JavaScript), `powershell`, `python` y `typescript`. En el caso de Java, utilice [Maven](functions-reference-java.md#create-java-functions). Para generar un proyecto independiente del lenguaje con solo los archivos de proyecto, use `custom`. Si no se establece, deberá elegir el entorno de ejecución durante la inicialización. |
|

> [!NOTE]
> Cuando se usan las opciones `--docker` o `--dockerfile`, Core Tools crea automáticamente el Dockerfile para las funciones de C#, JavaScript, Python y PowerShell. Para las funciones de Java, debe crear manualmente el Dockerfile. Use la [lista de imágenes](https://github.com/Azure/azure-functions-docker) de Azure Functions para buscar la imagen base correcta para su contenedor que ejecute Azure Functions.

## <a name="func-logs"></a>func logs

Obtiene los registros de las funciones que se ejecutan en un clúster de Kubernetes.

```
func logs --platform kubernetes --name <APP_NAME>
``` 

La acción `func logs` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------------------------------------ | -------------------------------------- |
| **`--platform`** | Plataforma de hospedaje de la aplicación de función. Opciones admitidas: `kubernetes`. |
| **`--name`** | Nombre de la aplicación de funciones en Azure. |

Para obtener más información, consulte [Azure Functions en Kubernetes con KEDA](functions-kubernetes-keda.md).

## <a name="func-new"></a>func new

Crea una nueva función en el proyecto actual basándose en una plantilla.

```
func new
``` 

La acción `func new` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------------------------------------ | -------------------------------------- |
| **`--authLevel`** | Permite establecer el nivel de autorización para un desencadenador HTTP. Los valores admitidos son: `function`, `anonymous` y `admin`. La autorización no se aplica cuando se ejecuta localmente. Para obtener más información, consulte el artículo sobre [enlaces HTTP](functions-bindings-http-webhook-trigger.md#authorization-keys). |
| **`--csx`** | (Versión 2.x y posteriores). Genera las mismas plantillas de script de C# (.csx) que se usan en la versión 1.x y en el portal. |
| **`--language`**, **`-l`**| Lenguaje de programación de la plantilla, como C#, F# o JavaScript. Esta opción es obligatoria en la versión 1.x. En la versión 2.x y versiones posteriores, no se usa esta opción porque el lenguaje está definido por el tiempo de ejecución de trabajo. |
| **`--name`**, **`-n`** | Nombre de función. |
| **`--template`**, **`-t`** | Use el comando `func templates list` para ver la lista completa de plantillas disponibles para cada lenguaje compatible.   |

Para obtener más información, consulte [Creación de una función](functions-run-local.md#create-func).

## <a name="func-run"></a>func run

*Solo versión 1.x.*

Permite invocar una función directamente, lo que es similar a ejecutar una función con la pestaña **Prueba** de Azure Portal. Esta acción solo se admite en la versión 1.x. Para versiones posteriores, use `func start` y [llame al punto de conexión de la función directamente](functions-run-local.md#passing-test-data-to-a-function).

```command
func run
```

La acción `func run` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--content`** | Contenido en línea pasado a la función. |
| **`--debug`** | Se asocia un depurador al proceso de host antes de ejecutar la función.|
| **`--file`** | Nombre del archivo que se usa como contenido.|
| **`--no-interactive`** | No solicita la entrada, lo que resulta útil para escenarios de automatización.|
| **`--timeout`** | Tiempo de espera (en segundos) hasta que el host local de Functions está listo.|

Por ejemplo, para llamar a una función desencadenada por HTTP y pasar cuerpo del contenido, ejecute el siguiente comando:

```
func run MyHttpTrigger --content '{\"name\": \"Azure\"}'
```

## <a name="func-start"></a>func start

Inicia el host en tiempo de ejecución local y carga el proyecto de función en la carpeta actual. 

El comando específico depende de la [versión en tiempo de ejecución](functions-versions.md).   

# <a name="v2x"></a>[v2.x+](#tab/v2)

```command
func start
```

`func start` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--cert`** | La ruta de acceso a un archivo .pfx que contiene una clave privada. Solo se admite con `--useHttps`. |
| **`--cors`** | Lista separada por comas de orígenes CORS, sin espacios en blanco. |
| **`--cors-credentials`** | Permite solicitudes autenticadas entre orígenes mediante cookies y el encabezado de autenticación. |
| **`--dotnet-isolated-debug`** | Cuando se establece en `true`, pausa el proceso de trabajo de .NET hasta que se adjunta un depurador desde el proyecto aislado de .NET que se está depurando. |
| **`--enable-json-output`** | Emite registros de consola como JSON, siempre que sea posible. |
| **`--enableAuth`** | Habilita la canalización de control de autenticación completa. |
| **`--functions`** | Lista separada por espacios de funciones que se cargarán. |
| **`--language-worker`** | Argumentos para configurar el trabajo del lenguaje. Por ejemplo, puede habilitar la depuración para el trabajo de lenguaje proporcionando el [puerto de depuración y otros argumentos necesarios](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). |
| **`--no-build`** | El proyecto actual no se compila antes de su ejecución. Solo para proyectos de clase .NET. De manera predeterminada, es `false`.  |
| **`--password`** | La contraseña o un archivo que contenga la contraseña de un archivo. pfx. Solo se usa con `--cert`. |
| **`--port`** | Puerto local en el que se escucha. Valor predeterminado: 7071. |
| **`--timeout`** | Tiempo de espera en segundos para que se inicie el host de Functions. Valor predeterminado: 20 segundos.|
| **`--useHttps`** | Enlace con `https://localhost:{port}` en lugar de con `http://localhost:{port}`. De forma predeterminada, esta opción crea un certificado de confianza en el equipo.|

Con el proyecto en ejecución, puede [comprobar los puntos de conexión de función individuales](functions-run-local.md#passing-test-data-to-a-function).

# <a name="v1x"></a>[v1.x](#tab/v1)

```command
func host start
```

`func start` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--cors`** | Lista separada por comas de orígenes CORS, sin espacios en blanco. |
| **`--port`** | Puerto local en el que se escucha. Valor predeterminado: 7071. |
| **`--pause-on-error`** | Se pone en pausa en espera de entrada adicional antes de salir del proceso. Se utiliza solo cuando se inicia Core Tools desde un entorno de desarrollo integrado (IDE).|
| **`--script-root`** | Se usa para especificar la ruta de acceso a la raíz de la aplicación de función que se va a ejecutar o implementar. Esto se usa para los proyectos compilados que generan archivos de proyecto en una subcarpeta. Por ejemplo, cuando se compila un proyecto de biblioteca de clases de C#, los archivos host.json, local.settings.json y function.json se generan en una subcarpeta *raíz* con una ruta de acceso similar a `MyProject/bin/Debug/netstandard2.0`. En este caso, establezca el prefijo como `--script-root MyProject/bin/Debug/netstandard2.0`. Esta es la raíz de la aplicación de función cuando se ejecuta en Azure. |
| **`--timeout`** | Tiempo de espera en segundos para que se inicie el host de Functions. Valor predeterminado: 20 segundos.|
| **`--useHttps`** | Enlace con `https://localhost:{port}` en lugar de con `http://localhost:{port}`. De forma predeterminada, esta opción crea un certificado de confianza en el equipo.|

En la versión 1.x, también puede usar el [comando `func run`](#func-run) para ejecutar una función específica y pasar datos de prueba a ella. 

---

## <a name="func-azure-functionapp-fetch-app-settings"></a>func azure functionapp fetch-app-settings

Obtiene la configuración de una aplicación de funciones específica.

```command
func azure functionapp fetch-app-settings <APP_NAME>
```

Para obtener un ejemplo, consulte [Obtención de las cadenas de conexión de almacenamiento](functions-run-local.md#get-your-storage-connection-strings).

La configuración se descarga en el archivo local.settings.json para el proyecto. Los valores en pantalla se enmascaran por motivos de seguridad. Puede proteger la configuración del archivo local.settings.json [habilitando el cifrado local](#func-settings-encrypt). 

## <a name="func-azure-functionapp-list-functions"></a>func azure functionapp list-functions

Devuelve una lista de las funciones de la aplicación de funciones especificada.

```command
func azure functionapp list-functions <APP_NAME>
```
## <a name="func-azure-functionapp-logstream"></a>func azure functionapp logstream

Conecta el símbolo del sistema local a los registros de streaming de la aplicación de funciones en Azure.

```command
func azure functionapp logstream <APP_NAME>
```

El tiempo de espera predeterminado para la conexión es de 2 horas. Puede cambiar el tiempo de espera agregando una configuración de la aplicación denominada [SCM_LOGSTREAM_TIMEOUT](functions-app-settings.md#scm_logstream_timeout), con un valor de tiempo de espera en segundos. Todavía no se admite para las aplicaciones de Linux en el plan de consumo. Para estas aplicaciones, use la opción `--browser` para ver los registros en el portal.

La acción `deploy` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--browser`** | Abra Azure Application Insights Live Stream para la aplicación de funciones en el explorador predeterminado. |

Para obtener más información, consulte [Habilitación de los registros de streaming](functions-run-local.md#enable-streaming-logs).

## <a name="func-azure-functionapp-publish"></a>func azure functionapp publish 

Implementa un proyecto de Functions en un recurso de aplicación de funciones existente en Azure. 

```command
func azure functionapp publish <FunctionAppName>
```

Para obtener más información, consulte [Implementación de los archivos de proyecto](functions-run-local.md#project-file-deployment).

Se aplican las siguientes opciones de publicación en función de la versión:

# <a name="v2x"></a>[v2.x+](#tab/v2)

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--additional-packages`** | Lista de paquetes para instalar al crear dependencias nativas. Por ejemplo: `python3-dev libevent-dev`. |
| **`--build`**, **`-b`** | Realiza la acción de compilación cuando se implementa en una aplicación de función de Linux. Acepta `remote` y `local`. |
| **`--build-native-deps`** | Omite la generación de la carpeta `.wheels` al publicar aplicaciones de funciones de Python. |
| **`--csx`** | Publica un proyecto de script de C# (.csx). |
| **`--force`** | Omite la comprobación previa a la publicación en determinados escenarios. |
| **`--dotnet-cli-params`** | Al publicar funciones compiladas de C# (.csproj), Core Tools llama a `dotnet build --output bin/publish`. Todos los parámetros pasados se anexarán a la línea de comandos. |
|**`--list-ignored-files`** | Muestra una lista de archivos que se omiten durante la publicación, según el archivo `.funcignore`. |
| **`--list-included-files`** | Muestra una lista de archivos que se publican, según el archivo `.funcignore`. |
| **`--no-build`** | El proyecto no se compila durante la publicación. En el caso de Python, `pip install` no se ejecuta. |
| **`--nozip`** | Desactiva el modo `Run-From-Package` predeterminado. |
| **`--overwrite-settings -y`** | Suprime el mensaje de sobrescritura de la configuración de la aplicación cuando se utiliza `--publish-local-settings -i`.|
| **`--publish-local-settings -i`** |  Se publica la configuración de local.settings.json en Azure, se pide que se sobrescriba si la configuración ya existe. Si usa el Emulador de Microsoft Azure Storage, cambie antes la configuración de la aplicación a una [conexión de almacenamiento real](functions-run-local.md#get-your-storage-connection-strings). |
| **`--publish-settings-only`**, **`-o`** |  Solo se publica la configuración y se omite el contenido. El valor predeterminado es Preguntar. |
| **`--slot`** | Nombre opcional de una ranura específica en la que se va a publicar. |

# <a name="v1x"></a>[v1.x](#tab/v1)

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--overwrite-settings -y`** | Suprime el mensaje de sobrescritura de la configuración de la aplicación cuando se utiliza `--publish-local-settings -i`.|
| **`--publish-local-settings -i`** |  Se publica la configuración de local.settings.json en Azure, se pide que se sobrescriba si la configuración ya existe. Si usa el Emulador de Microsoft Azure Storage, cambie antes la configuración de la aplicación a una [conexión de almacenamiento real](functions-run-local.md#get-your-storage-connection-strings). |

---

## <a name="func-azure-storage-fetch-connection-string"></a>func azure storage fetch-connection-string    

Obtiene la cadena de conexión de una cuenta de Azure Storage especificada.

```command
func azure storage fetch-connection-string <STORAGE_ACCOUNT_NAME>
```

## <a name="func-deploy"></a>func deploy

Implementa una aplicación de funciones en un contenedor de Linux personalizado en un clúster de Kubernetes sin KEDA.

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <DOCKER_USER>
```

Este comando compila el proyecto como un contenedor personalizado y lo publica en un clúster de Kubernetes mediante un escalador predeterminado o mediante KNative. Para publicar en un clúster mediante KEDA para la escala dinámica, use en su lugar el [comando `func kubernetes deploy`](#func-kubernetes-deploy). Los contenedores personalizados deben tener un archivo Dockerfile. Para crear una aplicación con un archivo Dockerfile, use la opción `--dockerfile` con el [comando `func init`](#func-init).     

La acción `deploy` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--config`** | Establece un archivo de configuración de implementación opcional. |
| **`--max`**  | Opcionalmente, establece el número máximo de instancias de la aplicación de función que se va a implementar. |
| **`--min`**  | Opcionalmente, establece el número mínimo de instancias de la aplicación de función que se va a implementar. |
| **`--name`** | Nombre de la aplicación de funciones (obligatorio). |
| **`--platform`** | Plataforma de hospedaje de la aplicación de funciones (obligatorio). Las opciones válidas son: `kubernetes` y `knative`.|
| **`--registry`** | Nombre de un registro de Docker en el que el usuario actual ha iniciado sesión (obligatorio). |

Core Tools usa la CLI local de Docker para compilar y publicar la imagen. 

Asegúrese de que Docker ya esté instalado localmente. Ejecute el comando `docker login` para conectarse a su cuenta.

## <a name="func-durable-delete-task-hub"></a>func durable delete-task-hub

Elimina todos los artefactos de almacenamiento de la central de tareas de Durable Functions.

```command
func durable delete-task-hub
```

La acción `delete-task-hub` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nombre opcional de la configuración que contiene la cadena de conexión de almacenamiento que se va a usar. |
| **`--task-hub-name`** |             Nombre opcional de la central de tareas de Durable que se va a usar. |

Para obtener más información, consulte la [documentación de Durable Functions](./durable/durable-functions-instance-management.md#delete-a-task-hub).

## <a name="func-durable-get-history"></a>func durable get-history

Devuelve el historial de la instancia de orquestación especificada.

```command
func durable get-history --id <INSTANCE_ID>
```

La acción `get-history` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--id`** | Especifica el identificador de una instancia de orquestación (obligatorio). |
| **`--connection-string-setting`** | Nombre opcional de la configuración que contiene la cadena de conexión de almacenamiento que se va a usar. |
| **`--task-hub-name`** |             Nombre opcional de la central de tareas de Durable que se va a usar. |

Para obtener más información, consulte la [documentación de Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1).

## <a name="func-durable-get-instances"></a>func durable get-instances

Devuelve el estado de todas las instancias de orquestación. Admite la paginación mediante el parámetro `top`.

```command
func durable get-instances
```

La acción `get-instances` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--continuation-token`** | Token opcional que indica una página o sección específica de las solicitudes que se devuelven. |
| **`--connection-string-setting`** | Nombre opcional de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. |
| **`--created-after`** | Opcionalmente, obtenga las instancias creadas después de esta fecha y hora (UTC). Se aceptan todos los valores datetime con formato ISO 8601. |
| **`--created-before`** | Opcionalmente, obtenga las instancias creadas antes de una fecha y hora (UTC) específicas. Se aceptan todos los valores datetime con formato ISO 8601. |
| **`--runtime-status`** | Opcionalmente, obtenga las instancias cuyo estado coincida con un estado específico, incluidos `running`, `completed` y `failed`. Puede proporcionar uno o varios estados separados por espacios. |
| **`--top`** | Opcionalmente, limite el número de registros devueltos en una solicitud determinada. |
| **`--task-hub-name`** | Nombre opcional de la central de tareas de Durable Functions que se va a usar. |

Para obtener más información, consulte la [documentación de Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-2).

## <a name="func-durable-get-runtime-status"></a>func durable get-runtime-status  

Devuelve el estado de una instancia de orquestación especificada.

```command
func durable get-runtime-status --id <INSTANCE_ID>
```

La acción `get-runtime-status` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nombre opcional de la configuración que contiene la cadena de conexión de almacenamiento que se va a usar. |
| **`--id`** | Especifica el identificador de una instancia de orquestación (obligatorio). |
| **`--show-input`** | Una vez establecida, la respuesta contiene la entrada de la función. |
| **`--show-output`** | Una vez establecida, la respuesta contiene el historial de ejecución. |
| **`--task-hub-name`** | Nombre opcional de la central de tareas de Durable Functions que se va a usar. |

Para obtener más información, consulte la [documentación de Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1).

## <a name="func-durable-purge-history"></a>func durable purge-history

Se purga el estado, el historial y el servicio Blob Storage de la instancia de orquestación para orquestaciones anteriores al umbral especificado.

```command
func durable purge-history
```

La acción `purge-history` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nombre opcional de la configuración que contiene la cadena de conexión de almacenamiento que se va a usar. |
| **`--created-after`** | Opcionalmente, elimine el historial de instancias creadas después de esta fecha y hora (UTC). Se aceptan todos los valores datetime con formato ISO 8601. |
| **`--created-before`** | Opcionalmente, elimine el historial de instancias creadas antes de esta fecha y hora (UTC). Se aceptan todos los valores datetime con formato ISO 8601.|
| **`--runtime-status`** | Opcionalmente, elimine el historial de instancias cuyo estado coincida con un estado específico, incluidos `completd`, `terminated`, `canceled` y `failed`. Puede proporcionar uno o varios estados separados por espacios. Si no incluye `--runtime-status`, el historial de instancias se elimina independientemente del estado.|
| **`--task-hub-name`** | Nombre opcional de la central de tareas de Durable Functions que se va a usar. |

Para obtener más información, consulte la [documentación de Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-7).

## <a name="func-durable-raise-event"></a>func durable raise-event         

Genera un evento en la instancia de orquestación especificada.

```command
func durable raise-event --event-name <EVENT_NAME> --event-data <DATA>
```

La acción `raise-event` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nombre opcional de la configuración que contiene la cadena de conexión de almacenamiento que se va a usar. |
| **`--event-data`** | Datos que se pasan al evento, ya sea en línea o desde un archivo JSON (obligatorio). En el caso de los archivos, prefije la ruta de acceso al archivo con una arroba (`@`), como `@path/to/file.json`. |
| **`--event-name`** | Nombre del evento que se va a generar (obligatorio).
| **`--id`** | Especifica el identificador de una instancia de orquestación (obligatorio). |
| **`--task-hub-name`** | Nombre opcional de la central de tareas de Durable Functions que se va a usar. |

Para obtener más información, consulte la [documentación de Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-5).

## <a name="func-durable-rewind"></a>func durable rewind              

Devuelve a un estado anterior a la instancia de orquestación especificada.

```command
func durable rewind --id <INSTANCE_ID> --reason <REASON>
```

La acción `rewind` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nombre opcional de la configuración que contiene la cadena de conexión de almacenamiento que se va a usar. |
| **`--id`** | Especifica el identificador de una instancia de orquestación (obligatorio). |
| **`--reason`** | Motivo para devolver a un estado anterior a la orquestación (obligatorio).|
| **`--task-hub-name`** | Nombre opcional de la central de tareas de Durable Functions que se va a usar. |

Para obtener más información, consulte la [documentación de Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-6).

## <a name="func-durable-start-new"></a>func durable start-new

Inicia una nueva instancia de la función de orquestador especificada.

```command
func durable start-new --id <INSTANCE_ID> --function-name <FUNCTION_NAME> --input <INPUT>
```

La acción `start-new` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nombre opcional de la configuración que contiene la cadena de conexión de almacenamiento que se va a usar. |
| **`--function-name`** | Nombre de la función de orquestador que se va a iniciar (obligatorio).|
| **`--id`** | Especifica el identificador de una instancia de orquestación (obligatorio). |
| **`--input`** | Entrada a la función de orquestador, ya sea en línea o desde un archivo JSON (obligatorio). En el caso de los archivos, prefije la ruta de acceso al archivo con una arroba (`@`), como `@path/to/file.json`. |
| **`--task-hub-name`** | Nombre opcional de la central de tareas de Durable Functions que se va a usar. |

Para obtener más información, consulte la [documentación de Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools).

## <a name="func-durable-terminate"></a>func durable terminate

Detiene la instancia de orquestación especificada.

```command
func durable terminate --id <INSTANCE_ID> --reason <REASON>
```

La acción `terminate` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nombre opcional de la configuración que contiene la cadena de conexión de almacenamiento que se va a usar. |
| **`--id`** | Especifica el identificador de una instancia de orquestación (obligatorio). |
| **`--reason`** | Motivo para detener la orquestación (obligatorio). |
| **`--task-hub-name`** | Nombre opcional de la central de tareas de Durable Functions que se va a usar. |

Para obtener más información, consulte la [documentación de Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-4).

## <a name="func-extensions-install"></a>func extensions install

Instala extensiones de Functions en un proyecto de biblioteca de clases que no es de C#. 

Cuando sea posible, en su lugar debe usar conjuntos de extensiones. Para obtener más información, consulte [Conjuntos de extensiones](functions-bindings-register.md#extension-bundles).

En el caso de la biblioteca de clases de C# y los proyectos aislados de .NET, use en su lugar métodos de instalación de paquetes NuGet estándar como `dotnet add package`.

La acción `install` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | Ruta de acceso del directorio que contiene el archivo extensions.csproj.|
| **`--csx`** |   Admite proyectos de scripting de C# (.csx). |
| **`--force`** |  Actualice las versiones de las extensiones existentes. |
| **`--output`** |  Ruta de acceso de salida para las extensiones. |
| **`--package`** |   Identificador de un paquete de extensión específico. Cuando no se especifica, se instalan todas las extensiones a las que se hace referencia, como con `func extensions sync`.|
| **`--source`** |  Origen de una fuente NuGet cuando no se usa NuGet.org.|
| **`--version`** |  Versión del paquete de extensión. |

No se realiza ninguna acción cuando se define un conjunto de extensiones en el archivo host.json.

## <a name="func-extensions-sync"></a>func extensions sync

Instala todas las extensiones agregadas a la aplicación de funciones.

La acción `sync` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | Ruta de acceso del directorio que contiene el archivo extensions.csproj.|
| **`--csx`** |   Admite proyectos de scripting de C# (.csx). |
| **`--output`** |  Ruta de acceso de salida para las extensiones. |

Regenera un archivo extensions.csproj que falta. No se realiza ninguna acción cuando se define un conjunto de extensiones en el archivo host.json.

## <a name="func-kubernetes-deploy"></a>func kubernetes deploy

Implementa un proyecto de Functions como un contenedor de Docker personalizado en un clúster de Kubernetes mediante KEDA.

```command
func kubernetes deploy 
```

Este comando compila el proyecto como un contenedor personalizado y lo publica en un clúster de Kubernetes mediante KEDA para el escalado dinámico. Para publicar en un clúster mediante un escalador predeterminado o mediante KNative, use en su lugar el [comando `func deploy`](#func-deploy). Los contenedores personalizados deben tener un archivo Dockerfile. Para crear una aplicación con un archivo Dockerfile, use la opción `--dockerfile` con el [comando `func init`](#func-init). 

Están disponibles las siguientes opciones de implementación de Kubernetes:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | Opcionalmente, muestra la plantilla de implementación, sin ejecución. |
| **`--config-map-name`** | Nombre opcional de una asignación de configuración existente con la [configuración de la aplicación de funciones](functions-how-to-use-azure-function-app-settings.md#settings) que se usará en la implementación. Se requiere `--use-config-map`. El comportamiento predeterminado es crear valores basados en el objeto `Values` en el [archivo local.settings.json].|
| **`--cooldown-period`** | El período de recuperación (en segundos) después de que todos los desencadenadores ya no estén activos antes de que la implementación se vuelva a reducir verticalmente a cero, con un valor predeterminado de 300 s. |
| **`--ignore-errors`** | Continúa la implementación después de que un recurso devuelva un error. El comportamiento predeterminado es detenerse al encontrar un error. |
| **`--image-name`** | Nombre de la imagen que se va a usar para la implementación del pod y desde la que se leerán las funciones. |
| **`--keda-version`** | Establece la versión de KEDA que se va a instalar. Las opciones válidas son: `v1` y `v2` (predeterminado). |
| **`--keys-secret-name`** | Nombre de una colección de secretos de Kubernetes que se usará para almacenar [claves de acceso de función](functions-bindings-http-webhook-trigger.md#authorization-keys). |
| **`--max-replicas`** | Establece el número máximo de réplicas para el que se escala Horizontal Pod Autoscaler (HPA). |
| **`--min-replicas`** | Establece el número mínimo de réplicas por debajo del cual HPA no se escala. |
| **`--mount-funckeys-as-containervolume`** | Monta las [claves de acceso de función](functions-bindings-http-webhook-trigger.md#authorization-keys) como un volumen de contenedor. |
| **`--name`** | Nombre usado para la implementación y otros artefactos en Kubernetes. |
| **`--namespace`** | Establece el espacio de nombres de Kubernetes en el que se va a implementar, que tiene como valor predeterminado el espacio de nombres predeterminado. |
| **`--no-docker`** | Las funciones se leen desde el directorio actual en lugar de desde una imagen. Requiere montar el sistema de archivos de imagen. |
| **`--registry`** | Una vez establecido, se ejecuta una compilación de Docker y la imagen se inserta en un registro con ese nombre. No se puede usar `--registry` con `--image-name`. Para Docker, use su nombre de usuario. |
| **`--polling-interval`** | Intervalo de sondeo (en segundos) para comprobar desencadenadores que no son HTTP, con un valor predeterminado de 30 segundos. |
| **`--pull-secret`** | Secreto que se usa para acceder a las credenciales del registro privado. |
| **`--secret-name`** | Nombre de una colección de secretos de Kubernetes existente que contiene la [configuración de la aplicación de funciones](functions-how-to-use-azure-function-app-settings.md#settings) que se usará en la implementación. El comportamiento predeterminado es crear valores basados en el objeto `Values` en el [archivo local.settings.json]. |
| **`--show-service-fqdn`** | Muestra las direcciones URL de los desencadenadores HTTP con el FQDN de Kubernetes en lugar del comportamiento predeterminado de usar una dirección IP. |
| **`--service-type`** | Establece el tipo de Kubernetes Service. Los valores admitidos son: `ClusterIP`, `NodePort` y `LoadBalancer` (predeterminado). |
| **`--use-config-map`** | Use un objeto `ConfigMap` (v1) en lugar de un objeto `Secret` (v1) para configurar la [configuración de la aplicación de funciones](functions-how-to-use-azure-function-app-settings.md#settings). El nombre del mapa se establece mediante `--config-map-name`.|

Core Tools usa la CLI local de Docker para compilar y publicar la imagen. 

Asegúrese de que Docker ya esté instalado localmente. Ejecute el comando `docker login` para conectarse a su cuenta.

Para obtener más información, consulte [Implementación de una aplicación de funciones en Kubernetes](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes).

## <a name="func-kubernetes-install"></a>func kubernetes install

Instala KEDA en un clúster de Kubernetes.

```command
func kubernetes install 
```

Instala KEDA en el clúster definido en el archivo de configuración kubectl.

La acción `install` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | Muestra la plantilla de implementación, sin ejecución. |
| **`--keda-version`** | Establece la versión de KEDA que se va a instalar. Las opciones válidas son: `v1` y `v2` (predeterminado). |
| **`--namespace`** | Admite la instalación en un espacio de nombres de Kubernetes específico. Si no se establece, se usa el espacio de nombres predeterminado. |

Para obtener más información, consulte [Administración de KEDA y Functions en Kubernetes](functions-kubernetes-keda.md#managing-keda-and-functions-in-kubernetes).

## <a name="func-kubernetes-remove"></a>func kubernetes remove

Quita KEDA del clúster de Kubernetes definido en el archivo de configuración kubectl.

```command
func kubernetes remove 
```

Quita KEDA del clúster definido en el archivo de configuración kubectl.

La acción `remove` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--namespace`** | Admite la desinstalación de un espacio de nombres de Kubernetes específico. Si no se establece, se usa el espacio de nombres predeterminado. | 

Para obtener más información, consulte [Desinstalación de KEDA de Kubernetes](functions-kubernetes-keda.md#uninstalling-keda-from-kubernetes).

## <a name="func-settings-add"></a>func settings add

Añade una nueva configuración a la colección `Values` en el [archivo local.settings.json].

```command
func settings add <SETTING_NAME> <VALUE>
```

Reemplace `<SETTING_NAME>` con el nombre de la configuración de la aplicación y `<VALUE>` con el valor de la configuración. 

La acción `add` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | Añade el par nombre-valor a la colección `ConnectionStrings` en lugar de a la colección `Values`. Use solo la colección `ConnectionStrings` cuando lo requieran determinados marcos. Para obtener más información, consulte el [archivo local.settings.json]. |

## <a name="func-settings-decrypt"></a>func settings decrypt

Descifra los valores cifrados previamente en la colección `Values` en el [archivo local.settings.json].

```command
func settings decrypt
```

También se descifran los valores de cadena de conexión de la colección `ConnectionStrings`. En local.settings.json, `IsEncrypted` también se establece en `false`. Cifre la configuración local para reducir el riesgo de pérdida de información valiosa de local.settings.json. En Azure, las configuraciones de aplicación siempre se almacenan cifradas. 

## <a name="func-settings-delete"></a>func settings delete

Quita una configuración existente de la colección `Values` en el [archivo local.settings.json].

```command
func settings delete <SETTING_NAME>
```

Reemplace `<SETTING_NAME>` con el nombre de la configuración de la aplicación y `<VALUE>` con el valor de la configuración. 

La acción `delete` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | Quita el par nombre-valor de la colección `ConnectionStrings` en lugar de hacerlo de la colección `Values`. |

## <a name="func-settings-encrypt"></a>func settings encrypt

Cifra los valores de elementos individuales en la colección `Values` en el [archivo local.settings.json].

```command
func settings encrypt
```

También se cifran los valores de cadena de conexión de la colección `ConnectionStrings`. En local.settings.json, `IsEncrypted` también se establece en `true`, lo que especifica que el tiempo de ejecución local descifra la configuración antes de usarla. Cifre la configuración local para reducir el riesgo de pérdida de información valiosa de local.settings.json. En Azure, las configuraciones de aplicación siempre se almacenan cifradas. 

## <a name="func-settings-list"></a>func settings list

Genera una lista de opciones en la colección `Values` en el [archivo local.settings.json]. 

```command
func settings list
```

También se generan las cadenas de conexión de la colección `ConnectionStrings`. De forma predeterminada, los valores se enmascaran por motivos de seguridad. Puede usar la opción `--showValue` para mostrar el valor real.

La acción `list` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--showValue`** | Muestra los valores reales sin máscara en la salida. |

## <a name="func-templates-list"></a>func templates list

Enumera las plantillas de función (desencadenador) disponibles.

La acción `list` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--language`** | Lenguaje para el que filtrar las plantillas devueltas. El valor predeterminado es devolver todos los lenguajes. |

[archivo local.settings.json]: functions-develop-local.md#local-settings-file.
