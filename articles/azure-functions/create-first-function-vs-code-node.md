---
title: 'Creación de una función de JavaScript con Visual Studio Code: Azure Functions'
description: Obtenga información sobre cómo crear una función de JavaScript y, a continuación, publicar el proyecto de Node.js local en el hospedaje sin servidor de Azure Functions con la extensión de Azure Functions en Visual Studio Code.
ms.topic: quickstart
ms.date: 07/01/2021
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-node_uiex
ms.openlocfilehash: 09c028d71d7cef4b83220a7c93a24c6bc3c256d4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732572"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>Inicio rápido: Creación de una función de JavaScript con Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Use Visual Studio Code para crear una función de JavaScript que responda a solicitudes HTTP. Pruebe el código localmente y, a continuación, impleméntelo en el entorno sin servidor de Azure Functions.

Este inicio rápido supone un pequeño costo en su cuenta de Azure.

También hay una [versión basada en la CLI](create-first-function-cli-node.md) de este artículo.

## <a name="configure-your-environment"></a>Configurar su entorno

Antes de empezar, asegúrese de que cumple los siguientes requisitos:

+ Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js 10.14.1+](https://nodejs.org/). Use el comando `node --version` para comprobar la versión.  

+ [Visual Studio Code](https://code.visualstudio.com/) en una de las [plataformas admitidas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ La [extensión de Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code.

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Creación del proyecto local

En esta sección se usa Visual Studio Code para crear un proyecto local de Azure Functions en JavaScript. Más adelante en este artículo, publicará el código de función en Azure.

1. Seleccione el icono de Azure en la barra de actividades y después en el área **Azure: Functions**, seleccione el icono **Crear un proyecto**.

    ![Elija Crear un proyecto.](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Elija una ubicación de directorio para el área de trabajo del proyecto y elija **Seleccionar**.

    > [!NOTE]
    > Estos pasos se han diseñado para completarse fuera de un área de trabajo. Por tanto, no seleccione una carpeta de proyecto que forme parte de un área de trabajo.

1. Escriba la siguiente información cuando se le indique:

    |Prompt|Número de selección|
    |--|--|
    |**Seleccionar un lenguaje para el proyecto de funciones**|Elija `JavaScript`.|
    |**Seleccionar una plantilla para la primera función de su proyecto**|Elija `HTTP trigger`.|
    |**Proporcionar un nombre de función**|Escriba `HttpExample`.|
    |**Nivel de autorización**|Elija `Anonymous`, que permite que cualquier llame al punto de conexión de la función. Para obtener información sobre el nivel de autorización, consulte [Claves de autorización](functions-bindings-http-webhook-trigger.md#authorization-keys).|
    |**Seleccionar cómo desea que se abra el proyecto**|Elija `Add to workspace`.|

    Con esta información, Visual Studio Code genera un proyecto de Azure Functions con un desencadenador HTTP. Los archivos del proyecto locales se pueden ver en Explorer. Para obtener más información sobre los archivos que se crean, consulte [Archivos del proyecto generados](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Después de comprobar que la función se ejecuta correctamente en el equipo local, es el momento de usar Visual Studio Code para publicar el proyecto directamente en Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

<a name="Publish the project to Azure"></a>

## <a name="deploy-the-project-to-azure"></a>Implementar el proyecto en Azure

En esta sección, va a crear una aplicación de funciones y los recursos relacionados en su suscripción de Azure y, después, va a implementar el código. 

> [!IMPORTANT]
> La implementación en una aplicación de funciones existente sobrescribe el contenido de esa aplicación en Azure. 


1. Seleccione el icono de Azure en la barra de actividades y después en el área **Azure: Functions**, seleccione el botón de **implementación en la aplicación de funciones**.

    ![Publicación del proyecto en Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Escriba la siguiente información cuando se le indique:

    |Prompt| Número de selección|
    |--|--|
    |**Seleccionar la aplicación de funciones en Azure**|Elija `+ Create new Function App`. (No elija la opción `Advanced`, que no se trata en este artículo).|
    |**Escribir un nombre único global para la aplicación de funciones**|Escriba un nombre que sea válido en una ruta de acceso de la dirección URL. El nombre que escriba se valida para asegurarse de que es único en Azure Functions.|
    |**Seleccionar un entorno de ejecución**|Elija la versión de Node.js en la que se ha estado ejecutando localmente. Ejecute el comando `node --version` para comprobar la versión.|
    |**Seleccionar una ubicación para los nuevos recursos**|Para mejorar el rendimiento, elija una [región](https://azure.microsoft.com/regions/) cerca de usted.|

    La extensión muestra el estado de los recursos individuales a medida que se crean en Azure en el área de notificación.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notificación de creación de recursos de Azure":::

    Cuando se complete, se crearán los siguientes recursos de Azure en la suscripción con nombres que se basan en el nombre de la aplicación de funciones:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

1. Una vez que se haya creado la aplicación de función se mostrará una notificación y se aplicará el paquete de implementación. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

1. Seleccione **View Output** (Ver salida) en esta notificación para ver la creación y los resultados de la implementación, incluidos los recursos de Azure que ha creado. Si se pierde la notificación, seleccione el icono de campana en la esquina inferior derecha para verlo de nuevo.

    ![Creación de la notificación completa](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

## <a name="change-the-code-and-redeploy-to-azure"></a>Cambio del código y nueva implementación en Azure

1. En la vista Explorador de VSCode, seleccione el archivo `./HttpExample/index.js`. 
1. Reemplace el archivo por el código siguiente para construir un objeto JSON y devolverlo.

    ```javascript
    module.exports = async function (context, req) {
        
        try {
            context.log('JavaScript HTTP trigger function processed a request.');
    
            // Read incoming data
            const name = req.query.name;
            const sport = req.query.sport;
        
            // fail if incoming data is required
            if (!name || !sport) {
    
                context.res = {
                    status: 400
                };
                return;
            }
            
            // Add or change code here
            const message = `${name} likes ${sport}`;
        
            // Construct response
            const responseJSON = {
                "name": name,
                "sport": sport,
                "message": message,
                "success": true
            }
    
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: responseJSON,
                contentType: 'application/json'
            };
        } catch(err) {
            context.res = {
                status: 500
            };
        }
    }
    ```
1. [Vuelva a ejecutar la aplicación de funciones](#run-the-function-locally) de forma local.
1. En el mensaje **Escribir el cuerpo de la solicitud**, cambie el cuerpo del mensaje de la solicitud a { "name": "Tom","sport":"basketball" }. Presione Entrar para enviar este mensaje de solicitud a la función.
1. Observe la respuesta en la notificación:

    ```json
    {
      "name": "Tom",
      "sport": "basketball",
      "message": "Tom likes basketball",
      "success": true
    }
    ```

1. [Vuelva a implementar la función](#deploy-the-project-to-azure) en Azure.

## <a name="troubleshooting"></a>Solución de problemas

Use la tabla siguiente para resolver los problemas más comunes que se encuentran al usar este inicio rápido.

|Problema|Solución|
|--|--|
|¿No puede crear un proyecto de función local?|Asegúrese de tener instalada la [extensión de Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).|
|¿No puede ejecutar la función localmente?|Asegúrese de tener instalado [Azure Functions Core Tools](functions-run-local.md?tabs=windows%2Ccsharp%2Cbash). <br/>En la ejecución en Windows, asegúrese de que el shell del terminal predeterminado de Visual Studio Code no se haya establecido en WSL Bash.|
|¿No puede implementar la función en Azure?|Revise la salida para obtener información sobre el error. El icono de campana que aparece en la esquina inferior derecha también permite ver la salida. ¿Ha realizado la publicación en una aplicación de funciones existente? Esa acción sobrescribe el contenido de la aplicación en Azure.|
|¿No se pudo ejecutar la aplicación de funciones basada en la nube?|No olvide usar la cadena de consulta para enviar parámetros.|

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code-extension.md)]

## <a name="next-steps"></a>Pasos siguientes

Ha usado [Visual Studio Code](functions-develop-vs-code.md?tabs=javascript) para crear una aplicación de función con una función simple desencadenada por HTTP. En el siguiente artículo, va a expandir esa función mediante la conexión a Azure Cosmos DB o Azure Storage. Para más información sobre cómo conectarse a otros servicios de Azure, consulte [Incorporación de enlaces a una función existente de Azure Functions](add-bindings-existing-function.md?tabs=javascript).  

> [!div class="nextstepaction"]
> [Conexión a una base de datos](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-javascript)
> [!div class="nextstepaction"]
> [Conexión de Azure Functions a Azure Storage mediante Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)
> [Protección de Azure Functions](security-concepts.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
