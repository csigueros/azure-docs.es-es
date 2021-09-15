---
title: 'Desarrollo y depuración de módulos en Visual Studio: Azure IoT Edge'
description: Use Visual Studio con Azure IoT Tools para desarrollar un módulo de IoT Edge en C o C# e insertarlo desde su instancia de IoT Hub en un dispositivo IoT, tal y como se configura en un manifiesto de implementación.
services: iot-edge
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: e7ded6eb8b3e8ee44594e75eb22b920c4e0649b6
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037605"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Uso de Visual Studio 2019 para desarrollar y depurar módulos para Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

En este artículo se muestra cómo usar Visual Studio 2019 para desarrollar y depurar módulos de Azure IoT Edge.

La extensión de Azure IoT Edge Tools para Visual Studio proporcionan las siguientes ventajas:

* Creación, edición, compilación y ejecución de soluciones y módulos de IoT Edge en su equipo de desarrollo local.
* Implementación de la solución de IoT Edge en un dispositivo de IoT Edge mediante Azure IoT Hub.
* Programe los módulos de Azure IoT en C o C# disfrutando de las ventajas de desarrollo en Visual Studio.
* Administración de dispositivos de IoT Edge y módulos con interfaz de usuario.

En este artículo se muestra cómo usar las herramientas de Azure IoT Edge para Visual Studio 2019 con el fin de desarrollar los módulos de IoT Edge. También aprenderá a implementar el proyecto en un dispositivo de IoT Edge. Actualmente, Visual Studio 2019 proporciona compatibilidad con módulos escritos en C y C#. Las arquitecturas de dispositivo compatibles son Windows x64 y Linux x64 o ARM32. Para obtener más información sobre los sistemas operativos, los lenguajes y las arquitecturas compatibles, consulte [Compatibilidad de lenguaje y arquitectura](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Requisitos previos

En este artículo se da por hecho que usa una máquina que ejecuta Windows como máquina de desarrollo. En equipos Windows, puede desarrollar módulos de Windows o Linux.

