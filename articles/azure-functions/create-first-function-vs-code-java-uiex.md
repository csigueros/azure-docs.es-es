---
title: 'Creación de una función de Java con Visual Studio Code: Azure Functions'
description: Obtenga información sobre cómo crear una función de Java y, a continuación, publicar el proyecto local en el hospedaje sin servidor de Azure Functions con la extensión de Azure Functions en Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 27268e53f86dd5cc25baac11a11f903f0d4c47eb
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287762"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Inicio rápido: Creación de una función de Java con Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Utilice Visual Studio Code para crear una función de Java que responda a solicitudes HTTP. Pruebe el código localmente y, a continuación, impleméntelo en el entorno sin servidor de Azure Functions.

Este inicio rápido supone un pequeño costo en su <abbr title="El perfil que mantiene la información de facturación en relación con el uso de Azure.">Cuenta de Azure</abbr>.

Si Visual Studio Code no es su herramienta de desarrollo preferida, consulte nuestros tutoriales análogos para desarrolladores de Java con [Maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) e [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="1-prepare-your-environment"></a>1. Preparación del entorno

Antes de empezar, asegúrese de que cumple los siguientes requisitos:

+ Una cuenta de Azure con una suscripción activa. <abbr title="La estructura organizativa básica en la que administra los recursos de Azure, que normalmente se asocia a un individuo o departamento dentro de una organización.">subscription</abbr>. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Kit para desarrolladores de Java](/azure/developer/java/fundamentals/java-support-on-azure), versión 8 u 11.

