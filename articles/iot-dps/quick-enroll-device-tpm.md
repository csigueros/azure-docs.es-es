---
title: 'Inicio rápido: Inscripción de un dispositivo individual en Azure Device Provisioning Service mediante la atestación de TPM'
description: 'Inicio rápido: Inscripción de un dispositivo individual en Azure IoT Hub Device Provisioning Service (DPS) mediante la atestación de TPM.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/20/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
zone_pivot_groups: iot-dps-set2
ms.openlocfilehash: d13a4b8224fc5ebe3eccf5f569af0efaed9b4101
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293906"
---
# <a name="quickstart-enroll-individual-device-to-iot-hub-device-provisioning-service-using-tpm-attestation"></a>Inicio rápido: Inscripción de un dispositivo individual en IoT Hub Device Provisioning Service mediante la atestación de TPM

En este artículo se muestra cómo crear mediante programación una inscripción individual para un dispositivo de TPM en Azure IoT Hub Device Provisioning Service mediante el [SDK de Microsoft Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) y una aplicación de ejemplo. Si lo desea, puede inscribir un dispositivo de TPM simulado en el servicio de aprovisionamiento mediante esta entrada de inscripción individual. 

Aunque estos pasos funcionan en equipos Windows y Linux, en este artículo se usa un equipo de desarrollo de Windows.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Realice los pasos que se describen en [Configuración de IoT Hub Device Provisioning Service con Azure Portal](./quick-setup-auto-provision.md).

:::zone pivot="programming-language-csharp"

