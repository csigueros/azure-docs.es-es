---
title: Creación de una función de Kotlin en Azure Functions mediante IntelliJ
description: Aprenda a usar IntelliJ para crear una función de Kotlin desencadenada por HTTP simple, que luego publique para ejecutarse en un entorno sin servidor en Azure.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: f2ec995334069dc707c8eb09fae3601f58c6c549
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287265"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>Creación de la primera función de Kotlin en Azure mediante IntelliJ

En este artículo se muestra cómo crear una función de Java desencadenada por HTTP en un proyecto de IntelliJ IDEA, cómo ejecutar y depurar el proyecto en el entorno de desarrollo integrado (IDE) y, por último, cómo implementar el proyecto de función en una aplicación de función en Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurado su entorno de desarrollo

Para crear y publicar funciones de Kotlin en Azure con IntelliJ, instale el software siguiente:

- [Kit de desarrolladores de Java](/azure/developer/java/fundamentals/java-support-on-azure) (JDK), versión 8
- [Apache Maven](https://maven.apache.org), versión 3.0 o posterior
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), versión Community o Ultimate con Maven
- [CLI de Azure](/cli/azure)
- [Versión 2.x](functions-run-local.md#v2) de Azure Functions Core Tools. que proporciona un entorno de desarrollo local para escribir, ejecutar y depurar Azure Functions.

> [!IMPORTANT]
> La variable de entorno JAVA_HOME se debe establecer en la ubicación de instalación del JDK para completar los pasos de este artículo.

## <a name="create-a-function-project"></a>Creación de un proyecto de aplicación de una función

1. En IntelliJ IDEA, seleccione **Create New Project** (Crear nuevo proyecto).  
1. En la ventana **New Project** (Nuevo proyecto), seleccione **Maven** en el panel izquierdo.
1. Active la casilla **Create from archetype** (Crear desde arquetipo) y, luego, seleccione **Add Archetype** (Agregar arquetipo) para [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. En la ventana **Add Archetype** (Agregar arquetipo), complete los campos como se indica a continuación:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-archetype
    - _Versión_: use la versión más reciente del [repositorio central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Creación de un proyecto de Maven a partir de arquetipo en IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png).  
1. Seleccione **Aceptar** y después **Siguiente**.
1. Especifique los detalles del proyecto actual y seleccione **Finish** (Finalizar).

Maven crea los archivos del proyecto en una carpeta nueva con el mismo nombre que el valor _ArtifactId_. El código generado en el proyecto es una función [desencadenada por HTTP](./functions-bindings-http-webhook.md) simple que devuelve el cuerpo de la solicitud HTTP desencadenadora.

## <a name="run-project-locally-in-the-ide"></a>Ejecución del proyecto localmente en el IDE

> [!NOTE]
> Para ejecutar y depurar el proyecto localmente, asegúrese de que [Azure Functions Core Tools, versión 2](functions-run-local.md#v2) está instalado.

1. Importe los cambios manualmente o habilite la [importación automática](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Abra la barra de herramientas **Maven Projects** (Proyectos de Maven).
1. Expanda **Lifecycle** (Ciclo de vida) y, a continuación, abra **package** (paquete). La solución se compila y empaqueta en un directorio de destino recién creado.
1. Expanda **Plugins** > **azure-functions** (Complementos > azure-functions) y abra **azure-functions:run** para iniciar el entorno de ejecución local de Azure Functions.  
  ![Barra de herramientas de Maven para Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Cierre el cuadro de diálogo de ejecución cuando termine de probar la función. Solo puede haber un host de función activo y en ejecución en el entorno local a la vez.

## <a name="debug-the-project-in-intellij"></a>Depuración del proyecto en IntelliJ

1. Para iniciar el host de función en modo de depuración, agregue **-DenableDebug** como argumento al ejecutar la función. Puede cambiar la configuración en [maven goals](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) (objetivos de maven) o ejecutar el siguiente comando en una ventana de terminal:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Este comando hace que el host de función abra un puerto de depuración en 5005.

1. En el menú **Run** (Ejecutar), seleccione **Edit Configurations** (Editar configuraciones).
1. Seleccione **(+)** para agregar un elemento **Remote** (Remoto).
1. Complete los campos _Name_ (Nombre) y _Settings_ (Configuración) y, a continuación, seleccione **OK** para guardar la configuración.
1. Después de la configuración, seleccione **Debug < Remote Configuration Name >** (Depurar <Nombre de configuración remota>) o presione MAYÚS + F9 en el teclado para iniciar la depuración.

   ![Depuración del proyecto en IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Cuando termine, detenga el depurador y el proceso en ejecución. Solo puede haber un host de función activo y en ejecución en el entorno local a la vez.

## <a name="deploy-the-project-to-azure"></a>Implementar el proyecto en Azure

1. Antes de implementar el proyecto en una aplicación de funciones en Azure, debe [iniciar sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli).

   ``` azurecli
   az login
   ```

1. Implemente el código en una nueva aplicación de funciones mediante el destino de Maven `azure-functions:deploy`. También puede seleccionar la opción **azure-functions:deploy** en la ventana de proyectos de Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Después de que la aplicación de funciones se haya implementado correctamente, busque la dirección URL de la función de desencadenador HTTP en la salida de la CLI de Azure.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha implementado la primera aplicación de funciones de Kotlin en Azure, revise la [guía para desarrolladores de Java de Azure Functions](functions-reference-java.md) para conocer más sobre el desarrollo de funciones de Java y Kotlin.
- Agregue aplicaciones de funciones adicionales con distintos desencadenadores al proyecto mediante el destino de Maven `azure-functions:add`.
