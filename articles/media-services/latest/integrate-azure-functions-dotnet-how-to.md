---
title: Desarrollo de Azure Functions con Media Services v3
description: En este artículo se muestra cómo empezar a desarrollar Azure Functions con Media Services v3 mediante Visual Studio Code.
services: media-services
author: xpouyat
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 06/09/2021
ms.author: xpouyat
ms.custom: devx-track-csharp
ms.openlocfilehash: 523a5dbfb503f47f15e44e7be1b61bf6cf05c471
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661723"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Desarrollo de Azure Functions con Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En este artículo se muestra cómo empezar a crear instancias de Azure Functions que usan Media Services. La función de Azure definida en este artículo codifica un archivo de vídeo con Media Encoder Standard. En cuanto se ha creado el trabajo de codificación, la función devuelve el nombre del trabajo y el nombre del recurso de salida. Para revisar Azure Functions, consulte [Introducción](../../azure-functions/functions-overview.md) y otros temas relacionados en la sección **Azure Functions**.

Si desea explorar e implementar instancias de Azure Functions existentes que usan Azure Media Services, visite [Azure Functions de Media Services](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration). Este repositorio incluye ejemplos que usan Media Services para mostrar flujos de trabajo relacionados con la ingesta directa de contenido desde Blob Storage, la codificación y las operaciones de streaming en vivo.

## <a name="prerequisites"></a>Prerrequisitos

