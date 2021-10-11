---
title: 'Inicio rápido: Aprovisionamiento de un dispositivo de clave simétrica simulada para Microsoft Azure IoT Hub'
description: Aprenda a aprovisionar un dispositivo que se autentica con una clave simétrica en Azure IoT Hub Device Provisioning Service (DPS)
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: 43c6dc8e9d8a6438468c44fd2b8cc31d04a92c2f
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276970"
---
# <a name="quickstart-provision-a-simulated-symmetric-key-device"></a>Inicio rápido: Aprovisionamiento de un dispositivo simulado con clave simétrica

En este inicio rápido creará un dispositivo simulado en una máquina Windows. El dispositivo simulado se configurará para usar el mecanismo de [atestación de clave simétrica](concepts-symmetric-key-attestation.md) para la autenticación. Después de configurar el dispositivo, lo aprovisionará en un centro de IoT mediante Azure IoT Hub Device Provisioning Service.

Si no conoce el proceso de aprovisionamiento, consulte la información general sobre el [aprovisionamiento](about-iot-dps.md#provisioning-process).

En este inicio rápido se muestra una solución para una estación de trabajo basada en Windows. No obstante, también puede realizar los procedimientos en Linux. Para obtener un ejemplo de Linux, consulte [Cómo aprovisionar para varios inquilinos](how-to-provision-multitenant.md).

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

* Realice los pasos que se describen en [Configuración de IoT Hub Device Provisioning Service con Azure Portal](./quick-setup-auto-provision.md).
::: zone pivot="programming-language-ansi-c"

* Si utiliza un entorno de desarrollo de Windows, instale [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 con la carga de trabajo [Desarrollo para el escritorio con C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitada. También se admiten Visual Studio 2015 y Visual Studio 2017. En el caso de Linux o macOS, consulte la sección correspondiente en [Preparación del entorno de desarrollo](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) en la documentación del SDK.

::: zone-end

::: zone pivot="programming-language-csharp"

* Instale el [SDK de .NET Core 2.1](https://dotnet.microsoft.com/download), o cualquier versión posterior, en una máquina con Windows. Para comprobar la versión, use el siguiente comando.

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* Instale [Node.js v4.0+](https://nodejs.org).

::: zone-end

::: zone pivot="programming-language-python"

* Instale [Python 3.7](https://www.python.org/downloads/), o cualquier versión posterior, en una máquina con Windows. Puede comprobar la versión de Python ejecutando `python --version`.

::: zone-end

::: zone pivot="programming-language-java"

* Instale [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure), o cualquier versión posterior, en su máquina.

* Descargue e instale [Maven](https://maven.apache.org/install.html).

::: zone-end

* Instale la última versión de [Git](https://git-scm.com/download/). Asegúrese de que Git se ha agregado a las variables de entorno accesibles desde la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente de las herramientas `git`, lo que incluye *Git Bash*, la aplicación de línea de comandos que puede usar para interactuar con su repositorio de Git local.


<a id="setupdevbox"></a>

## <a name="prepare-your-development-environment"></a>Preparación del entorno de desarrollo

::: zone pivot="programming-language-ansi-c"

En esta sección, preparará un entorno de desarrollo que se usa para compilar el [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c). El código de ejemplo intenta aprovisionar el dispositivo durante la secuencia de arranque del mismo.

1. Descargue el [sistema de compilación de CMake](https://cmake.org/download/) más reciente.

    >[!IMPORTANT]
    >Confirme que los requisitos previos de Visual Studio (Visual Studio y la carga de trabajo "Desarrollo para escritorio con C++") estén instalados en la máquina **antes** de empezar la instalación de `CMake`. Una vez que los requisitos previos están en su lugar, y se ha comprobado la descarga, instale el sistema de compilación de CMake. Tenga en cuenta que las versiones anteriores del sistema de compilación de CMake no generan el archivo de solución que se usa en este artículo. Asegúrese de usar la versión más reciente de CMake.

2. Abra un explorador web y vaya a la [página de la versión del SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

3. Seleccione la pestaña **Tags** (Etiquetas) en la parte superior de la página.

4. Copie el nombre de etiqueta de la versión más reciente del SDK de Azure IoT para C.

5. Abra un símbolo del sistema o el shell de Bash de Git. Ejecute los siguientes comandos para clonar la versión más reciente del repositorio de GitHub del [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c) (reemplace `<release-tag>` por la etiqueta que copió en el paso anterior).

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operación puede tardar varios minutos en completarse.

6. Una vez completada la operación, ejecute los siguientes comandos desde el directorio `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

7. El ejemplo de código usa una clave simétrica para proporcionar atestación. Ejecute el siguiente comando para crear una versión del SDK específica para su plataforma del cliente de desarrollo que incluya el cliente de aprovisionamiento de dispositivos:

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    >[!TIP]
    >Si `cmake` no encuentra el compilador de C++, es posible que aparezcan errores de compilación al ejecutar el comando anterior. Si eso sucede, pruebe a ejecutar este comando en el [símbolo del sistema de Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

8. Cuando la compilación se realiza correctamente, las últimas líneas de salida tendrán un aspecto similar al siguiente:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 16 2019
    -- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19042.
    -- The C compiler identification is MSVC 19.29.30040.0
    -- The CXX compiler identification is MSVC 19.29.30040.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. Abra un entorno de línea de comandos de Git CMD o Git Bash.

2. Utilice el siguiente comando para clonar el repositorio de GitHub de los [ejemplos de Azure IoT para C#](https://github.com/Azure-Samples/azure-iot-samples-csharp):

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Abra un entorno de línea de comandos de Git CMD o Git Bash.

2. Utilice el siguiente comando para clonar el repositorio de GitHub del [SDK de Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Abra un entorno de línea de comandos de Git CMD o Git Bash.

2. Utilice el siguiente comando para clonar el repositorio de GitHub del [SDK de Azure IoT para Python](https://github.com/Azure/azure-iot-sdk-python.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

3. Vaya al directorio `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` en que se encuentra el archivo de ejemplo _provision_symmetric_key.py_.

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. Instale la biblioteca _azure-iot-device_ ejecutando el siguiente comando.

    ```console
    pip install azure-iot-device
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Abra un entorno de línea de comandos de Git CMD o Git Bash.

2. Utilice el siguiente comando para clonar el repositorio de GitHub del [SDK de Azure IoT para Java](https://github.com/Azure/azure-iot-sdk-java.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. Vaya al directorio `azure-iot-sdk-java` raíz y compile el proyecto para descargar todos los paquetes necesarios.

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. Instale la biblioteca _azure-iot-device_ ejecutando el siguiente comando.

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

::: zone-end

## <a name="create-a-device-enrollment"></a>Creación de una inscripción de dispositivos

Azure IoT Hub Device Provisioning Service admite dos tipos de inscripciones:

* [Grupos de inscripción](concepts-service.md#enrollment-group): usados para inscribir varios dispositivos relacionados.
* [Inscripciones individuales](concepts-service.md#individual-enrollment): usadas para inscribir un solo dispositivo.

En este artículo se muestra una inscripción de un dispositivo que se va a aprovisionar con un centro de IoT.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service.

4. En el menú **Configuración**, seleccione **Administrar inscripciones**.

5. En la parte superior de la página, seleccione **+ Agregar inscripción individual**.

6. En la página **Agregar inscripción**, escriba la siguiente información.

   * **Mecanismo:** seleccione *Clave simétrica* como mecanismo de atestación de identidad.

   * **Generar claves automáticamente**: marque esta casilla.

   * **Identificador de registro**: escriba un identificador de registro para identificar la inscripción. Use únicamente caracteres alfanuméricos en minúsculas y guiones (“-”). Por ejemplo, *symm-key-device-007*.

   * **Id. de dispositivo de IoT Hub:** escriba un identificador de dispositivo.

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png" alt-text="Especificar la información de inscripción del dispositivo.":::

    ::: zone-end

    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-csharp.png" alt-text="Especificar la información de inscripción del dispositivo en C#.":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-nodejs.png" alt-text="Especificar la información de inscripción del dispositivo en Node.js.":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-python.png" alt-text="Especificar la información de inscripción del dispositivo en Python.":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-java.png" alt-text="Especificar la información de inscripción del dispositivo en Java.":::

    ::: zone-end

7. Seleccione **Guardar**. Se generan una **clave principal** y una **clave secundaria**, y se agregarán a la entrada de la inscripción, y volverá a la página **Administrar inscripciones**.

8. Para ver la inscripción del dispositivo de clave simétrica simulada, seleccione la pestaña **Inscripciones individuales**.

9. Seleccione el dispositivo (*symm-key-device-007*).

10. Copie el valor de la **clave principal** generada.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/copy-device-enrollment-primary-key.png" alt-text="Copia de la clave principal de la inscripción del dispositivo":::

<a id="firstbootsequence"></a>

## <a name="prepare-and-run-the-device-provisioning-code"></a>Preparación y ejecución del código de aprovisionamiento de dispositivos

::: zone pivot="programming-language-ansi-c"

En esta sección, actualizará el código de ejemplo del dispositivo para enviar la secuencia de arranque del dispositivo a la instancia de Device Provisioning Service. Esta secuencia de arranque hará que el dispositivo se reconozca, se autentique y se asigne a un centro de IoT vinculado a la instancia de Device Provisioning Service.

El código de aprovisionamiento de ejemplo realiza las siguientes tareas, en orden:

1. Autentica el dispositivo en el recurso de Device Provisioning, para lo que usa los tres parámetros siguientes:

    * El ámbito de identificador de Device Provisioning Service
    * El identificador de registro de la inscripción del dispositivo.
    * Clave simétrica principal para la inscripción del dispositivo.

2. Asigna el dispositivo al centro de IoT ya vinculado a la instancia de Device Provisioning Service.

Para actualizar y ejecutar el ejemplo de aprovisionamiento con la información del dispositivo:

1. En el menú principal de Device Provisioning Service, seleccione **Información general**.

2. Copie el valor de **Ámbito de id.** .

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Extracción de información del punto de conexión de Device Provisioning Service":::

3. En Visual Studio, abra el archivo de solución *azure_iot_sdks.sln* que se ha generado al ejecutar CMake. El archivo de solución debe estar en la siguiente ubicación:

    ```output

    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln

    ```

    >[!TIP]
    >Si el archivo no se ha generado en el directorio cmake, asegúrese de que usó una versión reciente del sistema de compilación de CMake.

4. En la ventana *Explorador de soluciones* de Visual Studio, vaya a la carpeta **Provision\_Samples**. Expanda el proyecto de ejemplo denominado **prov\_dev\_client\_sample**. Expanda **Archivos de código fuente** y abra **prov\_dev\_cliente\_sample.c**.

5. Busque la constante `id_scope` y reemplace el valor por el valor de **Ámbito de id.** que copió anteriormente.

    ```c
    static const char* id_scope = "0ne00002193";
    ```

6. Busque la definición de la función `main()` en el mismo archivo. Asegúrese de que la variable `hsm_type` está establecida en `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, tal como se muestra a continuación:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

7. Busque la llamada a `prov_dev_set_symmetric_key_info()` en **prov\_dev\_client\_sample.c** a la que se ha quitado los comentarios.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Quite la marca de comentario de la llamada de función y reemplace los valores de marcador de posición (incluidos los corchetes angulares) por el identificador de registro y el valor de clave principal.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```

8. Guarde el archivo.

9. Haga clic con el botón derecho en el proyecto **prov\_dev\_client\_sample** y seleccione **Set as Startup Project** (Establecer como proyecto de inicio).

10. En el menú de Visual Studio, seleccione **Depurar** > **Iniciar sin depurar** para ejecutar la solución. En el indicador para recompilar el proyecto, seleccione **Yes** (Sí) para recompilar el proyecto antes de ejecutarlo.

    La salida siguiente es un ejemplo de conexión correcta del dispositivo a la instancia del servicio de aprovisionamiento para que se asigne a IoT Hub:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

El código de aprovisionamiento de ejemplo realiza las siguientes tareas:

1. Autentica el dispositivo en el recurso de Device Provisioning, para lo que usa los tres parámetros siguientes:

    * El ámbito de identificador de Device Provisioning Service
    * El identificador de registro de la inscripción del dispositivo.
    * Clave simétrica principal para la inscripción del dispositivo.

2. Asigna el dispositivo al centro de IoT ya vinculado a la instancia de Device Provisioning Service.

3. Envía un mensaje de telemetría de prueba al centro de IoT.

Para actualizar y ejecutar el ejemplo de aprovisionamiento con la información del dispositivo:

1. En el menú principal de Device Provisioning Service, seleccione **Información general**.

2. Copie el valor de **Ámbito de id.** .

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Extracción de información del punto de conexión de Device Provisioning Service":::

3. Abra un símbolo del sistema y vaya a *SymmetricKeySample* en el repositorio de ejemplos clonados:

    ```cmd
    cd azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample
    ```

4. En la carpeta *SymmetricKeySample*, abra el archivo *Parameters.cs* en un editor de texto. Este archivo muestra los parámetros que se admiten en el ejemplo. En este artículo, solo se usarán los tres primeros parámetros necesarios al ejecutar el ejemplo. Revise el código de este archivo. No es necesario realizar ningún cambio.

    | Parámetro                         | Obligatorio | Descripción     |
    | :-------------------------------- | :------- | :-------------- |
    | `--s` o `--IdScope`              | True     | Ámbito del identificador de la instancia de DPS. |
    | `--i` o `--Id`                   | True     | Identificador de registro cuando se usa la inscripción individual o identificador del dispositivo deseado cuando se usa la inscripción de grupo. |
    | `--p` o `--PrimaryKey`           | True     | Clave principal de la inscripción individual o de grupo. |
    | `--e` o `--EnrollmentType`       | False    | Tipo de inscripción: `Individual` o `Group`. De manera predeterminada, su valor es `Individual`. |
    | `--g` o `--GlobalDeviceEndpoint` | False    | Punto de conexión global al que se conectarán los dispositivos. De manera predeterminada, su valor es `global.azure-devices-provisioning.net`. |
    | `--t` o `--TransportType`        | False    | Transporte que se va a utilizar para comunicarse con la instancia de Device Provisioning. Su valor predeterminado es `Mqtt`. Los valores posibles incluyen `Mqtt`, `Mqtt_WebSocket_Only`, `Mqtt_Tcp_Only`, `Amqp`, `Amqp_WebSocket_Only`, `Amqp_Tcp_only` y `Http1`.|

5. En la carpeta *SymmetricKeySample*, abra el archivo *ProvisioningDeviceClientSample.cs* en un editor de texto. Este archivo muestra cómo se usa la clase [SecurityProviderSymmetricKey](/dotnet/api/microsoft.azure.devices.shared.securityprovidersymmetrickey?view=azure-dotnet&preserve-view=true) junto con la clase [ProvisioningDeviceClient](/dotnet/api/microsoft.azure.devices.provisioning.client.provisioningdeviceclient?view=azure-dotnet&preserve-view=true) para aprovisionar el dispositivo de clave simétrica simulado. Revise el código de este archivo.  No es necesario realizar ningún cambio.

6. Compile y ejecute el código de ejemplo con el siguiente comando después de reemplazar los tres parámetros de ejemplo (reemplace `<id-scope>` por el ámbito de identificador de Device Provisioning Service, `<registration-id>` por el identificador de registro del dispositivo y `<primarykey>` por la clave principal del dispositivo).

    ```console
    dotnet run --s <id-scope> --i <registration-id> --p <primarykey>
    ```

7. Debería ver algo parecido a los siguiente. Se envía una cadena "TestMessage" al centro como mensaje de prueba.

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

El código de aprovisionamiento de ejemplo realiza las siguientes tareas, en orden:

1. Autentica el dispositivo en el recurso de Device Provisioning, para lo que usa los cuatro parámetros siguientes:
    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Asigna el dispositivo al centro de IoT ya vinculado a la instancia de Device Provisioning Service.

3. Envía un mensaje de telemetría de prueba al centro de IoT.

Para actualizar y ejecutar el ejemplo de aprovisionamiento con la información del dispositivo:

1. En el menú principal de Device Provisioning Service, seleccione **Información general**.

2. Copie los valores de **Ámbito de id.** y **Punto de conexión del servicio**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Extracción de información del punto de conexión de Device Provisioning Service":::

3. Abra un símbolo del sistema para ejecutar comandos de Node.js y vaya al siguiente directorio:

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

4. En la carpeta *provisioning/device/samples*, abra *register_symkey.js* y examine el código. Observe que el código de ejemplo establece una carga personalizada:

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    Puede marcar este código como comentario, ya que no es necesario para este inicio rápido. Se necesitaría una carga personalizada si deseara utilizar una función de asignación personalizada para asignar el dispositivo a IoT Hub. Para más información, consulte el [Tutorial: Uso de directivas de asignación personalizadas](tutorial-custom-allocation-policies.md).

     El método `provisioningClient.register()` intenta registrar el dispositivo.

    No se requieren más cambios.

5. En el símbolo del sistema, ahora establecerá las siguientes variables de entorno (reemplace `<id-scope>` por el ámbito de identificador de Device Provisioning Service, `<registration-id>` por el identificador de registro del dispositivo, `<primarykey>` por la clave principal del dispositivo, `<provisioning-host>` por la dirección URL del punto de conexión de servicio de Device Provisioning Service):

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

6. Compile y ejecute el código de ejemplo mediante los siguientes comandos:

   ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

7. Debería ver algo parecido a los siguiente. Se envía la cadena "Hola mundo" al centro como mensaje de prueba.

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-python"

El código de aprovisionamiento de ejemplo realiza las siguientes tareas, en orden:

1. Autentica el dispositivo en el recurso de Device Provisioning, para lo que usa los cuatro parámetros siguientes:

    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Asigna el dispositivo al centro de IoT ya vinculado a la instancia de Device Provisioning Service.

3. Envía un mensaje de telemetría de prueba al centro de IoT.

Para actualizar y ejecutar el ejemplo de aprovisionamiento con la información del dispositivo:

1. En el menú principal de Device Provisioning Service, seleccione **Información general**.

2. Copie los valores de **Ámbito de id.** y **Punto de conexión del servicio**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Extracción de información del punto de conexión de Device Provisioning Service":::

3. En el símbolo del sistema, ahora establecerá las siguientes variables de entorno (reemplace `<id-scope>` por el ámbito de identificador de Device Provisioning Service, `<registration-id>` por el identificador de registro del dispositivo, `<primarykey>` por la clave principal del dispositivo, `<provisioning-host>` por la dirección URL del punto de conexión de servicio de Device Provisioning Service):

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

4. En la ventana del símbolo del sistema, vaya al directorio siguiente:

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

5. Ejecute el código de ejemplo de Python en *_provision_symmetric_key.py_*.

    ```console
    python provision_symmetric_key.py
    ```

6. Debería ver algo parecido a los siguiente. También se envían al centro algunos ejemplos de mensajes de telemetría de velocidad del viento como prueba.

     ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```

::: zone-end

::: zone pivot="programming-language-java"

El código de aprovisionamiento de ejemplo realiza las siguientes tareas, en orden:

1. Autentica el dispositivo en el recurso de Device Provisioning, para lo que usa los cuatro parámetros siguientes:

    * `GLOBAL_ENDPOINT`
    * `SCOPE_ID`
    * `REGISTRATION_ID`
    * `SYMMETRIC_KEY`

2. Asigna el dispositivo al centro de IoT ya vinculado a la instancia de Device Provisioning Service.

3. Envía un mensaje de telemetría de prueba al centro de IoT.

Para actualizar y ejecutar el ejemplo de aprovisionamiento con la información del dispositivo:

1. En el menú principal de Device Provisioning Service, seleccione **Información general**.

2. Copie los valores de **Ámbito de id.** y **Punto de conexión del servicio**. En este caso, se trata de `SCOPE_ID` y `GLOBAL_ENDPOINT`, respectivamente.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Extracción de información del punto de conexión de Device Provisioning Service":::

3. Abra el código de ejemplo del dispositivo Java para editarlo. La ruta de acceso completa al código de ejemplo del dispositivo es:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

4. Reemplace el valor de las siguientes variables por el DPS y la inscripción del dispositivo (reemplace `<id-scope>` por el ámbito de identificador de Device Provisioning Service, `<registration-id>` por el identificador de registro del dispositivo, `<primarykey>` por la clave principal del dispositivo, `<provisioning-host>` por la dirección URL del punto de conexión de servicio de Device Provisioning Service):

    ```java
    private static final String SCOPE_ID = "<id-scope>";
    private static final String GLOBAL_ENDPOINT = "<provisioning-host>";
    private static final String SYMMETRIC_KEY = "<primarykey>";
    private static final String REGISTRATION_ID = "<registration-id>";
    ```

5. Abra el símbolo del sistema para la compilación. Vaya a la carpeta del proyecto de ejemplo de aprovisionamiento del repositorio del SDK de Java.

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

6. Compile el ejemplo y después vaya a la carpeta `target` y ejecute el archivo `.jar` que ha creado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

7. Debería ver algo parecido a los siguiente.

     ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>Confirmación del registro de aprovisionamiento de dispositivos

1. Vaya a [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione el centro de IoT al que se asignó el dispositivo.

4. En el menú **Exploradores**, seleccione **Dispositivos de IoT**.

5. Si el dispositivo se aprovisionó correctamente, el identificador del dispositivo debe aparecer en la lista y el valor de **Estado** debería ser *habilitado*. Si no ve el dispositivo, seleccione **Actualizar** en la parte superior de la página.

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration.png" alt-text="El dispositivo se registra con el centro de IoT":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-csharp.png" alt-text="El dispositivo con CSharp se registra en el centro de IoT":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-nodejs.png" alt-text="El dispositivo con Node.js se registra en el centro de IoT":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-python.png" alt-text="El dispositivo con Python se registra en el centro de IoT":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-java.png" alt-text="El dispositivo con Java se registra con el centro de IoT":::

    ::: zone-end

> [!NOTE]
> Si ha cambiado el valor predeterminado de *Estado inicial del dispositivo gemelo* en la entrada de inscripción para el dispositivo, el dispositivo puede extraer el estado gemelo deseado desde el centro y actuar en consecuencia. Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Limpieza de recursos

Si planea seguir trabajando con el ejemplo de cliente de dispositivo y explorándolo, no quite los recursos que se hayan creado en este inicio rápido. Si no planea continuar, use el siguiente comando para eliminar todos los recursos que se hayan creado en este inicio rápido.

### <a name="delete-your-device-enrollment"></a>Eliminación de la inscripción del dispositivo

1. Cierre la ventana de salida de ejemplo del cliente del dispositivo en su máquina.

2. En el menú de la izquierda de Azure Portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service.

4. En el menú **Configuración**, seleccione **Administrar inscripciones**.

5. Seleccione la pestaña **Inscripciones individuales**.

6. Active la casilla situada junto al campo *ID. DE REGISTRO* del dispositivo que ha inscrito en este inicio rápido.

7. En la parte superior de la página, seleccione **Eliminar**.

### <a name="delete-your-device-registration-from-iot-hub"></a>Elimine el registro del dispositivo de IoT Hub

1. En el menú de la izquierda de Azure Portal, seleccione **Todos los recursos**.

2. Seleccione IoT Hub.

3. En el menú **Exploradores**, seleccione **Dispositivos de IoT**.

4. Active la casilla situada junto al campo *ID. DE DISPOSITIVO* del dispositivo que registró en este inicio rápido.

5. En la parte superior de la página, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Aprovisionamiento de un dispositivo con certificado X.509:

> [!div class="nextstepaction"]
> [Inicio rápido: Aprovisionamiento de un dispositivo X.509 mediante el SDK para C de Azure IoT](quick-create-simulated-device-x509.md)