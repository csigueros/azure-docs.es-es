---
title: Conexión de dispositivos con certificados X.509 en una aplicación de Azure IoT Central
description: Procedimiento para conectar dispositivos con certificados X.509 mediante el SDK de dispositivo de Node.js para la aplicación de IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 06/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 2a9cf8f74410f69d95b0f6944025220de2c94c08
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113588154"
---
# <a name="how-to-connect-devices-with-x509-certificates-to-iot-central-application"></a>Conexión de dispositivos con certificados X.509 para una aplicación de IoT Central

IoT Central admite firmas de acceso compartido (SAS) y certificados X.509 para proteger la comunicación entre un dispositivo y la aplicación. En el tutorial [Creación y conexión de un aplicación cliente a una aplicación de Azure IoT Central](./tutorial-connect-device.md) se usa SAS. En este artículo, aprenderá a modificar el ejemplo de código para usar certificados X.509. Los certificados X.509 se recomiendan en entornos de producción. Para obtener más información, consulte [Conexión a Azure IoT Central](./concepts-get-connected.md).

En esta guía, se muestran dos formas de usar certificados X.509: en [inscripciones de grupo](how-to-connect-devices-x509.md#use-group-enrollment), que se usan normalmente en un entorno de producción, y en [inscripciones individuales](how-to-connect-devices-x509.md#use-individual-enrollment), que resultan útiles para las pruebas. En el artículo también se describe cómo [implementar certificados de dispositivo](#roll-x509-device-certificates) para mantener la conectividad cuando expiran los certificados.

Esta guía se basa en los ejemplos que se muestran en el tutorial [Creación y conexión de un aplicación cliente a la aplicación de Azure IoT Central](tutorial-connect-device.md), que usan C#, Java, JavaScript y Python. Para obtener un ejemplo en el que se usa el lenguaje de programación C, vea [Aprovisionamiento de varios dispositivos X.509 mediante grupos de inscripción](../../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md).

## <a name="prerequisites"></a>Requisitos previos

Complete el tutorial [Creación y conexión de un aplicación cliente a la aplicación de Azure IoT Central](./tutorial-connect-device.md). Esto incluye la instalación de los requisitos previos para elegir el lenguaje de programación.

En esta guía paso a paso, generará algunos certificados X.509 de prueba. Para poder generar estos certificados, necesita lo siguiente:

- Una máquina de desarrollo con la versión 6 de [Node.js](https://nodejs.org/) o una posterior instalada. Puede ejecutar `node --version` en la línea de comandos para comprobar la versión. En las instrucciones de este tutorial se da por hecho que se ejecuta el comando **node** en el símbolo del sistema de Windows. No obstante, puede usar Node.js en muchos otros sistemas operativos.
- Una copia local del repositorio de GitHub de [SDK de Microsoft Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node) que contiene los scripts para generar los certificados X.509 de prueba. Use este vínculo para descargar una copia del repositorio: [Descargar el archivo ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Después, descomprima el archivo en una ubicación conveniente en la máquina local.

## <a name="use-group-enrollment"></a>Uso de la inscripción de grupo

Use certificados X.509 con una inscripción de grupo en un entorno de producción. En una inscripción de grupo, agregue un certificado X.509 raíz o intermedio a la aplicación de IoT Central. Los dispositivos con certificados hoja derivados del certificado raíz o intermedio pueden conectarse a la aplicación.

### <a name="generate-root-and-device-certificates"></a>Generación de certificados raíz y de dispositivo

En esta sección, se usa un certificado X.509 para conectar un dispositivo con un certificado derivado del certificado del grupo de inscripciones de IoT Central.

> [!WARNING]
> Esta forma de generar certificados X.509 es solo para pruebas. En un entorno de producción, debe usar su mecanismo oficial y seguro para la generación de certificados.

1. Vaya al script del generador de certificados en el SDK de Microsoft Azure IoT para Node.js que descargó. Instale los paquetes necesarios:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Cree un certificado raíz y luego derive un certificado de dispositivo mediante la ejecución del script:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Un identificador de dispositivo puede contener letras, números y el carácter `-`.

Estos comandos generan los siguientes certificados raíz y de dispositivo:

| filename | contenido |
| -------- | -------- |
| mytestrootcert_cert.pem | Parte pública del certificado X.509 raíz |
| mytestrootcert_key.pem | Clave privada del certificado X.509 raíz |
| mytestrootcert_fullchain.pem | Cadena de claves completa del certificado X.509 raíz |
| sampleDevice01_cert.pem | Parte pública del certificado X.509 de dispositivo |
| sampleDevice01_key.pem | Clave privada del certificado X.509 de dispositivo |
| sampleDevice01_fullchain.pem | Cadena de claves completa del certificado X.509 de dispositivo |

Tome nota de la ubicación de estos archivos. Lo necesitará más adelante.

### <a name="create-a-group-enrollment"></a>Creación de una inscripción de grupo

1. Abra la aplicación de IoT Central, vaya a **Administración**, en el panel izquierdo, y seleccione **Conexión del dispositivo**.

1. Seleccione **+ Agregar grupo de inscripciones** y cree uno nuevo con el nombre _MyX509Group_ y el tipo de atestación **Certificados (X.509)** .

1. Abra el grupo de inscripciones que ha creado y seleccione **Administrar principal**.

1. Seleccione la opción de archivo para cargar el archivo de certificado raíz llamado _mytestrootcert_cert.pem_ generado anteriormente:

    ![Carga de certificados](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Para completar la comprobación, genere el código de verificación, cópielo y úselo para crear un certificado de verificación X.509 en el símbolo del sistema:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Seleccione **Comprobar** para cargar el certificado de verificación firmado _verification_cert.pem_ para completar la comprobación:

    ![Certificado comprobado](./media/how-to-connect-devices-x509/verified.png)

Ahora podrá conectar los dispositivos que tengan un certificado X.509 derivado de este certificado raíz principal.

Después de guardar el grupo de inscripciones, tome nota del ámbito de identificador.

### <a name="run-sample-device-code&quot;></a>Ejecución del código del dispositivo de ejemplo

:::zone pivot=&quot;programming-language-csharp&quot;

Si usa Windows, los certificados X.509 deben estar en el almacén de certificados de Windows para que el ejemplo funcione. Para agregar los certificados al almacén:

1. Use `openssl` para crear archivos PFX a partir de los archivos PEM. Al ejecutar estos comandos, se le pedirá que cree una contraseña. Tome nota de la contraseña, ya que la necesitará en el próximo paso:

    ```bash
    openssl pkcs12 -inkey sampleDevice001_key.pem -in sampleDevice001_cert.pem -export -out sampledevice001.pfx
    openssl pkcs12 -inkey mytestrootcert_key.pem -in mytestrootcert_cert.pem -export -out mytestrootcert.pfx
    ```

1. En el Explorador de Windows, haga doble clic en cada archivo PFX. En el **Asistente para importar certificados**, seleccione **Usuario actual** como ubicación del almacén, escriba la contraseña del paso anterior y deje que el asistente elija automáticamente el almacén de certificados. El asistente importa los certificados en el almacén personal del usuario actual.

Para modificar el código de ejemplo para usar los certificados:

1. En la solución de Visual Studio **IoTHubDeviceSamples**, abra el archivo *Parameter.cs* en el proyecto **TemperatureController**.

1. Agregue las dos definiciones de parámetros siguientes a la clase:

    ```csharp
    [Option(
        'x',
        &quot;CertificatePath&quot;,
        HelpText = &quot;(Required if DeviceSecurityType is \"dps\"). \nThe device PFX file to use during device provisioning." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_CERT\".")]
    public string CertificatePath { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_CERT");

    [Option(
        'p',
        "CertificatePassword",
        HelpText = "(Required if DeviceSecurityType is \"dps\"). \nThe password of the PFX certificate file." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_PASSWORD\".")]
    public string CertificatePassword { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_PASSWORD");
    ```

    Guarde los cambios.

1. En la solución de Visual Studio **IoTHubDeviceSamples**, abra el archivo *Program.cs* en el proyecto **TemperatureController**.

1. Agregue las instrucciones siguientes `using` :

    ```csharp
    using System.Security.Cryptography.X509Certificates;
    using System.IO;
    ```

1. Agregue el método siguiente a la clase:

    ```csharp
    private static X509Certificate2 LoadProvisioningCertificate(Parameters parameters)
    {
        var certificateCollection = new X509Certificate2Collection();
        certificateCollection.Import(
            parameters.CertificatePath,
            parameters.CertificatePassword,
            X509KeyStorageFlags.UserKeySet);

        X509Certificate2 certificate = null;

        foreach (X509Certificate2 element in certificateCollection)
        {
            Console.WriteLine($"Found certificate: {element?.Thumbprint} {element?.Subject}; PrivateKey: {element?.HasPrivateKey}");
            if (certificate == null && element.HasPrivateKey)
            {
                certificate = element;
            }
            else
            {
                element.Dispose();
            }
        }

        if (certificate == null)
        {
            throw new FileNotFoundException($"{parameters.CertificatePath} did not contain any certificate with a private key.");
        }

        Console.WriteLine($"Using certificate {certificate.Thumbprint} {certificate.Subject}");

        return certificate;
    }
    ```

1. En el método `SetupDeviceClientAsync`, sustituya el bloque de código de `case "dps"` por el código siguiente:

    ```csharp
    case "dps":
        s_logger.LogDebug($"Initializing via DPS");
        Console.WriteLine($"Loading the certificate...");
        X509Certificate2 certificate = LoadProvisioningCertificate(parameters);
        DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, certificate, cancellationToken);
        var authMethod = new DeviceAuthenticationWithX509Certificate(dpsRegistrationResult.DeviceId, certificate);
        deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
        break;
    ```

1. Reemplace el método `ProvisionDeviceAsync` con el código siguiente:

    ```csharp
    private static async Task<DeviceRegistrationResult> ProvisionDeviceAsync(Parameters parameters, X509Certificate2 certificate, CancellationToken cancellationToken)
    {
        SecurityProvider security = new SecurityProviderX509Certificate(certificate);
        ProvisioningTransportHandler mqttTransportHandler = new ProvisioningTransportHandlerMqtt();
        ProvisioningDeviceClient pdc = ProvisioningDeviceClient.Create(parameters.DpsEndpoint, parameters.DpsIdScope, security, mqttTransportHandler);

        var pnpPayload = new ProvisioningRegistrationAdditionalData
        {
            JsonData = PnpConvention.CreateDpsPayload(ModelId),
        };
        return await pdc.RegisterAsync(pnpPayload, cancellationToken);
    }
    ```

    Guarde los cambios.

1. Agregue las siguientes variables de entorno al proyecto:

    - `IOTHUB_DEVICE_X509_CERT`: `<full path to folder that contains PFX files>sampleDevice01.pfx`
    - `IOTHUB_DEVICE_X509_PASSWORD`: la contraseña que utilizó para crear el archivo *sampleDevice01.pfx*.

1. Compile y ejecute la aplicación. Compruebe que el dispositivo se aprovisiona correctamente.

:::zone-end

:::zone pivot="programming-language-java"

1. Vaya a la carpeta _azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample_ que contiene el archivo *pom.xml* y la carpeta *src* para el ejemplo de dispositivo del controlador de temperatura.

1. Edite el archivo *pom.xml* para agregar la siguiente configuración de dependencias en el nodo `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.sdk.iot.provisioning.security</groupId>
        <artifactId>${x509-provider-artifact-id}</artifactId>
        <version>${x509-provider-version}</version>
    </dependency>
    ```

    Guarde los cambios.

1. Abra el archivo *src/main/java/samples/com/microsoft/azure/sdk/iot/device/TemperatureController.java* en el editor de texto.

1. Reemplace la importación de `SecurityProviderSymmetricKey` por las siguientes importaciones:

    ```java
    import com.microsoft.azure.sdk.iot.provisioning.security.SecurityProvider;
    import com.microsoft.azure.sdk.iot.provisioning.security.hsm.SecurityProviderX509Cert;
    import com.microsoft.azure.sdk.iot.provisioning.security.exceptions.SecurityProviderException;
    ```

1. Agregue la siguiente importación:

    ```java
    import java.nio.file.*;
    ```

1. Agregue `SecurityProviderException` a la lista de excepciones que el método `main` genera:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, ProvisioningDeviceClientException, InterruptedException, SecurityProviderException {
    ```

1. Reemplace el método `initializeAndProvisionDevice` con el código siguiente:

    ```java
    private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException, SecurityProviderException {
        String deviceX509Key = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_KEY"))));
        String deviceX509Cert = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_CERT"))));
        SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(deviceX509Cert, deviceX509Key, null);
        ProvisioningDeviceClient provisioningDeviceClient;
        ProvisioningStatus provisioningStatus = new ProvisioningStatus();

        provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityProviderX509);

        AdditionalData additionalData = new AdditionalData();
        additionalData.setProvisioningPayload(com.microsoft.azure.sdk.iot.provisioning.device.plugandplay.PnpHelper.createDpsPayload(MODEL_ID));

        provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

        while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
        {
            if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
            {
                provisioningStatus.exception.printStackTrace();
                System.out.println("Registration error, bailing out");
                break;
            }
            System.out.println("Waiting for Provisioning Service to register");
            Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
        }

        ClientOptions options = new ClientOptions();
        options.setModelId(MODEL_ID);

        if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
            System.out.println("IotHUb Uri : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri());
            System.out.println("Device ID : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId());

            String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
            String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

            log.debug("Opening the device client.");
            deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityProviderX509, IotHubClientProtocol.MQTT, options);
            deviceClient.open();
        }
    }
    ```

    Guarde los cambios.

1. En el entorno de shell, agregue las dos variables de entorno siguientes. Asegúrese de proporcionar la ruta de acceso completa a los archivos PEM y use el delimitador de ruta de acceso correcto para el sistema operativo:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > Establezca las otras dos variables de entorno necesarias cuando complete el tutorial [Creación y conexión de un aplicación cliente a una aplicación de Azure IoT Central](./tutorial-connect-device.md).

1. Compile y ejecute la aplicación. Compruebe que el dispositivo se aprovisiona correctamente.

:::zone-end

:::zone pivot="programming-language-javascript"

1. Vaya a la carpeta _azure-iot-sdk-node/device/samples/pnp_ que contiene la aplicación **pnpTemperatureController.js** y ejecute el siguiente comando para instalar el paquete X.509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Abra el archivo **pnpTemperatureController.js** en un editor de texto.

1. Edite las instrucciones `require` para incluir el código siguiente:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Agregue las cuatro líneas siguientes a la sección "DPS connection information" (Información de conexión de DPS) para inicializar la variable `deviceCert`:

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Edite la función `provisionDevice` que crea el cliente; para ello, reemplace la primera línea por el código siguiente:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. En la misma función, modifique la línea que establece la variable `deviceConnectionString` como se indica a continuación:

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. En la función `main`, agregue la siguiente línea después de la línea que llama a `Client.fromConnectionString`:

    ```javascript
    client.setOptions(deviceCert);
    ```

    Guarde los cambios.

1. En el entorno de shell, agregue las dos variables de entorno siguientes. Asegúrese de proporcionar la ruta de acceso completa a los archivos PEM y use el delimitador de ruta de acceso correcto para el sistema operativo:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > Establezca las otras dos variables de entorno necesarias cuando complete el tutorial [Creación y conexión de un aplicación cliente a una aplicación de Azure IoT Central](./tutorial-connect-device.md).

1. Ejecute el script y compruebe que el dispositivo se ha aprovisionado correctamente:

    ```cmd/sh
    node simple_thermostat.js
    ```

:::zone-end

:::zone pivot="programming-language-python"

1. Vaya a la carpeta _azure-iot-device/samples/pnp_ y abra el archivo **temp_controller_with_thermostats.py** en un editor de texto.

1. Use la siguiente instrucción `from` para importar la funcionalidad de X.509:

    ```python
    from azure.iot.device import X509
    ```

1. Modifique la primera parte de la función `provision_device` como se muestra a continuación:

    ```python
    async def provision_device(provisioning_host, id_scope, registration_id, x509, model_id):
        provisioning_device_client = ProvisioningDeviceClient.create_from_x509_certificate(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            x509=x509,
        )
    ```

1. En la función `main`, reemplace la línea que establece la variable `symmetric_key` por el código siguiente:

    ```python
    x509 = X509(
        cert_file=os.getenv("IOTHUB_DEVICE_X509_CERT"),
        key_file=os.getenv("IOTHUB_DEVICE_X509_KEY"),
    )
    ```

1. En la función `main`, reemplace la llamada a la función `provision_device` por el código siguiente:

    ```python
    registration_result = await provision_device(
        provisioning_host, id_scope, registration_id, x509, model_id
    )
    ```

1. En la función `main`, reemplace la llamada a la función `IoTHubDeviceClient.create_from_symmetric_key` por el código siguiente:

    ```python
    device_client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=registration_result.registration_state.assigned_hub,
        device_id=registration_result.registration_state.device_id,
        product_info=model_id,
    )
    ```

    Guarde los cambios.

1. En el entorno de shell, agregue las dos variables de entorno siguientes. Asegúrese de proporcionar la ruta de acceso completa a los archivos PEM y use el delimitador de ruta de acceso correcto para el sistema operativo:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > Establezca las otras dos variables de entorno necesarias cuando complete el tutorial [Creación y conexión de un aplicación cliente a una aplicación de Azure IoT Central](./tutorial-connect-device.md).

1. Ejecute el script y compruebe que el dispositivo se ha aprovisionado correctamente:

    ```cmd/sh
    python temp_controller_with_thermostats.py
    ```

:::zone-end

Compruebe que la telemetría aparece en la vista del dispositivo en la aplicación de IoT Central:

![Captura de pantalla que muestra la telemetría que llega a la aplicación de IoT Central.](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-individual-enrollment"></a>Uso de la inscripción individual

Use certificados X.509 con una inscripción individual para probar el dispositivo y la solución. En una inscripción individual, no hay ningún certificado X.509 intermedio o raíz en la aplicación de IoT Central. Los dispositivos usan un certificado X.509 autofirmado para conectarse a la aplicación.

### <a name="generate-self-signed-device-certificate"></a>Generación de un certificado de dispositivo autofirmado

En esta sección, se usa un certificado X.509 autofirmado para conectar dispositivos para la inscripción individual; su uso permite la inscripción de un único dispositivo. Los certificados autofirmados son solo para pruebas.

> [!WARNING]
> Esta forma de generar certificados X.509 es solo para pruebas. En un entorno de producción, debe usar su mecanismo oficial y seguro para la generación de certificados.

Cree un certificado X.509 autofirmado de dispositivo mediante la ejecución de los siguientes comandos:

```cmd/sh
  cd azure-iot-sdk-node/provisioning/tools
  node create_test_cert.js device mytestselfcertprimary
  node create_test_cert.js device mytestselfcertsecondary 
```

> [!TIP]
> Un identificador de dispositivo puede contener letras, números y el carácter `-`.

### <a name="create-individual-enrollment"></a>Creación de una inscripción individual

1. En la aplicación de Azure IoT Central, seleccione **Dispositivos** y cree otro dispositivo con _mytestselfcertprimary_ como **Id. de dispositivo** en la plantilla del dispositivo termostato. Tome nota del valor **Ámbito de id.** , ya que lo usará más adelante.

1. Abra el dispositivo que acaba de crear y seleccione **Conectar**.

1. Seleccione **Inscripciones individuales** como **Método de conexión** y **Certificados (X.509)** como mecanismo:

    ![Inscripción individual](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Seleccione la opción Archivo en principal y cargue el archivo del certificado llamado _mytestselfcertprimary_cert.pem_ generado anteriormente.

1. Seleccione la opción Archivo del certificado secundario y cargue el archivo del certificado llamado _mytestselfcertsecondary_cert.pem_. Después, seleccione **Save** (Guardar):

    ![Carga de certificados de inscripción individual](./media/how-to-connect-devices-x509/individual-enrollment.png)

El dispositivo ya está aprovisionado con el certificado X.509.

### <a name="run-a-sample-individual-enrollment-device"></a>Ejecución de un dispositivo de inscripción individual de ejemplo

1. Copie los archivos _mytestselfcertprimary_key.pem_ y _mytestselfcertprimary_cert.pem_ en la carpeta _azure-iot-sdk-node/device/samples/pnp_ que contiene la aplicación **simple_thermostat.js**. Utilizó esta aplicación cuando completó el [tutorial Conexión de un dispositivo (JavaScript)](./tutorial-connect-device.md).

1. Modifique las variables de entorno que usó en el ejemplo anterior de la siguiente manera:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Ejecute el script y compruebe que el dispositivo se ha aprovisionado correctamente:

    ```cmd/sh
    node environmentalSensor.js
    ```

    También puede comprobar que la telemetría aparezca en la vista del dispositivo.

    ![Inscripción individual de la telemetría](./media/how-to-connect-devices-x509/telemetry-primary.png)

También puede repetir los pasos anteriores para el certificado _mytestselfcertsecondary_.

## <a name="connect-an-iot-edge-device"></a>Conexión de un dispositivo IoT Edge

En esta sección se da por supuesto que usa una inscripción de grupo para conectar el dispositivo de IoT Edge. Siga los pasos descritos en las secciones anteriores para lo siguiente:

- [Generación de certificados raíz y de dispositivo](#generate-root-and-device-certificates)
- [Creación de una inscripción de grupo](#create-a-group-enrollment) <!-- No slightly different type of enrollment group - UPDATE!! -->

Para conectar el dispositivo IoT Edge a IoT Central mediante el certificado X.509 de dispositivo:

- Copie lo archivos de certificado de dispositivo y clave en el dispositivo IoT Edge. En el ejemplo de inscripción de grupo anterior, estos archivos se denominaban **sampleDevice01_key.pem** y **sampleDevice01_cert.pem**.
- En el dispositivo IoT Edge, edite la sección `provisioning` en el archivo de configuración **/etc/iotedge/config.yaml** como se indica a continuación:

    ```yaml
    # DPS X.509 provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "<SCOPE_ID>"
      attestation:
        method: "x509"
    #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
        identity_cert: "file:///<path>/sampleDevice01_cert.pem"
        identity_pk: "file:///<path>/sampleDevice01_key.pem"
    #  always_reprovision_on_startup: true
    #  dynamic_reprovisioning: false
    ```

    > [!TIP]
    > No es necesario agregar un valor para `registration_id`. IoT Edge puede usar el valor **CN** del certificado X.509.

- Ejecute el siguiente comando para reiniciar el entorno de ejecución de IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

Para más información, vea [Creación y aprovisionamiento de un dispositivo IoT Edge mediante certificados X.509](../../iot-edge/how-to-auto-provision-x509-certs.md).

## <a name="connect-an-iot-edge-leaf-device"></a>Conexión de un dispositivo hoja IoT Edge

IoT Edge usa certificados X.509 para proteger la conexión entre los dispositivos hoja y un dispositivo IoT Edge que actúa como puerta de enlace. Para más información sobre la configuración de este escenario, vea [Conexión de un dispositivo de bajada a una puerta de enlace Azure IoT Edge](../../iot-edge/how-to-connect-downstream-device.md).

## <a name="roll-x509-device-certificates"></a>Implementación de certificados X.509 de dispositivo

Durante el ciclo de vida de la aplicación de IoT Central, deberá implementar los certificados x.509. Por ejemplo:

- Si tiene una brecha de seguridad, la implementación de certificados es un procedimiento recomendado de seguridad para ayudar a proteger el sistema.
- Los certificados x.509 tienen fechas de expiración. La frecuencia de implementación de los certificados depende de las necesidades de seguridad de la solución. Los clientes con soluciones que involucran datos altamente confidenciales pueden implementar certificados diariamente, mientras que otros usuarios implementan sus certificados cada dos años.

Para una conectividad ininterrumpida, IoT Central permite configurar certificados X.509 principales y secundarios. Si los certificados principal y secundario tienen fechas de expiración diferentes, puede implementar el certificado expirado mientras los dispositivos continúan conectándose con el otro certificado.

Para más información, vea [Asumir la metodología de infracción](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf).

En esta sección se describe cómo implementar los certificados en IoT Central. Al implementar un certificado en IoT Central, también debe copiar el nuevo certificado de dispositivo en los dispositivos.

### <a name="obtain-new-x509-certificates"></a>Obtención de nuevos certificados X.509

Obtenga nuevos certificados X.509 del proveedor de certificados. Use una herramienta como OpenSSL para crear sus propios certificados X.509. Este enfoque es muy útil para probar los certificados X.509, pero proporciona pocas garantías de seguridad. Use este enfoque solo para las pruebas, a menos que se prepare para actuar como proveedor de entidad de certificación propio.

### <a name="enrollment-groups-and-security-breaches"></a>Grupos de inscripción e infracciones de seguridad

Para actualizar un grupo de inscripción en respuesta a una infracción de seguridad, debe usar el siguiente enfoque para actualizar inmediatamente el certificado actual. Complete estos pasos para el certificado principal y el secundario, si ambos están en riesgo:

1. Vaya a **Administration** (Administración) en el panel izquierdo y seleccione **Device connection** (Conexión del dispositivo).

2. Seleccione **Grupos de inscripción** y luego seleccione el nombre de grupo en la lista.

3. Para la actualización de certificados, seleccione **Administrar principal** o **Administrar secundario**.

4. Agregue y verifique un certificado X.509 raíz en el grupo de inscripción.

### <a name="individual-enrollments-and-security-breaches"></a>Inscripciones individuales e infracciones de seguridad

Si está implementando certificados en respuesta a una infracción de seguridad, use el siguiente enfoque para actualizar inmediatamente el certificado actual. Complete estos pasos para el certificado principal y el secundario, si ambos están en riesgo:

1. Seleccione **Dispositivos** y seleccione el dispositivo.

1. Seleccione **Conectar** y seleccione el método de conexión **Individual Enrollment** (Inscripción individual).

1. Seleccione el mecanismo **Certificados (X.509)** .

1. Para actualizar un certificado, seleccione el icono de carpeta para seleccionar el nuevo certificado que se va a cargar para la entrada de inscripción. Seleccione **Guardar**.

### <a name="enrollment-groups-and-certificate-expiration"></a>Grupos de inscripciones y expiración de certificados

Para controlar la expiración de los certificados, actualice el certificado actual inmediatamente mediante el siguiente enfoque:

1. Vaya a **Administration** (Administración) en el panel izquierdo y seleccione **Device connection** (Conexión del dispositivo).

2. Seleccione **Grupos de inscripción** y luego seleccione el nombre de grupo en la lista.

3. Para la actualización del certificado, seleccione **Administrar principal**.

4. Agregue y verifique un certificado X.509 raíz en el grupo de inscripción.

5. Más adelante, cuando el certificado secundario haya expirado, vuelva y actualícelo.

### <a name="individual-enrollments-and-certificate-expiration"></a>Inscripciones individuales y expiración de certificados

Si está implementando certificados para controlar las expiraciones de los certificados, debe usar la configuración del certificado secundario como se indica a continuación para reducir el tiempo de inactividad de los dispositivos que intentan aprovisionar.

Cuando se aproxima la expiración del certificado secundario y necesita implementarse, puede pasar al uso de la configuración principal. La rotación entre los certificados principales y secundarios de este modo reduce el tiempo de inactividad de los dispositivos que intentan aprovisionar.

1. Seleccione **Dispositivos** y seleccione el dispositivo.

2. Seleccione **Conectar** y seleccione el método de conexión **Individual Enrollment** (Inscripción individual).

3. Seleccione el mecanismo **Certificados (X.509)** .

4. Para actualizar un certificado secundario, seleccione el icono de carpeta para seleccionar el nuevo certificado que se va a cargar para la entrada de inscripción. Seleccione **Guardar**.

5. Más adelante, cuando el certificado principal haya expirado, vuelva y actualícelo.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a conectar dispositivos mediante certificados X.509, el paso siguiente que se sugiere es consultar el artículo [Supervisión de la conectividad de dispositivos mediante la CLI de Azure](howto-monitor-devices-azure-cli.md).
