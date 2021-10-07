---
title: 'Inicio rápido: Creación de la primera función de C# en Azure mediante Visual Studio'
description: En este inicio rápido, aprenderá a usar Visual Studio para crear y publicar una función de C# desencadenada por HTTP en Azure Functions que se ejecuta en .NET Core 3.1.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/14/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, contperf-fy21q3-portal
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
zone_pivot_groups: runtime-version-programming-functions
ms.openlocfilehash: fb969f494c350d253d688d3a5379c30513aa64fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671267"
---
# <a name="quickstart-create-your-first-c-function-in-azure-using-visual-studio"></a>Inicio rápido: Creación de la primera función de C# en Azure mediante Visual Studio

[!INCLUDE [functions-runtime-version-dotnet](../../includes/functions-runtime-version-dotnet.md)]

Azure Functions permite ejecutar el código de C# en un entorno sin servidor en Azure. 

En este artículo aprenderá a:

> [!div class="checklist"]
> * Use Visual Studio para crear un proyecto de biblioteca de clases de C#.
> * Cree una función que responda a solicitudes HTTP. 
> * Ejecute el código localmente para comprobar el comportamiento de la función.
> * Implemente el proyecto de código en Azure Functions.
 
::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
> [!NOTE]
> Actualmente, Azure Functions 4.0 está en versión preliminar y Visual Studio admite la creación de funciones de C# que solo se ejecutan en .NET 6 mediante el [modelo de ejecución en proceso](functions-dotnet-class-library.md).
::: zone-end

Este inicio rápido supone un pequeño costo en su cuenta de Azure.

También hay una [versión basada en Visual Studio Code](create-first-function-vs-code-csharp.md) de este artículo.

## <a name="prerequisites"></a>Prerrequisitos