- Para poder crear la primera función, es necesario tener una cuenta de Azure activa. Si aún no tiene una cuenta de Azure, [tiene a su disposición la creación de una cuenta gratis](https://azure.microsoft.com/free/).
- Si va a crear instancias de Azure Functions que llevan a cabo acciones en su cuenta de Azure Media Services (AMS) o escuchan eventos enviados por Media Services, debe crear una cuenta de AMS, tal como se describe [aquí](account-create-how-to.md).
- Instale [Visual Studio Code](https://code.visualstudio.com/) en una de las [plataformas compatibles](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

En este artículo se explica cómo crear una función de .NET 5 de C# que se comunique con Azure Media Services. Para crear una función con otro lenguaje, consulte este [artículo](../../azure-functions/functions-develop-vs-code.md).

### <a name="run-local-requirements"></a>Ejecución de requisitos locales

Estos requisitos previos solo son necesarios para ejecutar y depurar las funciones localmente, pero no lo son para crear o publicar proyectos en Azure Functions.

- [SDK de .NET Core 3.1 y .NET 5](https://dotnet.microsoft.com/download/dotnet).

- La versión 3.x de [Azure Functions Core Tools](../../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools), o cualquier versión posterior. El paquete Core Tools se descarga e instala automáticamente al iniciar el proyecto de forma local. Core Tools incluye el entorno de ejecución completo de Azure Functions, por lo que la descarga y la instalación pueden tardar algún tiempo.

- La [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code.

## <a name="install-the-azure-functions-extension"></a>Instalación de la extensión de Azure Functions

Puede usar la extensión de Azure Functions para crear y probar funciones e implementarlas en Azure.

1. En Visual Studio Code, abra **Extensiones** y busque **Azure Functions**, o bien seleccione este vínculo en Visual Studio Code: [`vscode:extension/ms-azuretools.vscode-azurefunctions`](vscode:extension/ms-azuretools.vscode-azurefunctions).

1. Seleccione **Instalar** para instalar la extensión en Visual Studio Code.

    ![Instalación de la extensión de Azure Functions](./Media/integrate-azure-functions-dotnet-how-to/vscode-install-extension.png)

1. Después de la instalación, seleccione el icono de Azure en la barra de actividad. Debería ver un área de Azure Functions en la barra lateral.

    ![Área de Azure Functions en la barra lateral](./Media/integrate-azure-functions-dotnet-how-to/azure-functions-window-vscode.png)

## <a name="create-an-azure-functions-project"></a>Creación de un proyecto de Azure Functions

La extensión de Functions le permite crear un proyecto de aplicación de funciones, junto con su primera función. Los pasos siguientes muestran cómo crear una función desencadenada por HTTP en un nuevo proyecto de funciones. Desencadenador HTTP es la plantilla de desencadenador de función más sencilla de mostrar.

1. En **Azure: Functions**, seleccione el icono **Crear función**:

    ![Creación de una función](./Media/integrate-azure-functions-dotnet-how-to/create-function.png)

1. Seleccione la carpeta para el proyecto de aplicación de funciones y, a continuación, **Seleccione C# para el proyecto de funciones** y **.NET 5 Isolated**  para el tiempo de ejecución.

1. Seleccione la plantilla de función **Desencadenador de HTTP**.

    ![Elegir la plantilla del desencadenador HTTP](./Media/integrate-azure-functions-dotnet-how-to/create-function-choose-template.png)

1. Escriba **HttpTriggerEncode** como nombre de la función y seleccione Enter; acepte **Company.Function** como espacio de nombres y luego seleccione **Función** para los derechos de acceso. Este nivel de autorización requiere que proporcione un valor para la [clave de función](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) al llamar al punto de conexión de la función.

    ![Selección de la autorización de función](./Media/integrate-azure-functions-dotnet-how-to/create-function-auth.png)

    Se crea una función en el lenguaje elegido y en la plantilla para una función desencadenada por HTTP.

    ![Plantilla de función desencadenada por HTTP en Visual Studio Code](./Media/integrate-azure-functions-dotnet-how-to/new-function-full.png)

## <a name="install-media-services-and-other-extensions"></a>Instalación de Media Services y otras extensiones

Ejecute el comando dotnet add package en la ventana del terminal para instalar los paquetes de extensión que necesita en el proyecto. El siguiente comando instala el paquete de Media Services y otras extensiones necesarias para el ejemplo.

```bash
dotnet add package Azure.Storage.Blobs
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

## <a name="generated-project-files"></a>Archivos de proyecto generados

La plantilla de proyecto crea un proyecto en el lenguaje elegido e instala las dependencias necesarias. El proyecto nuevo contiene los archivos siguientes:

* **host.json**: permite configurar el host de Functions. Esta configuración se aplica cuando se ejecutan funciones localmente y cuando se ejecutan en Azure. Para más información, consulte la [referencia sobre host.json](./../../azure-functions/functions-host-json.md).

* **local.settings.json**: mantiene la configuración que se usa al ejecutar localmente las funciones. Esta configuración solo se utiliza al ejecutar las funciones de forma local.

    >[!IMPORTANT]
    >Debido a que el archivo local.settings.json puede contener secretos, tiene que excluirlo del control de origen del proyecto.

* Archivo de clase **HttpTriggerEncode.cs** que implementa la función.

### <a name="httptriggerencodecs"></a>HttpTriggerEncode.cs

Este es el código C# para la función. Su función es llevar un recurso de Media Services a una dirección URL de origen e iniciar un trabajo de codificación con Media Services. Usa una transformación que se crea si no existe. Cuando se crea, usa el valor preestablecido que se proporciona en el cuerpo de entrada. 

>[!IMPORTANT]
>Reemplace el contenido completo del archivo HttpTriggerEncode.cs con [`HttpTriggerEncode.cs` de este repositorio](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/blob/main/Tutorial/HttpTriggerEncode.cs).

Una vez que haya terminado de definir la función, haga clic en **Guardar y ejecutar**.

El código fuente del método **Run** de la función es:

[!code-csharp[Main](../../../media-services-v3-dotnet-core-functions-integration/Tutorial/HttpTriggerEncode.cs#Run)]

### <a name="localsettingsjson"></a>local.settings.json

Actualice el archivo con el siguiente contenido (y reemplace los valores).

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated",
    "AadClientId": "00000000-0000-0000-0000-000000000000",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "00000000-0000-0000-0000-000000000000",
    "AadTenantId": "00000000-0000-0000-0000-000000000000",
    "AccountName": "amsaccount",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "ResourceGroup": "amsResourceGroup",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000"
  }
}
```

## <a name="test-your-function"></a>Probar la función

Al ejecutar la función localmente en VS Code, la función debe exponerse como: 

```url
http://localhost:7071/api/HttpTriggerEncode
```

Para probarla, puede usar Postman para realizar una solicitud POST en esta dirección URL mediante un cuerpo de entrada JSON.

Ejemplo de cuerpo de entrada JSON:

```json
{
    "inputUrl":"https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4",
    "transformName" : "TransformAS",
    "builtInPreset" :"AdaptiveStreaming"
 }
```

La función debe devolver 200 OK con un cuerpo de salida que contenga los nombres de los recursos de trabajo y salida.

![Prueba de la función con Postman](./Media/integrate-azure-functions-dotnet-how-to/postman.png)

## <a name="next-steps"></a>Pasos siguientes

Ya está listo para empezar a desarrollar funciones que llamen a la API de Media Services.

Para obtener más información y un ejemplo completo del uso de Azure Functions con Azure Media Services v3, consulte [Ejemplo de Azure Functions con Media Services v3](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/main/Functions).