* Instale [Visual Studio 2019](https://www.visualstudio.com/vs/).

* Instale el [SDK de .NET Core 3.1 o cualquier versión posterior](https://dotnet.microsoft.com/download), en una máquina con Windows. Para comprobar la versión, use el siguiente comando.

    ```bash
    dotnet --info
    ```

* (Opcional) Si quiere inscribir un dispositivo simulado al final de este inicio rápido, siga el procedimiento descrito en [Creación y aprovisionamiento de un dispositivo de TPM simulado](quick-create-simulated-device-tpm.md) hasta llegar al paso donde obtiene una clave de aprobación para el dispositivo. Guarde la clave de aprobación, el id. del registro y, opcionalmente, el id. del dispositivo.

:::zone-end

:::zone pivot="programming-language-nodejs"

* Instale [Node.js v4.0+](https://nodejs.org).

* (Opcional) Cree una clave de aprobación. Siga los pasos descritos en [Creación y aprovisionamiento de un dispositivo simulado](quick-create-simulated-device-tpm.md) hasta que obtenga la clave.

:::zone-end

:::zone pivot="programming-language-python"

* Descargue e instale [Python 2.x o 3.x](https://www.python.org/downloads/). Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a las variables de entorno específicas de la plataforma. 

* Instale [PIP](https://pip.pypa.io/en/stable/installing/), si no se incluye con la distribución de Python.

> [!IMPORTANT]
> Este artículo solo se aplica al SDK de Python v1 en desuso. El dispositivo y los clientes del servicio para IoT Hub Device Provisioning Service aún no están disponibles en la versión 2. Actualmente, el equipo está haciendo todo lo posible para incorporar la versión 2 a la paridad de características.

:::zone-end

:::zone pivot="programming-language-java"

* Instale [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure). En este inicio rápido se instala el [SDK de servicio de Java](https://azure.github.io/azure-iot-sdk-java/master/service/) siguiente. Funciona tanto en Windows como en Linux. En este inicio rápido se usa Windows.

* Instale [Maven 3](https://maven.apache.org/download.cgi).

* Instale [Git](https://git-scm.com/download/) y asegúrese de que la ruta de acceso se agrega a la variable de entorno `PATH`.


:::zone-end

> [!NOTE]
> No siga los pasos para crear una inscripción individual mediante Azure Portal.


:::zone pivot="programming-language-python"

<a id="prepareenvironment"></a>

## <a name="prepare-the-development-environment"></a>Preparación del entorno de desarrollo

* Para descargar e instalar el [SDK del servicio de aprovisionamiento de Python](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client), elija una de las siguientes opciones:
    
    - Cree y compile el **SDK de Python de Azure IoT**. Siga [estas instrucciones](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) para compilar los paquetes de Python. Si usa el sistema operativo Windows, instale igualmente el [paquete redistribuible de Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para permitir el uso de archivos DLL nativos de Python.
    
    - [Instale o actualice *pip*, el sistema de administración de paquetes de Python](https://pip.pypa.io/en/stable/installing/) e instale el paquete mediante el comando siguiente:
        
        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

### <a name="copy-symmetric-and-tpm-enrollment-keys"></a>Copia de claves de inscripción simétricas y de TPM

En esta guía de inicio rápido se muestran las inscripciones de clave simétrica y TPM. Deberá crear las suyas propias o usar las claves de prueba proporcionadas:
 
# <a name="symmetric-key"></a>[Clave simétrica](#tab/symmetrickey)

En las inscripciones de dispositivos de clave simétrica simuladas, necesita una clave principal y una secundaria para el dispositivo. Si no tiene ninguna clave simétrica válida, puede usar las siguientes claves de ejemplo:

*Clave simétrica principal*

```
UmorGiEVPNIQuaWGXXbe8v9gWayS7XtOZmNMo6DEaEXP65GvhuK3OeRf8RVZ9BymBCHxNg3oRTey0pUHUwwYKQ==
```

*Clave simétrica secundaria*

```
Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
```

# <a name="tpm"></a>[TPM](#tab/tpm)

En las inscripciones de TPM, necesita la clave de aprobación del dispositivo. Si siguió la guía de inicio rápido [Creación y aprovisionamiento de un dispositivo simulado](quick-create-simulated-device-tpm.md) para crear un dispositivo de TPM simulado, use la clave que se creó para dicho dispositivo. En caso contrario, puede usar la siguiente clave de aprobación proporcionada con el SDK:

```
AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
```

---

:::zone-end


:::zone pivot="programming-language-java"

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Preparación del entorno de desarrollo 

### <a name="set-up-environment-variables"></a>Configurar las variables de entorno

Para configurar las variables de entorno:

1. La variable `PATH` debería incluir la ruta de acceso completa al directorio *jdk1.8.x\bin*. Si esta es la primera instalación de Java de su máquina, cree una nueva variable de entorno con el nombre `JAVA_HOME` y haga que señale a la ruta de acceso completa del directorio *jdk1.8.x*. En una máquina con Windows, este directorio se encuentra en la carpeta *C:\\Archivos de programa\\Java\\* . Puede crear o modificar las variables de entorno si busca **Editar las variables de entorno del sistema** en el **Panel de control** de su máquina Windows.

    Para comprobar si Java está correctamente configurado en la máquina, ejecute el comando siguiente en la ventana de comandos:

    ```cmd\sh
    java -version
    ```

2. Modifique la variable de entorno `PATH` para que apunte a la carpeta *apache-maven-3.x.x\\bin* dentro de la carpeta donde se extrae Maven. Para confirmar que Maven está instalado correctamente, ejecute este comando en la ventana Comandos:

    ```cmd\sh
    mvn --version
    ```

3. Asegúrese de que [GIT](https://git-scm.com/download/) esté instalado en su máquina y de que se agregue a la variable de entorno `PATH`.

### <a name="clone-git-repository-for-azure-iot-java-sdk"></a>Clonación del repositorio de Git para el SDK de Azure IoT para Java

Para clonar del SDK de Azure IoT para Java:

1. Abra un símbolo del sistema. 

2. Clone el repositorio de GitHub para código de ejemplo de inscripción de dispositivos mediante el [SDK del servicio de Java](https://azure.github.io/azure-iot-sdk-java/master/service/):

    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

:::zone-end

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Obtención de la cadena de conexión para el servicio de aprovisionamiento

En el ejemplo de esta guía de inicio rápido, necesitará copiar la cadena de conexión del servicio de aprovisionamiento.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service.

4. En el menú **Configuración**, seleccione **Directivas de acceso compartidas**.

5. Seleccione la directiva de acceso que quiera usar.

6. En el panel **Directiva de acceso**, copie y guarde la cadena de conexión de la clave principal.

    :::image type="content" source="./media/quick-enroll-device-tpm/get-service-connection-string.png" alt-text="Obtenga la cadena de conexión del servicio de aprovisionamiento desde el portal.":::

## <a name="create-the-individual-enrollment-sample"></a>Creación del ejemplo de inscripción individual

:::zone pivot="programming-language-csharp"

En esta sección se muestra cómo crear una aplicación de consola de .NET Core que agrega una inscripción individual para un dispositivo de TPM al servicio de aprovisionamiento. Con algunas modificaciones, también puede seguir estos pasos para crear una aplicación de consola de [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) para agregar la inscripción individual. Para más información sobre el desarrollo con IoT Core, consulte la [documentación para desarrolladores de Windows IoT Core](/windows/iot-core/).

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

    ![Ventana del Administrador de paquetes NuGet](media//quick-enroll-device-tpm/add-nuget.png)

    Este paso descarga, instala y agrega una referencia al paquete NuGet del [SDK de cliente del servicio de aprovisionamiento de Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) y sus dependencias.

11. Agregue las siguientes instrucciones `using` después de las demás instrucciones `using` en la parte superior de `Program.cs`:
  
    ```csharp
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

12. Agregue los siguientes campos a la clase `Program` y realice los cambios de la lista.  

    ```csharp
    private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
    private const string RegistrationId = "sample-registrationid-csharp";
    private const string TpmEndorsementKey =
        "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
        "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
        "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
        "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
        "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
        
    // Optional parameters
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

13. Reemplace el valor del marcador de posición `ProvisioningServiceConnectionString` con la cadena de conexión del servicio de aprovisionamiento que copió en la sección anterior.

14. Si usa este inicio rápido junto con el de [Creación y aprovisionamiento de un dispositivo de TPM simulado](quick-create-simulated-device-tpm.md) para aprovisionar un dispositivo simulado, reemplace la clave de aprobación y el id. de registro por los valores que anotó en ese inicio rápido. Puede reemplazar el identificador del dispositivo por el valor sugerido en ese inicio rápido, usar su propio valor o utilizar el valor predeterminado en este ejemplo.

15. Agregue el siguiente método a la clase `Program`.  Este código crea una entrada de inscripción individual y luego llama al método `CreateOrUpdateIndividualEnrollmentAsync` en `ProvisioningServiceClient` para agregar la inscripción individual al servicio de aprovisionamiento.

    ```csharp
    public static async Task RunSample()
    {
        Console.WriteLine("Starting sample...");
    
        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
        {
            #region Create a new individualEnrollment config
            Console.WriteLine("\nCreating a new individualEnrollment...");
            Attestation attestation = new TpmAttestation(TpmEndorsementKey);
            IndividualEnrollment individualEnrollment =
                    new IndividualEnrollment(
                            RegistrationId,
                            attestation);
    
            // The following parameters are optional. Remove them if you don't need them.
            individualEnrollment.DeviceId = OptionalDeviceId;
            individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
            #endregion
    
            #region Create the individualEnrollment
            Console.WriteLine("\nAdding new individualEnrollment...");
            IndividualEnrollment individualEnrollmentResult =
                await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
            Console.WriteLine("\nIndividualEnrollment created with success.");
            Console.WriteLine(individualEnrollmentResult);
            #endregion
        
        }
    }
    ```

16. Por último, reemplace el método `Main` por las siguientes líneas:

    ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
    ```

17. Compile la solución.

:::zone-end

:::zone pivot="programming-language-nodejs"

1. Desde una ventana de comandos en la carpeta de trabajo, ejecute:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Con un editor de texto, cree un archivo _create_individual_enrollment.js_ en la carpeta de trabajo. Agregue el siguiente código al archivo:

    ```Java
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

3. Guarde el archivo.

:::zone-end

:::zone pivot="programming-language-python"

1. Con un editor de texto, cree un nuevo archivo *Enrollment.py*.

2. Agregue las siguientes `import`instrucciones y variables al inicio del archivo *Enrollment.py* (reemplace `{dpsConnectionString}` con la cadena de conexión que copió anteriormente, el marcador de posición del certificado con el certificado que creó en [Copia simétrica y claves de inscripción de TPM](#copy-symmetric-and-tpm-enrollment-keys), y `{registrationid}` con un valor `registrationid` exclusivo que consta solo de caracteres alfanuméricos en minúscula y guiones):


    # <a name="symmetric-key"></a>[Clave simétrica](#tab/symmetrickey)

    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism
    from provisioningserviceclient.protocol.models import SymmetricKeyAttestation

    CONNECTION_STRING = "Enter your DPS connection string"
    PRIMARY_KEY = "Add a valid key"
    SECONDARY_KEY = "Add a valid key"
    REGISTRATION_ID = "Enter a registration ID"
    ```

    # <a name="tpm"></a>[TPM](#tab/tpm)
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "Enter your DPS connection string"
    ENDORSEMENT_KEY = "Enter the endorsement key for your device"
    REGISTRATION_ID = "Enter a registration ID"
    ```

    ---

3. Agregue el siguiente código de Python para implementar la creación de la inscripción individual:

     # <a name="symmetric-key"></a>[Clave simétrica](#tab/symmetrickey)

    ```python
    def main():
        print ( "Starting individual enrollment..." )
    
        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
    
        symAtt = SymmetricKeyAttestation(primary_key=PRIMARY_KEY, secondary_key=SECONDARY_KEY)
        att = AttestationMechanism(type="symmetricKey", symmetric_key=symAtt)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)
    
        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

    # <a name="tpm"></a>[TPM](#tab/tpm)

    ```python
    def main():
        print ( "Starting individual enrollment..." )
    
        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
    
        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)
    
        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

    ---

4. Guarde y cierre el archivo *Enrollment.py*.

:::zone-end

:::zone pivot="programming-language-java"

1. En el código fuente descargado, navegue hasta la carpeta de ejemplo *_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_* . Abra el archivo *_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_*.

2. Reemplace `[Provisioning Connection String]` por la cadena de conexión que copió en [Obtención de la cadena de conexión del servicio de aprovisionamiento](#get-the-connection-string-for-your-provisioning-service).

    ```Java
    private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
    ```

   2. Agregue los detalles del dispositivo de TPM:
       1. Obtenga el *id. de registro* y la *clave de aprobación del TPM* para simular el dispositivo de TPM. Para ello, siga los pasos que conducen a la sección [Simulate TPM device](quick-create-simulated-device-tpm.md#simulatetpm) (Simulación del dispositivo de TPM).
       2. Use el **_id. de registro_** y la **_clave de aprobación_** desde la salida del paso anterior para reemplazar los elementos `[RegistrationId]` y `[TPM Endorsement Key]` del archivo de código de ejemplo **_ServiceEnrollmentSample.java_** :
    
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

3. Para configurar el servicio de aprovisionamiento a partir del código de ejemplo, vaya al paso siguiente. Si decide no configurarlo, asegúrese de que convierte en comentario o elimina las siguientes instrucciones en el archivo _ServiceEnrollmentSample.java_:

    ```Java
    / / The following parameters are optional. Remove it if you don't need.
    individualEnrollment.setDeviceId(DEVICE_ID);
    individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
    individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
    ```

4. En este paso se muestra cómo configurar el servicio de aprovisionamiento en el código de ejemplo.

    1. Vaya a [Azure Portal](https://portal.azure.com).

    2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

    3. Seleccione la instancia de Device Provisioning Service.

    4. En el panel **Información general**, copie el nombre de host del *punto de conexión de servicio*.  En el ejemplo de código fuente, reemplace `[Host name]` con el nombre de host que ha copiado.

    ```Java
    private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
    ```

5. Estudie el código de ejemplo. Crea, actualiza, consulta y elimina una inscripción de un dispositivo de TPM individual. Para comprobar si la inscripción se realizó correctamente en el portal, convierta temporalmente en comentario las siguientes líneas de código al final del archivo _ServiceEnrollmentSample.java_:

    ```Java
    // *********************************** Delete info of individualEnrollment ************************************
    System.out.println("\nDelete the individualEnrollment...");
    provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
    ```

6. Guarde el archivo _ServiceEnrollmentSample.java_.

:::zone-end

## <a name="run-the-individual-enrollment-sample"></a>Ejecución del ejemplo de inscripción individual

:::zone pivot="programming-language-csharp"

1. Ejecute el ejemplo en Visual Studio para crear la inscripción. Aparecerá una ventana de comandos y se mostrarán los mensajes de confirmación.

2. Tras una creación correcta, la ventana de comandos muestra las propiedades dela nueva inscripción.

:::zone-end

:::zone pivot="programming-language-nodejs"

Para ejecutar el ejemplo, necesitará la cadena de conexión para el servicio de aprovisionamiento que copió en la sección anterior, así como la clave de aprobación del dispositivo. Si siguió la guía de inicio rápido [Creación y aprovisionamiento de un dispositivo simulado](quick-create-simulated-device-tpm.md) para crear un dispositivo de TPM simulado, use la clave que se creó para dicho dispositivo. En caso contrario, para crear una inscripción individual de ejemplo, puede usar la siguiente clave de aprobación proporcionada con el [SDK de servicio de Node.js](https://github.com/Azure/azure-iot-sdk-node):

```bash
AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
```

1. Para crear una inscripción individual para el dispositivo de TPM, ejecute el comando siguiente (incluidas las comillas alrededor de los argumentos de comando):

     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```

2. Tras una creación correcta, la ventana de comandos muestra las propiedades dela nueva inscripción.

:::zone-end

:::zone pivot="programming-language-python"

1. Abra un símbolo del sistema y ejecute el script siguiente.

    ```cmd/sh
    python Enrollment.py
    ```

2. Tras una creación correcta, la ventana de comandos muestra las propiedades dela nueva inscripción.

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

4. Tras una creación correcta, la ventana de comandos muestra las propiedades dela nueva inscripción.


:::zone-end

Compruebe que se ha creado el grupo de inscripción:

1. En Azure Portal, vaya a la instancia de Device Provisioning Service.

2. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

3. Seleccione **Inscripciones individuales**. Verá una nueva entrada de inscripción que corresponde al identificador de registro que se usó en el ejemplo.

:::zone pivot="programming-language-csharp"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-csharp.png" alt-text="Compruebe la inscripción de un dispositivo individual de C# en el portal.":::

:::zone-end

:::zone pivot="programming-language-nodejs"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-nodejs.png" alt-text="Compruebe la inscripción de un dispositivo individual de Node.js en el portal.":::

:::zone-end

:::zone pivot="programming-language-python"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-python.png" alt-text="Compruebe la inscripción de un dispositivo individual de Python en el portal.":::

:::zone-end

:::zone pivot="programming-language-java"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-java.png" alt-text="Compruebe la inscripción de un dispositivo individual de Java en el portal.":::

:::zone-end


## <a name="clean-up-resources"></a>Limpieza de recursos

Si planea echar un vistazo a los [tutoriales de Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md), no elimine los recursos que se crearon en esta guía de inicio rápido. De lo contrario, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo en el equipo.

2. En Azure Portal, seleccione **Todos los recursos** en el menú de la izquierda.

3. Seleccione la instancia de Device Provisioning Service.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. Seleccione la pestaña **Inscripciones individuales**.

6. Active la casilla situada junto al campo *ID. DE REGISTRO* del dispositivo que ha inscrito en esta guía de inicio rápido.

7. En la parte superior de la página, seleccione **Eliminar**.

8. En la instancia de Device Provisioning Service de Azure Portal, seleccione **Certificados**.

9. Seleccione el certificado que cargó para esta guía de inicio rápido.

10. En la parte superior de **Detalles del certificado**, seleccione **Eliminar**.  

11. Si ha seguido los pasos del inicio rápido [Creación y aprovisionamiento de un dispositivo de TPM simulado](quick-create-simulated-device-tpm.md) para crear un dispositivo de TPM simulado, haga lo siguiente:

    1. Cierre la ventana del simulador de TPM y la ventana de salida de ejemplo para el dispositivo simulado.

    2. En Azure Portal, vaya a la instancia de IoT Hub donde se aprovisionó el dispositivo. 

    3. En el menú de la opción **Exploradores**, seleccione **Dispositivos de IoT**.

    4. Seleccione la casilla situada junto al campo *ID. DE DISPOSITIVO* del dispositivo que registró en esta guía de inicio rápido.

    5. Seleccione **Eliminar** en la parte superior del panel.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado mediante programación una entrada de inscripción individual para un dispositivo de TPM. Opcionalmente, ha creado un dispositivo simulado de TPM en su equipo y lo ha aprovisionado con el centro de IoT mediante Azure IoT Hub Device Provisioning Service. Para más información acerca del aprovisionamiento de dispositivos, continúe con el tutorial para instalar el servicio Device Provisioning en Azure Portal.

> [!div class="nextstepaction"]
> [Tutoriales del servicio Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)