+ [Apache Maven](https://maven.apache.org), versión 3.0 o posterior.

+ [Visual Studio Code](https://code.visualstudio.com/) en una de las [plataformas admitidas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Paquete de la extensión de Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ La [extensión de Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code.

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a>Creación de un proyecto local de Functions

1. Seleccione el icono de Azure en la **barra de actividades** y, en el área **Azure: Functions**, seleccione el icono **Crear proyecto...**

    ![Elija Crear un proyecto.](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Elija una ubicación de directorio** para el área de trabajo del proyecto y, a continuación, **Seleccionar**.

1. Escriba la siguiente información cuando se le indique:

    + **Seleccione un lenguaje para el proyecto de funciones**: Elija `Java`.

    + **Seleccione una versión de Java**: Elija `Java 8` o `Java 11`, la versión de Java en la que se ejecutan las funciones en Azure. Elija una versión de Java que haya comprobado localmente.

    + **Proporcione un identificador de grupo**: Elija `com.function`.

    + **Proporcione un identificador de artefacto**: Elija `myFunction`.

    + **Proporcione una versión**: Elija `1.0-SNAPSHOT`.

    + **Proporcione un nombre de paquete**: Elija `com.function`.

    + **Proporcione un nombre de aplicación**: Elija `myFunction-12345`.

    + **Nivel de autorización**: Elija `Anonymous`, que permite que cualquier llame al punto de conexión de la función.

    + **Seleccione cómo desea que se abra el proyecto**: Elija `Add to workspace`.

<br/>

<details>
<summary><strong>¿No puede crear un proyecto de aplicación de funciones?</strong></summary>

Entre los problemas más comunes que se pueden resolver al crear un proyecto local de aplicación de funciones, se incluyen los siguientes:
* No tener instalada la extensión de Azure Functions. 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Ejecución local de la función

1. Presione <kbd>F5</kbd> para iniciar el proyecto de aplicación de funciones.

1. En el panel **Terminal**, observe el punto de conexión de la dirección URL de la función que se ejecuta localmente.

    ![Salida de la función local de VS Code](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Mientras se ejecuta Core Tools, vaya al área de **Azure: Área Functions**. En **Functions**, expanda **Proyecto local** > **Functions**. Haga clic con el botón derecho (Windows) o <kbd>Ctrl</kbd> + clic en (macOS) en la función `HttpExample` y elija **Execute Function Now...** (Ejecutar la función ahora...).

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Ejecutar la función ahora desde Visual Studio Code":::

1. En **Enter request body** (Especificar el cuerpo de la solicitud) verá el valor del cuerpo del mensaje de solicitud de `{ "name": "Azure" }`. Presione <kbd>Entrar</kbd> para enviar este mensaje de solicitud a la función.  

1. Cuando la función se ejecuta localmente y devuelve una respuesta, se genera una notificación en Visual Studio Code. La información sobre la ejecución de la función se muestra en el panel **Terminal**.

1. Presione <kbd>Ctrl + C</kbd> para detener Core Tools y desconectar el depurador.

<br/>

<details>
<summary><strong>¿No puede ejecutar la función localmente?</strong></summary>

Entre los problemas más comunes que se pueden resolver al ejecutar un proyecto local de Functions, se incluyen los siguientes:
* No tener instalado el conjunto de herramientas Core Tools. 
*  Si tiene problemas de ejecución en Windows, asegúrese de que el shell de terminal predeterminado para Visual Studio Code no se haya establecido en WSL Bash. 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4. Inicio de sesión en Azure

Para publicar la aplicación, inicie sesión en Azure. Si ya ha iniciado sesión, vaya a la sección siguiente.

1. Seleccione el icono de Azure en la barra de actividades y, a continuación, en el área **Azure: Functions**, seleccione **Iniciar sesión en Azure...** .

    ![Inicio de sesión en Azure en VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Cuando se le solicite en el explorador, **elija su cuenta de Azure** e **inicie sesión** con sus credenciales.

1. Cuando haya iniciado sesión correctamente, cierre la nueva ventana del explorador y vuelva a Visual Studio Code.

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5. Publicar el proyecto en Azure

La primera implementación de código incluye crear un recurso de función en la suscripción de Azure.

1. Seleccione el icono de Azure en la barra de actividades y después en el área **Azure: Functions**, seleccione el botón de **implementación en la aplicación de funciones**.

    ![Publicación del proyecto en Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Escriba la siguiente información cuando se le indique:

    + **Select folder** (Seleccionar carpeta): Elija la carpeta que contiene la aplicación de funciones. 

    + **Seleccione la suscripción**: elija la suscripción que desee usar. No se mostrará esta opción si solo tiene una suscripción.

    + **Seleccione la aplicación de funciones en Azure**: Elija `Create new Function App`.

    + **Especifique un nombre único global para la aplicación de funciones**: escriba un nombre que sea único en Azure en una ruta de acceso URL. El nombre que escriba se valida para garantizar que sea único de forma global.

    - **Seleccione una ubicación para los nuevos recursos**:  Para mejorar el rendimiento, elija una [región](https://azure.microsoft.com/regions/) cerca de usted.

1. Una vez que se haya creado la aplicación de función se mostrará una notificación y se aplicará el paquete de implementación. Seleccione **View Output** (Ver salida) para ver los resultados de la creación y la implementación.

    ![Creación de la notificación completa](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

<br/>

<details>
<summary><strong>¿No puede publicar la función?</strong></summary>

En esta sección se han creado los recursos de Azure y se ha implementado el código local en la aplicación de funciones. Si las acciones no se realizaron correctamente, haga lo siguiente:

* Revise la salida para obtener información sobre el error. El icono de campana que aparece en la esquina inferior derecha también permite ver la salida. 
* ¿Ha realizado la publicación en una aplicación de funciones existente? Esa acción sobrescribe el contenido de la aplicación en Azure.
</details>

<br/>

<details>
<summary><strong>¿Qué recursos se han creado?</strong></summary>

Cuando se complete, se crearán los siguientes recursos de Azure en la suscripción con nombres que se basan en el nombre de la aplicación de funciones:

* **Grupo de recursos**: un grupo de recursos es un contenedor lógico para recursos relacionados de la misma región.
* **Cuenta de Azure Storage**: un recurso de almacenamiento mantiene información de estado y de otro tipo sobre su proyecto.
* **Plan de consumo**: Un plan de consumo define el host subyacente para la aplicación de funciones sin servidor.
* **Aplicación de funciones**: una aplicación de funciones proporciona el entorno para ejecutar el código de la función y las funciones de grupo como una unidad lógica.
* **Application Insights**: Application Insights hace un seguimiento del uso de la función sin servidor.

</details>

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6. Ejecución de la función en Azure

1. Vuelva al área **Azure: Functions** en la barra lateral y expanda su suscripción, la nueva aplicación de funciones y **Functions**. Haga clic con el botón derecho (Windows) o <kbd>Ctrl</kbd> + clic en (macOS) en la función `HttpExample` y elija **Execute Function Now...** (Ejecutar la función ahora...).

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Ejecutar la función ahora en Azure desde Visual Studio Code":::

1. En **Enter request body** (Especificar el cuerpo de la solicitud) verá el valor del cuerpo del mensaje de solicitud de `{ "name": "Azure" }`. Presione Entrar para enviar este mensaje de solicitud a la función.  

1. Cuando la función se ejecuta en Azure y devuelve una respuesta, se genera una notificación en Visual Studio Code.

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7. Limpieza de recursos

Si no tiene previsto continuar con el [paso siguiente](#next-steps), elimine la aplicación de funciones y sus recursos para no incurrir en costos adicionales.

1. En Visual Studio Code, seleccione el icono de Azure en la barra de actividades y, a continuación, el área Functions en la barra lateral.
1. Seleccione la aplicación de funciones, haga clic con el botón derecho y seleccione **Delete Function app...** (Eliminar aplicación de funciones...).

<br/>
<hr/>

## <a name="next-steps"></a>Pasos siguientes

Expanda la función mediante la incorporación de un <abbr title="En Azure Storage, una forma de asociar una función a una cola de almacenamiento, de tal manera que pueda crear mensajes en la cola.">enlace de salida</abbr>. Este enlace escribe la cadena de la solicitud HTTP en un mensaje en una cola de Azure Queue Storage.

> [!div class="nextstepaction"]
> [Conexión a una cola de Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
