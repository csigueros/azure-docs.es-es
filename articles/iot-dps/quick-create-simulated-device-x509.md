---
title: 'Inicio rápido: Aprovisionamiento de un dispositivo de certificado X.509 simulado en Microsoft Azure IoT Hub'
description: Aprenda a aprovisionar un dispositivo simulado que se autentica con un certificado X.509 en Azure IoT Hub Device Provisioning Service
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/07/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: c0c3d486e2a886a49c51e7ed78ed935c1a1c5c22
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276697"
---
# <a name="quickstart-provision-an-x509-certificate-simulated-device"></a>Inicio rápido: Aprovisionamiento de un dispositivo simulado de certificado X.509

En este inicio rápido creará un dispositivo simulado en una máquina Windows. El dispositivo simulado se configurará para usar el mecanismo de [atestación de certificado X.509](concepts-x509-attestation.md) para la realizar autenticación. Después de configurar el dispositivo, lo aprovisionará en un centro de IoT mediante Azure IoT Hub Device Provisioning Service.

Si no conoce el proceso de aprovisionamiento, consulte la información general sobre el [aprovisionamiento](about-iot-dps.md#provisioning-process).  Asegúrese de completar los pasos descritos en [Configuración del servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md) antes de continuar.

En este inicio rápido se muestra una solución para una estación de trabajo basada en Windows. No obstante, también puede realizar los procedimientos en Linux. Para obtener un ejemplo de Linux, consulte [Cómo aprovisionar para varios inquilinos](how-to-provision-multitenant.md).

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

* Realice los pasos que se describen en [Configuración de IoT Hub Device Provisioning Service con Azure Portal](./quick-setup-auto-provision.md).

Los siguientes requisitos previos corresponden a un entorno de desarrollo de Windows. En el caso de Linux o macOS, consulte la sección correspondiente en [Preparación del entorno de desarrollo](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) en la documentación del SDK.

::: zone pivot="programming-language-ansi-c"

* Instale [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) con la carga de trabajo [Desarrollo para el escritorio con C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitada. También se admiten Visual Studio 2015 y Visual Studio 2017. En el caso de Linux o macOS, consulte la sección correspondiente en [Preparación del entorno de desarrollo](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) en la documentación del SDK.

::: zone-end

::: zone pivot="programming-language-csharp"

* Instale el [SDK de .NET Core 3.1 o cualquier versión posterior](https://dotnet.microsoft.com/download), en una máquina con Windows. Para comprobar la versión, use el siguiente comando.

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* Instale [Node.js v4.0 o una versión posterior](https://nodejs.org) en la máquina.

* Instale [OpenSSL](https://www.openssl.org/) en la máquina y asegúrese de que se agrega a las variables de entorno accesibles para la ventana de comandos. Esta biblioteca se puede compilar e instalar desde el origen o descargarse e instalarse desde el sitio web de un [tercero](https://wiki.openssl.org/index.php/Binaries) como [este](https://sourceforge.net/projects/openssl/).

::: zone-end

::: zone pivot="programming-language-python"

* [Python 3.6 o una versión superior](https://www.python.org/downloads/) en la máquina.

* Instale [OpenSSL](https://www.openssl.org/) en la máquina y asegúrese de que se agrega a las variables de entorno accesibles para la ventana de comandos. Esta biblioteca se puede compilar e instalar desde el origen o descargarse e instalarse desde el sitio web de un [tercero](https://wiki.openssl.org/index.php/Binaries) como [este](https://sourceforge.net/projects/openssl/).

::: zone-end

::: zone pivot="programming-language-java"

* Instale [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure) o cualquier versión posterior, en la máquina.

* Descargue e instale [Maven](https://maven.apache.org/install.html).

::: zone-end

* Instale la última versión de [Git](https://git-scm.com/download/). Asegúrese de que Git se ha agregado a las variables de entorno accesibles desde la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente de las herramientas `git`, lo que incluye *Git Bash*, la aplicación de línea de comandos que puede usar para interactuar con su repositorio de Git local.

## <a name="prepare-your-development-environment"></a>Preparación del entorno de desarrollo

::: zone pivot="programming-language-ansi-c"

En esta sección, preparará un entorno de desarrollo que se usa para compilar el [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c). El código de ejemplo intenta aprovisionar el dispositivo durante la secuencia de arranque del dispositivo.

1. Descargue el último [sistema de compilación CMake](https://cmake.org/download/).

    >[!IMPORTANT]
    >Confirme que los requisitos previos de Visual Studio (Visual Studio y la carga de trabajo "Desarrollo para el escritorio con C++") estén instalados en la máquina **antes** de empezar la instalación de `CMake`. Una vez que los requisitos previos están en su lugar, y se ha comprobado la descarga, instale el sistema de compilación de CMake. Igualmente, tenga en cuenta que las versiones anteriores del sistema de compilación CMake no generan el archivo de solución que se usa en este artículo. Asegúrese de usar la versión más reciente de CMake.

2. Abra un explorador web y vaya a la [página de la versión del SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

3. Seleccione la pestaña **Etiquetas** en la parte superior de la página.

4. Copie el nombre de etiqueta de la versión más reciente del SDK de Azure IoT para C.

5. Abra un símbolo del sistema o el shell de Bash de Git. Ejecute los siguientes comandos para clonar la versión más reciente del repositorio de GitHub del [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c). (Reemplace `<release-tag>` con el nombre de la etiqueta que copió en el paso anterior).

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

7. El código de ejemplo usa un certificado X.509 para proporcionar atestación mediante la autenticación de X.509. Ejecute el siguiente comando para compilar una versión del SDK específica para su plataforma de desarrollo que incluya el cliente de aprovisionamiento de dispositivos. Se genera una solución de Visual Studio para el dispositivo simulado en el directorio `cmake`.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    >[!TIP]
    >Si `cmake` no encuentra el compilador de C++, es posible que obtenga errores de compilación durante la ejecución del comando anterior. Si eso sucede, pruebe a ejecutar este comando en el [símbolo del sistema de Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

8. Una vez realizada la compilación, las últimas líneas de salida son similares a las siguientes:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
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

2. Utilice el siguiente comando para clonar el repositorio de GitHub de los [ejemplos de Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Abra un entorno de línea de comandos de Git CMD o Git Bash.

2. Utilice el siguiente comando para clonar el repositorio de GitHub de los [ejemplos de Azure IoT para Python](https://github.com/Azure/azure-iot-sdk-node.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Abra un entorno de línea de comandos de Git CMD o Git Bash.

2. Utilice el siguiente comando para clonar el repositorio de GitHub de los [ejemplos de Azure IoT para Java](https://github.com/Azure/azure-iot-sdk-java.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. Vaya al directorio `azure-iot-sdk-`java raíz y compile el proyecto para descargar todos los paquetes necesarios.

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

4. Vaya al proyecto de generación de certificados y compílelo.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

::: zone-end

## <a name="create-a-self-signed-x509-device-certificate"></a>Creación de un certificado de dispositivo X.509 autofirmado

En esta sección, usará código de ejemplo del SDK de Azure IoT para crear un certificado X.509 autofirmado. Se debe cargar este certificado en el servicio de aprovisionamiento y que este lo compruebe.

> [!CAUTION]
> Use los certificados creados con las herramientas del SDK para pruebas de desarrollo únicamente.
> No use estos certificados en producción.
> Los certificados generados que genera el SDK contienen contraseñas codificadas de forma rígida, como *1234*, y caducan pasados 30 días.
> Para saber cómo obtener certificados adecuados para el código de producción, consulte [Cómo obtener un certificado de entidad de certificación X.509](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) en la documentación de Azure IoT Hub.
>

::: zone pivot="programming-language-csharp"

El identificador del dispositivo IoT será el nombre común del firmante en el certificado. Asegúrese de usar un nombre de firmante que cumpla con los [requisitos de cadena del identificador de dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

::: zone-end

Para crear el certificado X.509:

::: zone pivot="programming-language-ansi-c"

### <a name="clone-the-azure-iot-c-sdk"></a>Clonación del SDK de Azure IoT para C

El [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c) contiene herramientas de prueba que pueden ayudarle a crear una cadena de certificados X.509, cargar un certificado raíz o intermedio a partir de esa cadena y realizar la prueba de posesión con el servicio para comprobar el certificado.

Si ya ha clonado la última versión del repositorio de GitHub del [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c), vaya a la [siguiente sección](#create-a-test-certificate).

1. Abra un explorador web y vaya a la [página de la versión del SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

2. Copie el nombre de etiqueta de la versión más reciente del SDK de Azure IoT para C.

3. Abra un símbolo del sistema o el shell de Bash de Git. Ejecute los siguientes comandos para clonar la versión más reciente del repositorio de GitHub del [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c). (Reemplace `<release-tag>` con el nombre de la etiqueta que copió en el paso anterior).

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operación puede tardar varios minutos en completarse.

4. Las herramientas de prueba deberían estar en el directorio *azure-iot-sdk-c/tools/CACertificates* del repositorio que clonó.

### <a name="create-a-test-certificate"></a>Creación de un certificado de prueba

Siga los pasos descritos en [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (Administración de certificados de entidad de certificación de prueba para ejemplos y tutoriales).

Además de las herramientas del SDK de C, el [ejemplo de comprobación del certificado de grupo](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) del *SDK de Microsoft Azure IoT para .NET* muestra cómo realizar la prueba de posesión en C# con un certificado X.509 existente de entidad de certificación raíz o intermedio.

::: zone-end

::: zone pivot="programming-language-csharp"

1. En un símbolo del sistema de PowerShell, cambie los directorios al directorio de proyecto para el ejemplo de aprovisionamiento de dispositivos X.509.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. El código de ejemplo está configurado para usar los certificados X.509 almacenados en un archivo con formato PKCS12 protegido con contraseña (`certificate.pfx`). Además, necesita un archivo de certificado de clave pública (`certificate.cer`) para crear una inscripción individual más adelante en este inicio rápido. Para generar el certificado autofirmado y los archivos `.cer` y `.pfx` asociados, ejecute el siguiente comando:

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. El script le pide una contraseña PFX. Recuerde esta contraseña, ya que tendrá que volver a usarla para ejecutar el ejemplo. De manera opcional, puede ejecutar `certutil` para volcar el certificado y comprobar el nombre del firmante.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Abra un símbolo del sistema, vaya al script del generador de certificados y compile el proyecto:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

2. Cree el certificado X.509 _de hoja_ mediante la ejecución del script con su propio _nombre-de-certificado_. El nombre común del certificado hoja se convierte en el [identificador del registro](./concepts-service.md#registration-id), así que asegúrese de usar solo minúsculas, números y guiones.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. En el símbolo del sistema de Git Bash, ejecute el siguiente comando:

    # <a name="windows"></a>[Windows](#tab/windows)

    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > La barra diagonal adicional que se proporciona para el nombre de firmante (`//CN=Python-device-01`) solo es necesaria para escapar la cadena con Git en plataformas Windows.

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```

    ---

2. Cuando se le pida que **Enter PEM pass phrase:** (Escriba la frase de contraseña de PEM:) use la frase de contraseña `1234`.

3. Cuando se le pregunte de nuevo **Verifying - Enter PEM pass phrase:** (Verificando: escriba la fase de contraseña de PEM), use de nuevo la frase de contraseña `1234`.

Se genera un archivo de certificado de prueba (*python-device.pem*) y un archivo de clave privada (*python-device.key.pem*) en el directorio donde ejecutó el comando `openssl`.

::: zone-end

::: zone pivot="programming-language-java"

1. Mediante el símbolo del sistema de los pasos anteriores, vaya a la carpeta `target`.

2. Ejecute el archivo .jar que creó en la sección anterior.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

3. Escriba **N** en _Do you want to input common name?_ (¿Quiere escribir un nombre de entrada?). 

4. Copie la salida de `Client Cert` al portapapeles, comenzando desde *-----BEGIN CERTIFICATE-----* hasta *-----END CERTIFICATE-----* .

   ![Generador de certificados individuales](./media/quick-create-simulated-device-x509/cert-generator-java.png)

5. Cree un archivo denominado *_X509individual.pem_* en el equipo Windows.

6. Abra el archivo denominado *_X509individual.pem_* en el editor que prefiera y copie el contenido del Portapapeles en este archivo. 

7. Guarde el archivo y cierre el editor.

8. En el símbolo del sistema, escriba **N** en _Do you want to input Verification Code_ (¿Desea escribir un código de verificación?) y mantenga la salida del programa abierta para usarla como referencia más adelante en el inicio rápido. A continuación, copie los valores `Client Cert` y `Client Cert Private Key` para su uso en la sección siguiente.

::: zone-end

## <a name="create-a-device-enrollment"></a>Creación de una inscripción de dispositivos

Azure IoT Hub Device Provisioning Service admite dos tipos de inscripciones:

* [Grupos de inscripción](concepts-service.md#enrollment-group): usados para inscribir varios dispositivos relacionados.
* [Inscripciones individuales](concepts-service.md#individual-enrollment): usadas para inscribir un solo dispositivo.

En este artículo se muestra una inscripción de un dispositivo que se va a aprovisionar con un centro de IoT.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. En la parte superior de la página, seleccione **+ Agregar inscripción individual**.

::: zone pivot="programming-language-ansi-c"

6. En la página **Agregar inscripción**, escriba la siguiente información.

    * **Mecanismo:** Seleccione **X.509** como *Mecanismo* de atestación de identidad.
    * **Archivo .pem o .cer de certificado principal**: haga clic en **Seleccionar un archivo** para seleccionar el archivo de certificado *X509testcert.pem* que creó en la sección anterior.
    * **Id. de dispositivo IoT Hub:** escriba *test-docs-cert-device* para dar al dispositivo un identificador.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Agregue el dispositivo como inscripción individual con la atestación X.509.":::

::: zone-end

::: zone pivot="programming-language-csharp"

6. En la página **Agregar inscripción**, escriba la siguiente información.

    * **Mecanismo:** Seleccione **X.509** como *Mecanismo* de atestación de identidad.
    * **Archivo .pem o .cer de certificado principal**: elija **Seleccionar un archivo** para seleccionar el archivo de certificado *certificate.cer* que creó en la sección anterior.
    * Deje el valor del **id. de dispositivo de IoT Hub** en blanco. El dispositivo se aprovisionará con su id. de dispositivo establecido en el nombre común en el certificado X.509, *iothubx509device1*. Este nombre común también será el nombre usado para el identificador de registro de la entrada de inscripción individual.
    * De forma opcional, puede proporcionar la siguiente información:
        * Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
        * Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Agregue el dispositivo como inscripción individual con la atestación X.509.":::

::: zone-end

::: zone pivot="programming-language-nodejs"

6. En la página **Agregar inscripción**, escriba la siguiente información.

    * **Mecanismo:** Seleccione **X.509** como *Mecanismo* de atestación de identidad.
    * **Archivo .pem o .cer de certificado principal**: elija **Seleccionar un archivo** para seleccionar el archivo de certificado *{certificate-name}_cert.pem* que creó en la sección anterior.
    * De forma opcional, puede proporcionar la siguiente información:
        * Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
        * Escriba un identificador de dispositivo único. Asegúrese de evitar datos confidenciales al asignar nombre al dispositivo.
        * Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Agregue el dispositivo como inscripción individual con la atestación X.509.":::

::: zone-end

::: zone pivot="programming-language-python"

6. En la página **Agregar inscripción**, escriba la siguiente información.

    * **Mecanismo:** Seleccione **X.509** como *Mecanismo* de atestación de identidad.
    * **Archivo principal de certificado .pem o .cer**: elija **Seleccione un archivo** para seleccionar el archivo de certificado *python-device.pem* si va a usar el certificado de prueba que creó anteriormente.
    * De forma opcional, puede proporcionar la siguiente información:
        * Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
        * Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Agregue el dispositivo como inscripción individual con la atestación X.509.":::

::: zone-end

::: zone pivot="programming-language-java"

6. En el panel **Agregar inscripción**, escriba la siguiente información:
   * Seleccione **X.509** como *Mecanismo* de atestación de identidad.
   * En *Archivo .pem o .cer del certificado principal*, elija *Seleccionar un archivo* para seleccionar el archivo de certificado *X509individual.pem* que creó en los pasos anteriores.  
   * De forma opcional, puede proporcionar la siguiente información:
     * Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
     * Escriba un identificador de dispositivo único. Asegúrese de evitar datos confidenciales al asignar nombre al dispositivo. 
     * Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.
    
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Agregue el dispositivo como inscripción individual con la atestación X.509.":::

::: zone-end
    
7. Seleccione **Guardar**. Volverá a **Administrar inscripciones**.

8. Seleccione **Inscripciones individuales**. La entrada de la inscripción X.509 debe aparecer en la tabla de registro.

## <a name="prepare-and-run-the-device-provisioning-code"></a>Preparación y ejecución del código de aprovisionamiento de dispositivos

::: zone pivot="programming-language-ansi-c"

En esta sección, actualizará el código de ejemplo para enviar la secuencia de arranque del dispositivo a la instancia de Device Provisioning Service . Esta secuencia de arranque hará que el dispositivo se reconozca y se asigne a un centro de IoT vinculado a la instancia del servicio Device Provisioning.

1. En Azure Portal, seleccione la pestaña **Información general** de Device Provisioning Service.

2. Copie el valor de del **_Ámbito de id_**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="Copie el ámbito del id. del portal.":::

3. En la ventana del *Explorador de soluciones* de Visual Studio, desplácese hasta la carpeta **Aprovisionar\_Ejemplos**. Expanda el proyecto de ejemplo denominado **prov\_dev\_client\_sample**. Expanda **Archivos de código fuente** y abra **prov\_dev\_cliente\_sample.c**.

4. Busque la constante `id_scope` y reemplace el valor por el valor de **Ámbito de id.** que copió anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Busque la definición de la función `main()` en el mismo archivo. Asegúrese de que la variable `hsm_type` está establecida en `SECURE_DEVICE_TYPE_X509` y no en `SECURE_DEVICE_TYPE_TPM`, tal como se muestra a continuación.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Haga clic con el botón derecho en el proyecto **prov\_dev\_client\_sample** y seleccione **Set as Startup Project** (Establecer como proyecto de inicio).

7. En el menú de Visual Studio, seleccione **Depurar** > **Iniciar sin depurar** para ejecutar la solución. En la solicitud para volver a compilar el proyecto, haga clic en **Sí** para recompilar el proyecto antes de ejecutarlo.

    La salida siguiente es un ejemplo de arranque correcto del cliente de dispositivo de aprovisionamiento de ejemplo y su conexión a la instancia del servicio de aprovisionamiento para obtener información de IoT Hub y registrarse:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. En Azure Portal, seleccione la pestaña **Información general** de Device Provisioning Service.

2. Copie el valor de del **_Ámbito de id_**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="Copie el ámbito del id. del portal.":::

3. Abra una ventana de símbolo del sistema.

4. Escriba el siguiente comando para compilar y ejecutar el ejemplo de aprovisionamiento de dispositivos X.509 (reemplace el valor `<IDScope>` por el ámbito de id. que copió en la sección anterior). El archivo de certificado será de forma predeterminada *./certificate.pfx* y solicitará la contraseña .pfx. Escriba la contraseña.

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Si desea pasar todo como un parámetro, puede usar el siguiente formato de ejemplo.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```

5. El dispositivo se conectará a DPS y se asignará a un centro de IoT. A continuación, el dispositivo enviará un mensaje de telemetría al centro.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. En Azure Portal, seleccione la pestaña **Información general** de Device Provisioning Service. 

2. Copie los valores del **_Ámbito de id._** y el **Punto de conexión global del dispositivo**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="Copie el ámbito del id. del portal.":::

3. Copie el _certificado_ y la _clave_ en la carpeta de ejemplo.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

4. Vaya al script de prueba de dispositivo y compile el proyecto.

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

5. Edite el archivo **register\_x509.js** con los siguientes cambios:

    * Reemplace `provisioning host` con el **_punto de conexión de dispositivo global_** que anotó en el **paso 1** anterior.
    * Reemplace `id scope` por el **_ámbito de id._** que anotó en el **paso 1** anterior. 
    * Reemplace `registration id` por el **_Identificador de registro_** que anotó en la sección anterior.
    * Reemplace `cert filename` y `key filename` con los archivos que copió en el **paso 2** anterior.

6. Guarde el archivo.

7. Ejecute el script y compruebe que el dispositivo se haya aprovisionado correctamente.

    ```cmd/sh
    node register_x509.js
    ``` 

>[!TIP]
>El [SDK de dispositivo de Node.js de Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-node) proporciona una manera sencilla de simular un dispositivo. Para obtener más información, consulte [Conceptos de dispositivos](./concepts-service.md).

::: zone-end

::: zone pivot="programming-language-python"

El ejemplo de aprovisionamiento de Python, [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py), se encuentra en el directorio `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios`. En este ejemplo se usan seis variables de entorno para autenticar y aprovisionar un dispositivo IoT mediante DPS. Estas variables de entorno son:

| Nombre de la variable              | Descripción                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Este valor es el punto de conexión global que se usa para conectarse al recurso de DPS |    
| `PROVISIONING_IDSCOPE`     |  Este valor es el ámbito de identificador del recurso de DPS. |    
| `DPS_X509_REGISTRATION_ID` |  Este valor es el identificador del dispositivo. También debe coincidir con el nombre del firmante en el certificado del dispositivo. |    
| `X509_CERT_FILE`           |  El nombre de archivo del certificado del dispositivo. |    
| `X509_KEY_FILE`            |  El nombre de archivo de la clave privada del certificado del dispositivo. |
| `PASS_PHRASE`              |  La frase de contraseña usada para cifrar el certificado y el archivo de clave privada (`1234`). |    

1. En Azure Portal, seleccione la pestaña **Información general** de Device Provisioning Service.

2. Copie los valores del **_Ámbito de id._** y el **Punto de conexión global del dispositivo**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="Copie el ámbito del id. del portal.":::

3. En el símbolo del sistema de Git Bash, use los siguientes comandos para agregar las variables de entorno del punto de conexión del dispositivo global y el ámbito de id.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

4. El identificador de registro del dispositivo IoT debe coincidir con el nombre del firmante que aparece en su certificado de dispositivo. Si ha generado un certificado de prueba autofirmado, `Python-device-01` es el nombre del firmante y el id. de registro del dispositivo.

    Si ya tiene un certificado de dispositivo, puede usar `certutil` para comprobar el nombre común del firmante que se usa para el dispositivo, tal como se muestra a continuación:

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

5. En el símbolo del sistema de Git Bash, establezca la variable de entorno del identificador de registro como se indica a continuación:

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

6. En el símbolo del sistema de Git Bash, establezca las variables de entorno del archivo de certificado, el archivo de clave privada y la frase de contraseña.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

7. Revise el código del archivo [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py). Si no usa la **versión 3.7 de Python** o una posterior, realice el [cambio en el código mencionado aquí](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) para reemplazar `asyncio.run(main())` y guarde el cambio.

8. Guarde los cambios.

9. Ejecute el ejemplo. El ejemplo se conectará, se aprovisionará el dispositivo en un centro y se enviarán algunos mensajes de prueba al centro.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. En Azure Portal, seleccione la pestaña **Información general** de Device Provisioning Service.

2. Copie los valores del **_Ámbito de id._** y el **Punto de conexión global del dispositivo**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="Copie el ámbito del id. del portal.":::

3. Abra un símbolo del sistema. Vaya a la carpeta del proyecto de ejemplo en el repositorio del SDK de Java.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

4. Escriba la información del servicio de aprovisionamiento y de la identidad X.509 en el código. Se utiliza durante el aprovisionamiento para la atestación del dispositivo simulado, antes del registro de dispositivo:

   * Edite el archivo `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` para que incluya el _Ámbito de id._ y el _Provisioning Service Global Endpoint_ (Punto de conexión global del Servicio de aprovisionamiento) tal y como se indicó previamente. Incluya también el valor de _Client cert_ (Certificado de cliente) y _Client Cert Private Key_ (Clave privada del certificado de cliente) tal y como se indicó en la sección anterior.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   * Al copiar y pegar el certificado y la clave privada, utilice el siguiente formato:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

5. Genere la muestra y luego vaya a la carpeta `target` y ejecute el archivo .jar creado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>Confirmación del registro de aprovisionamiento de dispositivos

1. Vaya a [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione el centro de IoT al que se asignó el dispositivo.

4. En el menú **Exploradores**, seleccione **Dispositivos de IoT**.

5. Si el dispositivo se aprovisionó correctamente, el id. del dispositivo debe aparecer en la lista, con el **estado** configurado como *habilitado*. Si no ve el dispositivo, seleccione **Actualizar** en la parte superior de la página.

   :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration.png" alt-text="El dispositivo se registra con el centro de IoT":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-csharp.png" alt-text="El dispositivo con CSharp se registra en el centro de IoT":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-nodejs.png" alt-text="El dispositivo con Node.js se registra en el centro de IoT":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-python.png" alt-text="El dispositivo con Python se registra en el centro de IoT":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-java.png" alt-text="El dispositivo con Java se registra con el centro de IoT":::

    ::: zone-end


::: zone pivot="programming-language-csharp,programming-language-nodejs,programming-language-python,programming-language-java"

>[!IMPORTANT]
>Si ha cambiado el valor predeterminado de *Estado inicial del dispositivo gemelo* en la entrada de inscripción para el dispositivo, el dispositivo puede extraer el estado gemelo deseado desde el centro y actuar en consecuencia. Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)

::: zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

Si planea seguir trabajando con el ejemplo de cliente de dispositivo y explorándolo, no quite los recursos que se hayan creado en esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos que se hayan creado en la guía de inicio rápido.

### <a name="delete-your-device-enrollment"></a>Eliminación de la inscripción de dispositivos

1. Cierre la ventana de salida de ejemplo del cliente del dispositivo en su máquina.

2. En Azure Portal, seleccione **Todos los recursos** en el menú de la izquierda.

3. Seleccione la instancia de Device Provisioning Service.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. Seleccione la pestaña **Inscripciones individuales**.

6. Active la casilla situada junto al campo *ID. DE REGISTRO* del dispositivo que ha inscrito en esta guía de inicio rápido.

7. En la parte superior de la página, seleccione **Eliminar**.

### <a name="delete-your-device-registration-from-iot-hub"></a>Elimine el registro del dispositivo de IoT Hub

1. En Azure Portal, seleccione **Todos los recursos** en el menú de la izquierda.

2. Seleccione IoT Hub.

3. En el menú **Exploradores**, seleccione **Dispositivos de IoT**.

4. Seleccione la casilla situada junto al campo *ID. DE DISPOSITIVO* del dispositivo que registró en esta guía de inicio rápido.

5. En la parte superior de la página, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Para saber cómo inscribir el dispositivo X.509 mediante programación:

> [!div class="nextstepaction"]
> [Inicio rápido de Azure: Inscripción de dispositivos X.509 en Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509.md)