* Para desarrollar módulos con **contenedores de Windows**, use un equipo Windows que ejecute la versión 1809 y la compilación 17763, o una más reciente.
* Para desarrollar módulos con **contenedores de Linux**, use un equipo Windows que cumpla los [requisitos de Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Instale Visual Studio en la máquina de desarrollo. Asegúrese de incluir las cargas de trabajo de **desarrollo de Azure** y **desarrollo de escritorio con C++** en la instalación de Visual Studio 2019. También puede [modificar Visual Studio 2019](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) para agregar las cargas de trabajo necesarias.

Una vez que Visual Studio 2019 esté listo, también necesitará las siguientes herramientas y componentes:

* Descargue e instale las [herramientas de Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) desde Visual Studio Marketplace para crear un proyecto de IoT Edge en Visual Studio 2019.

  > [!TIP]
  > Si usa Visual Studio 2017, descargue e instale [Azure IoT Edge Tools para VS 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) del marketplace de Visual Studio.

* Descargue e instale [Docker Community Edition](https://docs.docker.com/install/) en su equipo de desarrollo para compilar y ejecutar las imágenes del módulo. Tendrá que configurar Docker CE para que se ejecute en modo contenedor de Linux o de Windows, en función del tipo de módulos que vaya a desarrollar.

* Configure el entorno local de desarrollo para depurar, ejecutar y probar la solución de IoT Edge mediante la instalación de la [herramienta de desarrollo de Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Instale [Python (3.5/3.6/3.7/3.8) y Pip](https://www.python.org/) y, a continuación, el paquete **iotedgehubdev**. Para ello, ejecute el siguiente comando en el terminal. Asegúrese de que la versión de la herramienta de desarrollo de Azure IoT EdgeHub es posterior a la 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

* Instale el administrador de bibliotecas de Vcpkg y, después, instale el **paquete azure-iot-sdk-c** para Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

* Cree una instancia de [Azure Container Registry](../container-registry/index.yml) o [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) para almacenar las imágenes de los módulos.

  > [!TIP]
  > Puede usar un registro de Docker local con fines de prototipo y prueba en lugar de un registro en la nube.

* Para probar el módulo en un dispositivo, necesitará una instancia de IoT Hub con al menos un dispositivo IoT Edge. Para crear rápidamente un dispositivo IoT Edge con fines de prueba, siga los pasos descritos en el inicio rápido para [Linux](quickstart-linux.md) o [Windows](quickstart.md). Si ejecuta el demonio de IoT Edge en su equipo de desarrollo, es posible que deba detener EdgeHub y EdgeAgent para empezar el desarrollo en Visual Studio.

### <a name="check-your-tools-version"></a>Comprobación de la versión de herramientas

1. En el menú **Extensiones**, seleccione **Administrar extensiones**. Expanda **Instalado > Herramientas** y encontrará **Azure IoT Edge Tools para Visual Studio** y **Cloud Explorer para Visual Studio**.

1. Observe la versión instalada. Puede comparar esta versión con la más reciente de Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Si su versión es anterior a la que está disponible en Visual Studio Marketplace, actualice las herramientas en Visual Studio como se muestra en la siguiente sección.

### <a name="update-your-tools"></a>Actualización de las herramientas

1. En la ventana **Administrar extensiones**, expanda **Actualizaciones > Visual Studio Marketplace**, elija **Azure IoT Edge Tools** o **Cloud Explorer para Visual Studio** y seleccione **Actualizar**.

1. Después de descargar la actualización de las herramientas, cierre Visual Studio para desencadenar la actualización de las herramientas mediante el programa de instalación de VSIX.

1. En el instalador, presione **Aceptar** para iniciar y **Modificar** para actualizar las herramientas.

1. Una vez completada la actualización, seleccione **Cerrar** y reinicie Visual Studio.

## <a name="create-an-azure-iot-edge-project"></a>Creación de un proyecto de Azure IoT Edge

La plantilla de proyecto de IoT Edge en Visual Studio crea una solución que se puede implementar en dispositivos IoT Edge. En primer lugar, cree una solución de Azure IoT Edge y, luego, genere el primer módulo en esa solución. Cada solución de IoT Edge puede contener más de un módulo.

> [!TIP]
> La estructura del proyecto de IoT Edge creada por Visual Studio no es igual que en Visual Studio Code.

1. En Visual Studio, cree un nuevo proyecto.

1. En la página **Crear un proyecto**, busque **Azure IoT Edge**. Seleccione el proyecto que coincida con la plataforma y la arquitectura del dispositivo IoT Edge y haga clic en **Siguiente**.

   :::image type="content" source="./media/how-to-visual-studio-develop-module/create-new-project.png" alt-text="Creación de un proyecto":::

1. En la página **Configurar el nuevo proyecto**, escriba un nombre para el proyecto y especifique la ubicación y, luego, seleccione **Crear**.

1. En la ventana **Agregar módulo**, seleccione el tipo de módulo que desea desarrollar. También puede seleccionar **Existing module** (Módulo existente) para agregar un módulo de IoT Edge ya existente a la implementación. Especifique el nombre y el repositorio de imágenes del módulo.

   Visual Studio completa automáticamente la dirección URL del repositorio con **localhost:5000/<NombreDelMódulo\>** . Si usa un registro de Docker local para realizar pruebas, **localhost** puede valer. Si va a usar Azure Container Registry, reemplace **localhost:5000** por el servidor de inicio de sesión de la configuración del registro. El servidor de inicio de sesión se parece a **_\<registry name\>_ .azurecr.io**. El resultado final debería parecerse a **\<*registry name*\>.azurecr.io/ _\<module name\>_**.

   Seleccione **Agregar** para agregar el módulo al proyecto.

   ![Adición de la aplicación y el módulo](./media/how-to-visual-studio-develop-csharp-module/add-module.png)

Ahora tiene un proyecto y un módulo de IoT Edge en la solución Visual Studio.

La carpeta del módulo contiene un archivo para el código del módulo, denominado `program.cs` o `main.c` según el lenguaje que haya elegido. Esta carpeta también contiene un archivo denominado `module.json` que describe los metadatos del módulo. Varios archivos de Docker proporcionan la información necesaria para crear el módulo como un contenedor de Windows o Linux.

La carpeta del proyecto contiene una lista de todos los módulos incluidos en ese proyecto. En este momento debería mostrar solo un módulo, pero puede agregar más. Para más información sobre cómo agregar módulos a un proyecto, consulte la sección [Creación y depuración de varios módulos](#build-and-debug-multiple-modules) que aparece más adelante en este artículo.

La carpeta del proyecto también contiene un archivo denominado `deployment.template.json`. Este archivo es una plantilla de un manifiesto de implementación de IoT Edge, que define todos los módulos que se ejecutarán en un dispositivo y cómo se comunicarán entre sí. Para más información sobre los manifiestos de implementación, consulte [Obtenga información sobre cómo implementar módulos y establecer rutas](module-composition.md). Si abre esta plantilla de implementación, podrá ver que los dos módulos del entorno de ejecución, **edgeAgent** y **edgeHub** están incluidos, junto con el módulo personalizado que creó en este proyecto de Visual Studio. Un cuarto módulo, denominado **SimulatedTemperatureSensor** también está incluido. Este módulo predeterminado es el que genera los datos simulados que puede usar para probar los módulos o eliminar si no es necesario. Para ver cómo funciona el sensor de temperatura simulado, vea el [código fuente de SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

### <a name="set-iot-edge-runtime-version"></a>Establecimiento de la versión del entorno de ejecución de IoT Edge

La extensión IoT Edge adopta como predeterminada la versión estable más reciente del entorno de ejecución de IoT Edge cuando crea los recursos de implementación. Actualmente, esta versión es la 1.2. Si va a desarrollar módulos para dispositivos que ejecutan la versión de compatibilidad a largo plazo 1.1 o la versión 1.0 anterior, actualice la versión del entorno de ejecución de IoT Edge en Visual Studio para que coincidan.

1. En el Explorador de soluciones, haga clic con el botón derecho en el nombre del proyecto y seleccione **Set IoT Edge runtime version** (Establecer la versión del entorno de ejecución de Azure IoT Edge).

   :::image type="content" source="./media/how-to-visual-studio-develop-module/set-iot-edge-runtime-version.png" alt-text="Haga clic con el botón derecho en el nombre del proyecto y seleccione Establecer la versión del entorno de ejecución de Azure IoT Edge.":::

1. Use el menú desplegable para elegir la versión del entorno de ejecución en la que se ejecutan los dispositivos IoT Edge y, a continuación, seleccione **Aceptar** para guardar los cambios.

1. Vuelva a generar el manifiesto de implementación con la nueva versión del entorno de ejecución. Haga clic con el botón derecho en el nombre del proyecto y seleccione **Generar implementación para IoT Edge**.

## <a name="develop-your-module"></a>Desarrollo de su módulo

Al agregar un nuevo módulo, este viene con un código predeterminado que está listo para crearse e implementarse en un dispositivo para que pueda empezar a realizar pruebas sin tener que modificar ningún código. El código del módulo se encuentra en la carpeta del módulo en un archivo denominado `Program.cs` (para C#) o `main.c` (para C).

La solución predeterminada se ha creado para que los datos simulados del módulo **SimulatedTemperatureSensor** se enruten hacia el módulo, el cual recibe la entrada y, a continuación, la envía a IoT Hub.

Cuando tenga todo listo para personalizar la plantilla del módulo con su propio código, use los [SDK de Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) para compilar módulos que aborden las necesidades principales de las soluciones de IoT, como la seguridad, la administración de dispositivos y la fiabilidad.

## <a name="set-up-the-iotedgehubdev-testing-tool"></a>Configuración de la herramienta de pruebas iotedgehubdev

La herramienta de desarrollo de IoT EdgeHub proporciona una experiencia de desarrollo y depuración local. La herramienta ayuda a iniciar los módulos IoT Edge sin el runtime de IoT Edge para que pueda crear, desarrollar, probar, ejecutar y depurar módulos y soluciones de IoT Edge de forma local. Para realizar las pruebas, no es necesario insertar imágenes en un registro de contenedor e implementarlas en un dispositivo.

Para más información, consulte el artículo sobre la [herramienta de desarrollo de Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

Para inicializar la herramienta, proporcione una cadena de conexión del dispositivo IoT Edge desde IoT Hub.

1. Recupere la cadena de conexión de un dispositivo IoT Edge desde Azure Portal, la CLI de Azure o Cloud Explorer para Visual Studio. 

1. Vaya al menú **Herramientas**, seleccione **Azure IoT Edge Tools** > **Setup IoT Edge Simulator** (Configurar el simulador de IoT Edge).

1. Pegue la cadena de conexión y haga clic en **Aceptar**.

> [!NOTE]
> Debe seguir estos pasos solo una vez en el equipo de desarrollo, ya que los resultados se aplican automáticamente a todas las soluciones de Azure IoT Edge posteriores. Puede realizar nuevamente este procedimiento si necesita cambiar a una cadena de conexión diferente.

## <a name="build-and-debug-a-single-module"></a>Compilación y depuración de un solo módulo

Por lo general, querrá probar y depurar los módulos antes de que se ejecuten en una solución entera con varios de ellos.

1. En **Explorador de soluciones**, haga clic con el botón derecho en la carpeta del módulo y seleccione **Establecer como proyecto de inicio** en el menú.

   ![Establecimiento del proyecto de inicio](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Pulse **F5** o haz clic en el botón Ejecutar de la barra de herramientas para ejecutar el módulo. La primera vez que lo haga, puede tardar 10&ndash;20 segundos.

   ![Ejecución del módulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Si el módulo se inicializa correctamente, verá que una aplicación de consola de .NET Core se inicia.

1. Establezca un punto de interrupción para inspeccionar el módulo.

   * Si desarrolla en C#, establezca un punto de interrupción en la función `PipeMessage()` de **Program.cs**.
   * Si usa C, establezca un punto de interrupción en la función `InputQueue1Callback()` de **main.c**.

1. Pruebe el módulo con el envío de un mensaje mediante la ejecución del comando siguiente en un shell de **Git Bash** o **WSL Bash**. (No puede ejecutar el comando `curl` en PowerShell ni desde el símbolo del sistema).

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Depuración de un solo módulo](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

   Debe desencadenarse el punto de interrupción. Puede inspeccionar las variables en la ventana **Variables locales** de Visual Studio.

   > [!TIP]
   > También puede usar [PostMan](https://www.getpostman.com/) u otras herramientas de API para enviar mensajes en lugar de `curl`.

1. Presione **Ctrl + F5** o haga clic en el botón Detener para detener la depuración.

## <a name="build-and-debug-multiple-modules"></a>Creación y depuración de varios módulos

Cuando haya terminado de desarrollar el módulo, querrá ejecutar y depurar una solución completa de varios módulos.

1. En **Explorador de soluciones**, para agregar un segundo módulo a la solución, haga clic con el botón derecho en la carpeta del proyecto. En el menú, seleccione **Agregar** > **New IoT Edge Module** (Nuevo módulo de IoT Edge).

   ![Incorporación de un nuevo módulo a un proyecto de IoT Edge existente](./media/how-to-visual-studio-develop-csharp-module/add-new-module.png)

1. Abra el archivo `deployment.template.json` y verá que el nuevo módulo se ha agregado a la sección **modules**. También se ha agregado una nueva ruta a la sección **routes** para enviar mensajes desde el nuevo módulo a IoT Hub. Si desea enviar datos desde el sensor de temperatura simulado al nuevo módulo, agregue otra ruta como la del ejemplo siguiente: 

    ```json
   "sensorTo<NewModuleName>&quot;: &quot;FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/<NewModuleName>/inputs/input1\")"
    ```

1. Haga clic con el botón derecho en la carpeta del proyecto y seleccione **Establecer como proyecto de inicio** en el menú contextual.

1. Cree los puntos de interrupción y presione **F5** para poder ejecutar y depurar varios módulos a la vez. Verá varias ventanas de aplicación de consola de .NET Core; cada una de ellas representa un módulo distinto.

   ![Depuración de varios módulos](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Presione **Ctrl + F5** o seleccione el botón Detener para detener la depuración.

## <a name="build-and-push-images"></a>Compilación e inserción de imágenes

1. Asegúrese de que el proyecto de IoT Edge es el proyecto de inicio, no uno de los módulos individuales. Seleccione **Depurar** o **Versión** como configuración para compilar las imágenes del módulo.

    > [!NOTE]
    > Al elegir **Depurar**, Visual Studio usará `Dockerfile.(amd64|windows-amd64).debug` para compilar las imágenes de Docker. Esto incluye el depurador de la línea de comandos de .NET Core VSDBG en la imagen de contenedor al realizar la compilación. Para los módulos de IoT Edge listos para producción, se recomienda usar la configuración de **Versión** que usa `Dockerfile.(amd64|windows-amd64)` sin VSDBG.

1. Si usa un registro privado como Azure Container Registry (ACR), use el siguiente comando de Docker para iniciar sesión en él.  Puede obtener el nombre de usuario y la contraseña en la página **Claves de acceso** del registro en Azure Portal. Si usa un registro local, puede [Run a local registry](https://docs.docker.com/registry/deploying/#run-a-local-registry) (Ejecutar un registro local).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Si usa un registro privado como Azure Container Registry, deberá agregar la información de inicio de sesión del registro a la configuración en tiempo de ejecución que se encuentra en el archivo `deployment.template.json`. Reemplace los marcadores de posición por su nombre de usuario administrador, contraseña y nombre de registro reales de ACR.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

   >[!NOTE]
   >En este artículo se usan credenciales de inicio de sesión de administrador de Azure Container Registry, que son prácticas para escenarios de desarrollo y pruebas. Cuando esté listo para escenarios de producción, se recomienda una opción de autenticación con privilegios mínimos, como las entidades de servicio. Para más información, consulte [Administración del acceso al registro de contenedor](production-checklist.md#manage-access-to-your-container-registry).

1. En **Explorador de soluciones**, haga clic con el botón derecho en la carpeta del proyecto y seleccione **Compilación e inserción de módulos IoT Edge** para compilar e insertar las imágenes de Docker para cada módulo.

## <a name="deploy-the-solution"></a>Implementación de la solución

En el artículo de la guía de inicio rápido que siguió para configurar el dispositivo de IoT Edge, implementó un módulo mediante Azure Portal. También puede implementar módulos mediante Cloud Explorer para Visual Studio. Ya tiene un manifiesto de implementación listo para su escenario (el archivo `deployment.json`) y todo lo que necesita hacer es seleccionar el dispositivo que recibirá la implementación.

1. Abra **Cloud Explorer** al hacer clic en **Ver** > **Cloud Explorer**. Asegúrese de haber iniciado sesión en Visual Studio 2019.

1. En **Cloud Explorer**, expanda su suscripción, busque Azure IoT Hub y el dispositivo de Azure IoT Edge que desea implementar.

1. Haga clic con el botón derecho en el dispositivo IoT Edge para crear una implementación para este. Vaya al manifiesto de implementación configurado para la plataforma, el cual se encuentra en la carpeta **config** de la solución de Visual Studio, por ejemplo, `deployment.arm32v7.json`.

1. Haga clic en el botón Actualizar para ver los nuevos módulos en ejecución junto con el módulo **SimulatedTemperatureSensor**, **$edgeAgent** y **$edgeHub**.

## <a name="view-generated-data"></a>Visualización de datos generados

1. Para supervisar el mensaje de D2C de un determinado dispositivo IoT Edge, selecciónelo en el centro de IoT en **Cloud Explorer** y, luego, haga clic en **Iniciar la supervisión del punto de conexión de eventos integrado** en la ventana **Acción**.

1. Para detener la supervisión de datos, seleccione **Detener la supervisión del punto de conexión de eventos integrado** en la ventana **Acción**.

## <a name="next-steps"></a>Pasos siguientes

Para desarrollar módulos personalizados para los dispositivos IoT Edge, consulte [Información y uso de los SDK de Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
