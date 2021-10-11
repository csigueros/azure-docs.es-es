---
title: 'Inicio rápido: inscripción de grupos al servicio Azure Device Provisioning Service mediante la atestación de certificado X.509'
description: En esta guía de inicio rápido se muestra cómo inscribir mediante programación un grupo de dispositivos que utilizan la atestación de un certificado X.509 intermedio o raíz de una entidad de certificación.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/17/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
zone_pivot_groups: iot-dps-set2
ms.openlocfilehash: 1b668c4e15db7ff35adfa6078eeaac441e8377ea
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293887"
---
# <a name="quickstart-enroll-a-group-of-devices-to-the-device-provisioning-service-using-x509-certificate-attestation"></a>Inicio rápido: inscripción de un grupo de dispositivos en Device Provisioning Service mediante la atestación de certificado X.509 

:::zone pivot="programming-language-csharp,programming-language-nodejs, programming-language-python"

En esta guía de inicio rápido se muestra cómo crear mediante programación un [grupo de inscripción](concepts-service.md#enrollment-group) que usa certificados X.509 intermedios o raíces de una entidad de certificación. El grupo de inscripción se crea mediante el [SDK de Microsoft Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) y una aplicación de ejemplo. Un grupo de inscripción controla el acceso al servicio de aprovisionamiento de los dispositivos que comparten un certificado de firma común en su cadena de certificados. Para más información, consulte [Control del acceso de dispositivo al servicio de aprovisionamiento con certificados X.509](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Para más información sobre el uso de la infraestructura de clave pública (PKI) basada en certificados X.509 con Azure IoT Hub y el servicio Device Provisioning, consulte [Introducción a la seguridad mediante certificados de entidades de certificación X.509](../iot-hub/iot-hub-x509ca-overview.md).

:::zone-end

:::zone pivot="programming-language-java"

En esta guía de inicio rápido se muestra cómo crear mediante programación una inscripción individual y un [grupo de inscripción](concepts-service.md#enrollment-group) que usa certificados X.509 intermedios o raíces de una entidad de certificación. El grupo de inscripción se crea mediante el [SDK de Microsoft Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) y una aplicación de ejemplo. Un grupo de inscripción controla el acceso al servicio de aprovisionamiento de los dispositivos que comparten un certificado de firma común en su cadena de certificados. Para más información, consulte [Control del acceso de dispositivo al servicio de aprovisionamiento con certificados X.509](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Para más información sobre el uso de la infraestructura de clave pública (PKI) basada en certificados X.509 con Azure IoT Hub y el servicio Device Provisioning, consulte [Introducción a la seguridad mediante certificados de entidades de certificación X.509](../iot-hub/iot-hub-x509ca-overview.md).

:::zone-end

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

* Realice los pasos que se describen en [Configuración de IoT Hub Device Provisioning Service con Azure Portal](./quick-setup-auto-provision.md).

:::zone pivot="programming-language-csharp"

* Instale [Visual Studio 2019](https://www.visualstudio.com/vs/).

* Instale el [SDK de .NET Core 3.1 o cualquier versión posterior](https://dotnet.microsoft.com/download), en una máquina con Windows. Para comprobar la versión, use el siguiente comando.

    ```bash
    dotnet --info
    ```

:::zone-end

:::zone pivot="programming-language-nodejs"

* Instale [Node.js v4.0 o una versión posterior](https://nodejs.org) en la máquina.

:::zone-end

:::zone pivot="programming-language-python"

* Instale [Python 2.x o 3.x](https://www.python.org/downloads/) y agregue Python a las variables de entorno específicas de su plataforma.

    > [!IMPORTANT]
    > Este artículo solo se aplica al SDK de Python v1 en desuso. El dispositivo y los clientes del servicio para IoT Hub Device Provisioning Service aún no están disponibles en la versión 2. Actualmente, el equipo está haciendo todo lo posible para incorporar la versión 2 a la paridad de características.

* Instale [Pip](https://pip.pypa.io/en/stable/installing/), si aún no está incluido con su distribución de Python.

:::zone-end

:::zone pivot="programming-language-java"

* [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure). En este inicio rápido se instala el [SDK de servicio de Java](https://azure.github.io/azure-iot-sdk-java/master/service/) siguiente. Funciona tanto en Windows como en Linux. En este inicio rápido se usa Windows.

* [Maven 3](https://maven.apache.org/download.cgi).

:::zone-end

* Instale la última versión de [Git](https://git-scm.com/download/). Asegúrese de que Git se ha agregado a las variables de entorno accesibles desde la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente de las herramientas `git`, lo que incluye *Git Bash*, la aplicación de línea de comandos que puede usar para interactuar con su repositorio de Git local.

>[!NOTE]
>Aunque los pasos de este artículo funcionan en equipos Windows y Linux, en este artículo se usa un equipo de desarrollo de Windows.

## <a name="prepare-test-certificates"></a>Preparación de los certificados de prueba

En esta guía de inicio rápido debe tener un archivo *.pem* o *.cer* que contenga la porción pública de un certificado X.509 intermedio o raíz de entidad de certificación. Se debe cargar este certificado en el servicio de aprovisionamiento y que este lo compruebe.

:::zone pivot="programming-language-csharp,programming-language-nodejs, programming-language-python"

### <a name="clone-the-azure-iot-c-sdk"></a>Clonación del SDK de Azure IoT para C

El [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c) contiene herramientas de prueba que pueden ayudarle a crear una cadena de certificados X.509, cargar un certificado raíz o intermedio a partir de esa cadena y realizar la prueba de posesión con el servicio para comprobar el certificado.

Si ya ha clonado la última versión del repositorio de GitHub del [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c), vaya a la [siguiente sección](#create-the-test-certificate).

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

:::zone-end

:::zone pivot="programming-language-java"

<a id="javasample"></a>

### <a name="clone-the-azure-iot-java-sdk"></a>Clonación del SDK de Azure IoT para Java

El [SDK de Azure IoT para Java](https://github.com/Azure/azure-iot-sdk-java) contiene herramientas de prueba que pueden ayudarle a crear una cadena de certificados X.509, cargar un certificado raíz o intermedio a partir de esa cadena y realizar la prueba de posesión con el servicio para comprobar el certificado.

1. Abra un símbolo del sistema.

2. Clone el repositorio de GitHub para código de ejemplo de inscripción de dispositivos mediante el [SDK del servicio de Java](https://azure.github.io/azure-iot-sdk-java/master/service/):

    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

:::zone-end

### <a name="create-the-test-certificate"></a>Creación del certificado de prueba

Para crear el certificado de prueba:

:::zone pivot="programming-language-csharp,programming-language-nodejs, programming-language-python"

Para crear el certificado, siga los pasos descritos en [Administración de certificados de entidad de certificación de prueba para ejemplos y tutoriales](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

>[!TIP]
>Además de las herramientas del SDK de C, el [ejemplo de comprobación del certificado de grupo](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) del *SDK de Microsoft Azure IoT para .NET* muestra cómo realizar la prueba de posesión en C# con un certificado X.509 existente de entidad de certificación raíz o intermedio.

:::zone-end

:::zone pivot="programming-language-java"

1. En la ventana de comandos, vaya a la carpeta *_azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator_*.

2. Para compilar la herramienta, ejecute el siguiente comando:

    ```cmd\sh
    mvn clean install
    ```

3. Para ejecutar la herramienta, use el siguiente comando:

    ```cmd\sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

4. Cuando se le solicite, puede especificar opcionalmente un _nombre común_ para los certificados.

5. La herramienta genera de forma local un *Certificado de cliente*, la *Clave privada del certificado de cliente* y el *Certificado raíz*. Copie el *Certificado raíz*, ya que lo necesitará para modificar el código de muestra.

6. Cierre la ventana de comandos o escriba **n** cuando se le solicite el *código de verificación*.

:::zone-end

### <a name="add-and-verify-your-test-certificate"></a>Incorporación y comprobación del certificado de prueba

Para agregar y comprobar el certificado a la instancia de Device Provisioning Service.

1. Una vez que haya creado los certificados, inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service.

4. En el **Configuración,** seleccione **Certificados*.

5. En el menú superior, seleccione **+Agregar:** .

6. Escriba un nombre de certificado y cargue el archivo *.pem* que creó en la sección anterior.

7. Seleccione **Establecer el estado del certificado como comprobado al cargar**.

8. Seleccione **Guardar**.

:::image type="content" source="./media/quick-enroll-device-x509/add-certificate.png" alt-text="Agregue un certificado para realizar la comprobación":::.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Obtención de la cadena de conexión para el servicio de aprovisionamiento

En el ejemplo de esta guía de inicio rápido, necesitará copiar la cadena de conexión del servicio de aprovisionamiento.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service.

4. En el menú **Configuración**, seleccione **Directivas de acceso compartidas**.

5. Seleccione la directiva de acceso que quiera usar.

6. En el panel **Directiva de acceso**, copie y guarde la cadena de conexión de la clave principal.

    ![Obtención de la cadena de conexión del servicio de aprovisionamiento desde el portal](media/quick-enroll-device-x509/get-service-connection-string.png)

## <a name="create-the-enrollment-group-sample"></a>Crear el ejemplo del grupo de inscripción

:::zone pivot="programming-language-csharp"

En esta sección se muestra cómo crear una aplicación de consola de .NET Core que agrega un grupo de inscripción al servicio de aprovisionamiento.

>[!TIP]
>Con algunas modificaciones, también puede seguir estos pasos para crear una aplicación de consola de [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) para agregar un grupo de inscripción. Para más información sobre el desarrollo con IoT Core, consulte la [documentación para desarrolladores de Windows IoT Core](/windows/iot-core/).

1. Abra Visual Studio y seleccione **Crear un proyecto**.

2. En **Crear un proyecto nuevo**, seleccione **Aplicación de consola*.

3. Seleccione **Siguiente**.

4. En **Nombre del proyecto**, escriba *CreateEnrollmentGroup*.

5. Seleccione **Siguiente**. Mantenga la **plataforma de destino** predeterminada.

6. Seleccione **Crear**.

7. Cuando se abre la solución, en el panel **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **CreateEnrollmentGroup** y, a continuación, seleccione **Administrar paquetes NuGet**.

8. En **Administrador de paquetes NuGet**, seleccione **Examinar**.

9. Escriba y seleccione *Microsoft.Azure.Devices.Provisioning.Service*.

10. Seleccione **Instalar**.

    ![Ventana del Administrador de paquetes NuGet](media//quick-enroll-device-x509/add-nuget.png)

    Este paso descarga, instala y agrega una referencia al paquete NuGet del [SDK de cliente del servicio de aprovisionamiento de Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) y sus dependencias.

11. Agregue las siguientes instrucciones `using` después de las demás instrucciones `using` en la parte superior de `Program.cs`:

    ```csharp
    using System.Security.Cryptography.X509Certificates;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

12. Agregue los siguientes campos a la clase `Program` y realice los cambios de la lista.  

    ```csharp
    private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
    private static string EnrollmentGroupId = "enrollmentgrouptest";
    private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
    ```

13. Reemplace el valor del marcador de posición `ProvisioningServiceConnectionString` con la cadena de conexión del servicio de aprovisionamiento que copió en la sección anterior.

14. Reemplace el valor del marcador de posición `X509RootCertPath` por la ruta de acceso a un archivo .pem o .cer. Este archivo representa la parte pública de un certificado X.509 de entidad de certificación raíz o intermedio que se haya cargado y comprobado previamente en el servicio de aprovisionamiento.

15. Tiene la opción de cambiar el valor `EnrollmentGroupId`. La cadena solo puede contener caracteres en minúsculas y guiones.

    > [!IMPORTANT]
    > En el código de producción, tenga en cuenta las consideraciones de seguridad siguientes:
    >
    > * La codificación de forma rígida de la cadena de conexión para el administrador del servicio de aprovisionamiento va contra los procedimientos recomendados de seguridad. En su lugar, la cadena de conexión debe mantenerse de forma segura, como en un archivo de configuración seguro o en el registro.
    > * Asegúrese de cargar solo la parte pública del certificado de firma. No cargue nunca archivos .pfx (PKCS12) o .pem que contengan claves privadas en el servicio de aprovisionamiento.

16. Agregue el siguiente método a la clase `Program`. Este código crea una entrada de grupo de inscripción y luego llama al método `CreateOrUpdateEnrollmentGroupAsync` en `ProvisioningServiceClient` para agregar el grupo de inscripción al servicio de aprovisionamiento.

    ```csharp
    public static async Task RunSample()
    {
        Console.WriteLine("Starting sample...");
    
        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
        {
            #region Create a new enrollmentGroup config
            Console.WriteLine("\nCreating a new enrollmentGroup...");
            var certificate = new X509Certificate2(X509RootCertPath);
            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
            EnrollmentGroup enrollmentGroup =
                    new EnrollmentGroup(
                            EnrollmentGroupId,
                            attestation)
                    {
                        ProvisioningStatus = ProvisioningStatus.Enabled
                    };
            Console.WriteLine(enrollmentGroup);
            #endregion
    
            #region Create the enrollmentGroup
            Console.WriteLine("\nAdding new enrollmentGroup...");
            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
            #endregion
    
        }
    }
    ```

17. Por último, reemplace el método `Main` por las siguientes líneas:

    ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
    ```

18. Compile la solución.

:::zone-end

:::zone pivot="programming-language-nodejs"

En esta sección, se muestra cómo crear un script de node.js que agregue un grupo de inscripción al servicio de aprovisionamiento.

1. Desde una ventana de comandos en la carpeta de trabajo, ejecute:

     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Con un editor de texto, cree un archivo **create_enrollment_group.js** en la carpeta de trabajo. Agregue el siguiente código al archivo y guárdelo:

    ```javascript
        'use strict';
        var fs = require('fs');
    
        var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;
    
        var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    
        var enrollment = {
          enrollmentGroupId: 'first',
          attestation: {
            type: 'x509',
            x509: {
              signingCertificates: {
                primary: {
                  certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
                }
              }
            }
          },
          provisioningStatus: 'disabled'
        };
    
        serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
          if (err) {
            console.log('error creating the group enrollment: ' + err);
          } else {
            console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
            enrollmentResponse.provisioningStatus = 'enabled';
            serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
              if (err) {
                console.log('error updating the group enrollment: ' + err);
              } else {
                console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
              }
            });
          }
        });
    ```

:::zone-end

:::zone pivot="programming-language-python"

En esta sección se muestra cómo crear un script de Python que agregue un grupo de inscripción al servicio de aprovisionamiento.

1. Con un editor de texto, cree un nuevo archivo *EnrollmentGroup.py*.

2. Copie el siguiente código de Python en el archivo (reemplace `{dpsConnectionString}` con la cadena de conexión que copió anteriormente, el marcador de posición del certificado que cuenta con el certificado creado en el paso [Preparación de certificados de prueba](#prepare-test-certificates) y `{registrationid}` con un valor `registrationid` único que consta solo de caracteres alfanuméricos en minúscula y guiones):

    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism
    
    CONNECTION_STRING = "{dpsConnectionString}"
    
    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""
    
    GROUP_ID = "{registrationid}"

    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

3. Guarde y cierre el archivo **EnrollmentGroup.py**.

:::zone-end

:::zone pivot="programming-language-java"

<a id="runjavasample"></a>

1. En el SDK de IoT de Azure para Java, vaya a la carpeta de ejemplo *_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_*.

2. Abra el archivo *_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_* en el editor que prefiera.

3. Reemplace `[Provisioning Connection String]` por la cadena de conexión que copió en [Obtención de la cadena de conexión del servicio de aprovisionamiento](#get-the-connection-string-for-your-provisioning-service).

4. Reemplace el valor `PUBLIC_KEY_CERTIFICATE_STRING` con el valor del *certificado raíz** que generó en la sección anterior. Asegúrese de reemplazar todo el valor de la muestra, incluidas las líneas **_-----COMENZAR CERTIFICADO-----_** y **_-----FINALIZAR CERTIFICADO-----_**.

5. Para configurar el servicio de aprovisionamiento a partir del código de ejemplo, vaya al paso siguiente. Si decide no configurarlo, asegúrese de que convierte en comentario o elimina las siguientes instrucciones en el archivo _ServiceEnrollmentGroupSample.java_:

    ```Java
    enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
    enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
    ```

6. En este paso se muestra cómo configurar el servicio de aprovisionamiento en el código de ejemplo.

    1. Vaya a [Azure Portal](https://portal.azure.com).

    2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

    3. Seleccione la instancia de Device Provisioning Service.

    4. En el panel **Información general**, copie el nombre de host del *punto de conexión de servicio*.  En el ejemplo de código fuente, reemplace `[Host name]` con el nombre de host que ha copiado.

        ```Java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

7. Estudie el código de ejemplo. Crea, actualiza, consulta y elimina una inscripción de un grupo para dispositivos X.509. Para comprobar si la inscripción se realizó correctamente en el portal, convierta temporalmente en comentario las siguientes líneas de código al final del archivo _ServiceEnrollmentGroupSample.java_:

    ```Java
    // ************************************** Delete info of enrollmentGroup ***************************************
    System.out.println("\nDelete the enrollmentGroup...");
    provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
    ```

8. Guarde el archivo _ServiceEnrollmentGroupSample.java_.

:::zone-end

## <a name="run-the-enrollment-group-sample"></a>Ejecutar el ejemplo del grupo de inscripción

:::zone pivot="programming-language-csharp"

1. Ejecute el ejemplo en Visual Studio para crear el grupo de inscripción. Aparecerá una ventana de comandos y se mostrarán los mensajes de confirmación.

2. Si se realiza una creación correcta, la ventana de comandos muestra las propiedades del grupo de inscripción nuevo.

:::zone-end

:::zone pivot="programming-language-nodejs"

1. Abra un símbolo del sistema y el siguiente comando (incluya las comillas alrededor de los argumentos de comando y reemplace `<connection string>` con la cadena de conexión que copió en la sección anterior y `<certificate .pem file>` con la ruta de acceso del archivo `.pem`):

    ```cmd\sh
    node create_enrollment_group.js "<connection string>" "<certificate .pem file>"
    ```

2. Si se realiza una creación correcta, la ventana de comandos muestra las propiedades del grupo de inscripción nuevo.

:::zone-end

:::zone pivot="programming-language-python"

1. Abra un símbolo del sistema en modo de administrador y ejecute el siguiente comando para instalar [azure-iot-provisioning-device-client](https://pypi.org/project/azure-iot-provisioning-device-client).

    ```cmd/sh
    pip install azure-iothub-provisioningserviceclient    
    ```

2. En el símbolo del sistema, ejecute este script:

    ```cmd/sh
    python EnrollmentGroup.py
    ```

3. Si se realiza una creación correcta, la ventana de comandos muestra las propiedades del grupo de inscripción nuevo.

:::zone-end

:::zone pivot="programming-language-java"

1. Abra una ventana de comandos en modo de administrador y vaya a la carpeta *_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_*.

2. En el símbolo del sistema, use este comando:

    ```cmd\sh
    mvn install -DskipTests
    ```

    Este comando descarga el paquete de Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) a su máquina. El paquete incluye los archivos binarios para el SDK del servicio de Java que debe compilar el código de ejemplo. Si ejecutó la herramienta para _generar certificados X.509_ en la sección anterior, este paquete ya estará descargado en su máquina.

3. En el símbolo del sistema, ejecute este script:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. Si se realiza una creación correcta, la ventana de comandos muestra las propiedades del grupo de inscripción nuevo.

:::zone-end

Compruebe que se ha creado el grupo de inscripción:

1. En Azure Portal, vaya a la instancia de Device Provisioning Service.

2. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

3. Seleccione **Grupos de inscripción**. Verá una nueva entrada de inscripción que corresponde al identificador de registro que se usó en el ejemplo.

:::zone pivot="programming-language-csharp"

:::image type="content" source="./media/quick-enroll-device-x509/verify-enrollment-csharp.png" alt-text="Compruebe la inscripción del grupo de C# en el portal.":::

:::zone-end

:::zone pivot="programming-language-nodejs"

![Propiedades de la inscripción en el portal](media/quick-enroll-device-x509/verify-enrollment-nodejs.png)

:::zone-end

:::zone pivot="programming-language-python"

:::image type="content" source="./media/quick-enroll-device-x509/verify-enrollment-python.png" alt-text="Compruebe la inscripción del grupo de Python en el portal.":::

:::zone-end

:::zone pivot="programming-language-java"

:::image type="content" source="./media/quick-enroll-device-x509/verify-enrollment-java.png" alt-text="Compruebe la inscripción del grupo de Java en el portal.":::

:::zone-end

:::zone pivot="programming-language-java"

## <a name="modifications-to-enroll-a-single-x509-device"></a>Modificaciones para inscribir un único dispositivo X.509

Para inscribir un único dispositivo X.509, modifique el código de ejemplo de la *inscripción individual* que se usa en [Inscripción de un dispositivo de TPM al Servicio IoT Hub Device Provisioning mediante el SDK del servicio de Java](quick-enroll-device-tpm.md) tal y como se indica a continuación:

1. Copie el *nombre común* del certificado de cliente X.509 en el Portapapeles. Si desea utilizar la herramienta para _generar certificados X.509_ tal y como se muestra en la [sección de código de ejemplo anterior](#javasample), escriba un _nombre común_ para el certificado o use el nombre predeterminado **microsoftriotcore**. Use este **nombre común** como el valor para la variable *REGISTRATION_ID*.

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. Cambie el nombre de la variable *TPM_ENDORSEMENT_KEY* a *PUBLIC_KEY_CERTIFICATE_STRING*. Copie su certificado de cliente o el valor de **Client Cert** (certificado de cliente) que obtuvo con la herramienta para _generar certificados X.509_, como el valor de la variable *PUBLIC_KEY_CERTIFICATE_STRING*.

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
    private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
    ```

3. En la función **main**, reemplace la línea `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` con el código siguiente para usar el certificado de cliente X.509:

    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. Guarde, compile y ejecute el archivo de ejemplo de la *inscripción individual*. Para ello, use los pasos de la sección sobre [Creación del código de ejemplo para la inscripción individual](quick-enroll-device-tpm.md).

:::zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

Si planea echar un vistazo a los [tutoriales de Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md), no elimine los recursos que se crearon en esta guía de inicio rápido. De lo contrario, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo en el equipo.

2. En Azure Portal, seleccione **Todos los recursos** en el menú de la izquierda.

3. Seleccione la instancia de Device Provisioning Service.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. Seleccione la pestaña **Grupos de inscripción**.

6. Active la casilla situada junto al campo *ID. DE REGISTRO* del dispositivo que ha inscrito en esta guía de inicio rápido.

7. En la parte superior de la página, seleccione **Eliminar**.

8. En la instancia de Device Provisioning Service de Azure Portal, seleccione **Certificados**.

9. Seleccione el certificado que cargó para esta guía de inicio rápido.

10. En la parte superior de **Detalles del certificado**, seleccione **Eliminar**.  

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado un grupo de inscripción para un certificado X.509 de entidad de certificación intermedio o raíz mediante Azure IoT Hub Device Provisioning Service. Para más información acerca del aprovisionamiento de dispositivos, continúe con el tutorial para instalar el servicio Device Provisioning en Azure Portal.

> [!div class="nextstepaction"]
> [Tutoriales del servicio Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)

:::zone pivot="programming-language-nodejs"

> [!div class="nextstepaction"]
>[Ejemplo de aprovisionamiento de dispositivos Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).

:::zone-end