::: zone pivot="programming-runtime-functions-v3"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/). Asegúrese de seleccionar la carga de trabajo de **desarrollo de Azure** durante la instalación. 
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
+ [Visual Studio 2022, versión preliminar 3 o posterior](https://azure.microsoft.com/downloads/). Asegúrese de seleccionar la carga de trabajo de **desarrollo de Azure** durante la instalación. 
::: zone-end

+ [Suscripción de Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). Si aún no tiene ninguna cuenta, cree una [gratuita](https://azure.microsoft.com/free/dotnet/) antes de empezar.

## <a name="create-a-function-app-project"></a>Creación de un proyecto de aplicación de función

::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]
::: zone-end

::: zone pivot="programming-runtime-functions-v4"
La plantilla del proyecto de Azure Functions de Visual Studio crea un proyecto de biblioteca de clases de C# que puede publicar en una aplicación de funciones en Azure. Una aplicación de funciones permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación, el escalado y el uso compartido de recursos.

1. En el menú de Visual Studio, seleccione **Archivo** > **Nuevo** > **Proyecto**.

1. En **Crear un proyecto**, escriba *functions* en el cuadro de búsqueda, elija la plantilla **Azure Functions** y seleccione **Siguiente**.

1. En **Configurar el nuevo proyecto**, escriba un **nombre de proyecto**  para el proyecto y, a continuación, seleccione **Crear**. El nombre de la aplicación de función debe ser válido como espacio de nombres de C#, por lo que no debe usar guiones bajos, guiones u otros caracteres no alfanuméricos.

1. En la configuración de **Crear aplicación de Azure Functions**, use los valores de la tabla siguiente:

    | Configuración      | Value  | Descripción                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versión de .NET** | **.NET 6** | Este valor crea un proyecto de función que se ejecuta dentro de proceso con la versión 4.x del entorno de ejecución de Azure Functions. Azure Functions 1.x admite .NET Framework. Para más información, consulte [Selección de un destino para versiones de runtime de Azure Functions](./functions-versions.md).   |
    | **Plantilla de función** | **desencadenador HTTP** | Este valor crea una función desencadenada por una solicitud HTTP. |
    | **Cuenta de almacenamiento (AzureWebJobsStorage)**  | **Emulador de Storage** | Dado que una aplicación de funciones de Azure necesita una cuenta de almacenamiento, se asigna o se crea una cuando publica su proyecto en Azure. Un desencadenador HTTP no utiliza una cadena de conexión de cuenta de Azure Storage; todos los demás tipos de desencadenador requieren una cadena de conexión de cuenta de Azure Storage válida.  |
    | **Nivel de autorización** | **Anónimo** | Cualquier cliente puede desencadenar una función creada sin tener que proporcionar una clave. Esta configuración de autorización facilita probar la función nueva. Para más información sobre las claves y la autorización, consulte [Claves de autorización](./functions-bindings-http-webhook-trigger.md#authorization-keys) e [Introducción a los enlaces y desencadenadores HTTP de Azure Functions](./functions-bindings-http-webhook.md). |
    
    :::image type="content" source="../../includes/media/functions-vs-tools-create/functions-project-settings-v4.png" alt-text="Configuración de un proyecto de Azure Functions":::

    Asegúrese de establecer el **Nivel de autorización** en **Anónimo**. Al elegir el nivel predeterminado de **Función**, tiene que presentar la [tecla de función](./functions-bindings-http-webhook-trigger.md#authorization-keys) en las solicitudes para acceder al punto de conexión de la función.

1. Seleccione **Crear** para crear el proyecto de función y la función con desencadenador HTTP.

::: zone-end

Visual Studio crea un proyecto y una clase que contiene código reutilizable para el tipo de función de desencadenador HTTP. El código reutilizable envía una respuesta HTTP que incluye un valor del cuerpo de la solicitud o de la cadena de consulta. El atributo `HttpTrigger` especifica que la función es desencadenada por una solicitud HTTP.

## <a name="rename-the-function"></a>Cambio del nombre de la función

El atributo del método `FunctionName` establece el nombre de la función que, de forma predeterminada, se genera como `Function1`. Como las herramientas no le permiten reemplazar el nombre de la función predeterminada al crear un proyecto, dedique un minuto a crear un nombre mejor para la clase, el archivo y los metadatos de la función.

1. En el **Explorador de archivos**, haga clic con el botón derecho en el archivo Function1.cs y cambie su nombre a `HttpExample.cs`.

1. En el código, cambie el nombre de la clase Function1 a `HttpExample`.

1. En el método `HttpTrigger` denominado `Run`, cambie el nombre del atributo del método `FunctionName` a `HttpExample`. 

La definición de la función ahora debe parecerse al código siguiente:

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs" range="15-18"::: 

Ahora que ha cambiado el nombre de la función, puede probarla en el equipo local.

## <a name="run-the-function-locally"></a>Ejecución local de la función

Visual Studio se integra con Azure Functions Core Tools; de este modo puede probar las funciones localmente mediante el sistema en tiempo de ejecución completo de Azure Functions.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Después de comprobar que la función se ejecuta correctamente en el equipo local es el momento de publicar el proyecto en Azure.

## <a name="publish-the-project-to-azure"></a>Publicar el proyecto en Azure

Para poder publicar el proyecto, debe tener una aplicación de funciones en la suscripción de Azure. La publicación de Visual Studio crea una aplicación de funciones la primera vez que se publica el proyecto.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>Comprobación de la función en Azure

1. En Cloud Explorer, se debe seleccionar la nueva aplicación de funciones. Si no es así, expanda su suscripción > **App Services** y seleccione la nueva aplicación de funciones.

1. Haga clic con el botón derecho en la aplicación de funciones y elija **Abrir en explorador**. Se abre la raíz de su aplicación de funciones en el explorador web predeterminado y muestra la página que indica que su aplicación de funciones se está ejecutando. 

    ::: zone pivot="programming-runtime-functions-v3"
    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Aplicación de funciones en ejecución":::
    ::: zone-end
    
    ::: zone pivot="programming-runtime-functions-v4"
    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure-v4.png" alt-text="Aplicación de funciones en ejecución":::
    ::: zone-end

1. En la barra de direcciones del explorador, anexe la cadena `/api/HttpExample?name=Functions` a la URL base y ejecute la solicitud.

    La dirección URL que llama a la función de desencadenador HTTP tiene el formato siguiente:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

1. Vaya a esta dirección URL y verá una respuesta en el explorador para la solicitud GET remota devuelta por la función, que es similar a la del siguiente ejemplo:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Respuesta de la función en el explorador":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto trabajar con las siguientes guías de inicio rápido, tutoriales o con cualquiera de los servicios que haya creado en esta guía de inicio rápido, no elimine los recursos.

En Azure, los *recursos* son aplicaciones de función, funciones o cuentas de almacenamiento, entre otros. Se agrupan en *grupos de recursos* y se puede eliminar todo el contenido de un grupo si este se elimina. 

Ha creado recursos para completar estas guías de inicio rápido. Se le pueden facturar por estos recursos, dependiendo del [estado de la cuentade los ](https://azure.microsoft.com/account/) y [precios de los servicios](https://azure.microsoft.com/pricing/). 

[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha usado Visual Studio para crear y publicar una aplicación de funciones en C# en Azure con una función de desencadenador HTTP sencilla. 

Prosiga en el siguiente artículo para aprender a agregar un enlace de cola de Azure Storage a la función:
> [!div class="nextstepaction"]
> [Adición de un enlace de cola de Azure Storage a una función](functions-add-output-binding-storage-queue-vs.md)

