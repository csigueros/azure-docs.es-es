---
title: Carga de archivos desde dispositivos a Azure IoT Hub con Java | Microsoft Docs
description: Cómo cargar archivos de un dispositivo a la nube mediante el SDK de dispositivo IoT de Azure para Java. Los archivos cargados se almacenan en un contenedor de blobs de Azure Storage.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/18/2021
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: a280e7b156ebb31269e4a65508596f8ba03c3caf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780089"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Carga de archivos de un dispositivo a la nube con IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial le muestra cómo usar las funcionalidades de carga de archivos de IoT Hub con Java. Para obtener información general sobre el proceso de carga de archivos, vea [Carga de archivos con IoT Hub](iot-hub-devguide-file-upload.md).

En el inicio rápido [Envío de telemetría de un dispositivo a IoT Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) y el tutorial de [Envío de mensajes de la nube al dispositivo con IoT Hub](iot-hub-java-java-c2d.md) se muestra cómo usar la funcionalidad básica de mensajería del dispositivo a la nube y de la nube al dispositivo de IoT Hub. En el tutorial [Configuración del enrutamiento de mensajes con IoT Hub](tutorial-routing.md) se describe una forma de almacenar de manera confiable los mensajes enviados del dispositivo a la nube en Azure Blob Storage. Sin embargo, en algunos casos no se pueden asignar fácilmente los datos de que los dispositivos envían en los mensajes de dispositivo a nube con un tamaño relativamente reducido que acepta Azure IoT Hub. Por ejemplo:

* Archivos grandes con imágenes
* Vídeos
* Datos de vibración muestreados con alta frecuencia
* Algún tipo de datos de procesamiento previo.

Dichos archivos se suelen procesar por lotes en la nube mediante herramientas como [Azure Data Factory](../data-factory/introduction.md) o la pila [Hadoop](../hdinsight/index.yml). Sin embargo, cuando necesite cargar archivos desde un dispositivo, todavía puede usar la seguridad y confiabilidad de IoT Hub. En el ejemplo se muestra cómo. Además, hay dos ejemplos ubicados en [https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/file-upload-sample/src/main/java/samples/com/microsoft/azure/sdk/iot](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/file-upload-sample/src/main/java/samples/com/microsoft/azure/sdk/iot) en GitHub.

> [!NOTE]
> IoT Hub admite muchas plataformas de dispositivos y lenguajes (incluido C, .NET y JavaScript), mediante los SDK de dispositivo IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot) para obtener instrucciones paso a paso sobre cómo conectar el dispositivo a Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Requisitos previos

* [Java SE Development Kit 8](/java/azure/jdk/). Asegúrese de seleccionar **Java 8** en **Long-term support** (Soporte técnico a largo plazo) para obtener descargas de JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Una cuenta de Azure activa. (En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos).

* Asegúrese de que el puerto 8883 está abierto en el firewall. En el ejemplo de dispositivo de este artículo se usa el protocolo MQTT, que se comunica mediante el puerto 8883. Este puerto puede estar bloqueado en algunos entornos de red corporativos y educativos. Para más información y para saber cómo solucionar este problema, consulte el artículo sobre la [conexión a IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registro de un nuevo dispositivo en el centro de IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="create-a-project-using-maven"></a>Creación de un proyecto mediante Maven

Cree un directorio para el proyecto e inicie un shell en ese directorio. En la línea de comandos, ejecute lo siguiente:

```cmd/sh
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Esto genera un directorio con el mismo nombre que *artifactId* y una estructura de proyecto estándar:

```
  my-app
  |-- pom.xml
   -- src
      -- main
         -- java
            -- com
               -- mycompany
                  -- app
                     --App.Java
```

Con un editor de texto, reemplace el archivo pom.xml por lo siguiente:

```xml

<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>my-app</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <dependencies>
      <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.30.1</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.29</version>
    </dependency>    
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.3</version>
            <configuration>
              <source>1.7</source>
              <target>1.7</target>
            </configuration>
        </plugin>
        <plugin>
          <artifactId>maven-shade-plugin</artifactId>
          <version>2.4</version>
          <executions>
              <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                  <configuration>
                      <filters>
                          <filter>
                              <artifact>*:*</artifact>
                              <excludes>
                                  <exclude>META-INF/*.SF</exclude>
                                  <exclude>META-INF/*.RSA</exclude>
                              </excludes>
                          </filter>
                      </filters>
                      <shadedArtifactAttached>true</shadedArtifactAttached>
                      <shadedClassifierName>with-deps</shadedClassifierName>
                  </configuration>
              </execution>
          </executions>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```

## <a name="upload-a-file-from-a-device-app"></a>Carga de un archivo desde una aplicación de dispositivo

Copie el archivo que quiere cargar en la carpeta `my-app` del árbol del proyecto. Con un editor de texto, reemplace App.java por el código siguiente. Proporcione la cadena de conexión del dispositivo y el nombre de archivo donde corresponda. Ha copiado la cadena de conexión del dispositivo al registrar este.

```java
package com.mycompany.app;

import com.azure.storage.blob.BlobClient;
import com.azure.storage.blob.BlobClientBuilder;
import com.microsoft.azure.sdk.iot.deps.serializer.FileUploadCompletionNotification;
import com.microsoft.azure.sdk.iot.deps.serializer.FileUploadSasUriRequest;
import com.microsoft.azure.sdk.iot.deps.serializer.FileUploadSasUriResponse;
import com.microsoft.azure.sdk.iot.device.DeviceClient;
import com.microsoft.azure.sdk.iot.device.IotHubClientProtocol;

import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.URISyntaxException;
import java.util.Scanner;

public class App 
{
    /**
     * Upload a single file to blobs using IoT Hub.
     *
     */
    public static void main(String[] args)throws IOException, URISyntaxException
    {
        String connString = "Your device connection string here";
        String fullFileName = "Path of the file to upload";

        System.out.println("Starting...");
        System.out.println("Beginning setup.");

        // File upload will always use HTTPS, DeviceClient will use this protocol only
        //   for the other services like Telemetry, Device Method and Device Twin.
        IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;

        System.out.println("Successfully read input parameters.");

        DeviceClient client = new DeviceClient(connString, protocol);

        System.out.println("Successfully created an IoT Hub client.");

        try
        {
            File file = new File(fullFileName);
            if (file.isDirectory())
            {
                throw new IllegalArgumentException(fullFileName + " is a directory, please provide a single file name, or use the FileUploadSample to upload directories.");
            }

            System.out.println("Retrieving SAS URI from IoT Hub...");
            FileUploadSasUriResponse sasUriResponse = client.getFileUploadSasUri(new FileUploadSasUriRequest(file.getName()));

            System.out.println("Successfully got SAS URI from IoT Hub");
            System.out.println("Correlation Id: " + sasUriResponse.getCorrelationId());
            System.out.println("Container name: " + sasUriResponse.getContainerName());
            System.out.println("Blob name: " + sasUriResponse.getBlobName());
            System.out.println("Blob Uri: " + sasUriResponse.getBlobUri());

            System.out.println("Using the Azure Storage SDK to upload file to Azure Storage...");

            try
            {
                BlobClient blobClient =
                    new BlobClientBuilder()
                        .endpoint(sasUriResponse.getBlobUri().toString())
                        .buildClient();

                blobClient.uploadFromFile(fullFileName);
            }
            catch (Exception e)
            {
                System.out.println("Exception encountered while uploading file to blob: " + e.getMessage());

                System.out.println("Failed to upload file to Azure Storage.");

                System.out.println("Notifying IoT Hub that the SAS URI can be freed and that the file upload failed.");

                // Note that this is done even when the file upload fails. IoT Hub has a fixed number of SAS URIs allowed active
                // at any given time. Once you are done with the file upload, you should free your SAS URI so that other
                // SAS URIs can be generated. If a SAS URI is not freed through this API, then it will free itself eventually
                // based on how long SAS URIs are configured to live on your IoT Hub.
                FileUploadCompletionNotification completionNotification = new FileUploadCompletionNotification(sasUriResponse.getCorrelationId(), false);
                client.completeFileUpload(completionNotification);

                System.out.println("Notified IoT Hub that the SAS URI can be freed and that the file upload was a failure.");

                client.closeNow();
                return;
            }

            System.out.println("Successfully uploaded file to Azure Storage.");

            System.out.println("Notifying IoT Hub that the SAS URI can be freed and that the file upload was a success.");
            FileUploadCompletionNotification completionNotification = new FileUploadCompletionNotification(sasUriResponse.getCorrelationId(), true);
            client.completeFileUpload(completionNotification);
            System.out.println("Successfully notified IoT Hub that the SAS URI can be freed, and that the file upload was a success");
        }
        catch (Exception e)
        {
            System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \nERROR: " +  e.getMessage());
            System.out.println("Shutting down...");
            client.closeNow();
        }

        System.out.println("Press any key to exit...");

        Scanner scanner = new Scanner(System.in);
        scanner.nextLine();
        System.out.println("Shutting down...");
        client.closeNow();
    }
}
```

## <a name="build-and-run-the-application"></a>Compilación y ejecución de la aplicación

En la carpeta `my-app` del símbolo del sistema, ejecute el siguiente comando:

```cmd/sh
mvn clean package -DskipTests
```

Una vez finalizada la compilación, ejecute el siguiente comando para ejecutar la aplicación:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Puede usar el portal para ver el archivo cargado en el contenedor de almacenamiento que configuró:

![Archivo cargado](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="receive-a-file-upload-notification"></a>Recepción de una notificación de carga de archivos

En esta sección, se crea una aplicación de consola de Java que recibe mensajes de notificación de carga de archivos de IoT Hub.

1. Cree un directorio para el proyecto e inicie un shell en ese directorio. En la línea de comandos, ejecute lo siguiente:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la carpeta `my-app` nueva.

3. Con un editor de texto, reemplace el archivo `pom.xml` de la carpeta `my-app` por lo siguiente. Añadir la dependencia del cliente del servicio le permite usar el paquete **iothub-java-service-client** en la aplicación para comunicarse con el servicio IoT Hub:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>

      <groupId>com.mycompany.app</groupId>
      <artifactId>my-app</artifactId>
      <version>1.0-SNAPSHOT</version>

      <name>my-app</name>
      <!-- FIXME change it to the project's website -->
      <url>http://www.example.com</url>

      <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.7</maven.compiler.source>
        <maven.compiler.target>1.7</maven.compiler.target>
      </properties>

      <dependencies>
          <dependency>
          <groupId>com.microsoft.azure.sdk.iot</groupId>
          <artifactId>iot-device-client</artifactId>
          <version>1.30.1</version>
        </dependency>
        <dependency>
          <groupId>com.microsoft.azure.sdk.iot</groupId>
          <artifactId>iot-service-client</artifactId>
          <version>1.7.23</version>
        </dependency>
        <dependency>
          <groupId>org.slf4j</groupId>
          <artifactId>slf4j-log4j12</artifactId>
          <version>1.7.29</version>
        </dependency>    
        <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.11</version>
          <scope>test</scope>
        </dependency>
      </dependencies>

      <build>
        <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                  <source>1.7</source>
                  <target>1.7</target>
                </configuration>
            </plugin>
            <plugin>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.4</version>
              <executions>
                  <execution>
                      <phase>package</phase>
                      <goals>
                        <goal>shade</goal>
                      </goals>
                      <configuration>
                          <filters>
                              <filter>
                                  <artifact>*:*</artifact>
                                  <excludes>
                                      <exclude>META-INF/*.SF</exclude>
                                      <exclude>META-INF/*.RSA</exclude>
                                  </excludes>
                              </filter>
                          </filters>
                          <shadedArtifactAttached>true</shadedArtifactAttached>
                          <shadedClassifierName>with-deps</shadedClassifierName>
                      </configuration>
                  </execution>
              </executions>
            </plugin>
          </plugins>
        </pluginManagement>
      </build>
    </project>
    ```

    > [!NOTE]
    > Puede comprobar la versión más reciente de **iot-service-client** mediante la [búsqueda de Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Guarde y cierre el archivo `pom.xml`.

5. Obtenga la cadena de conexión del servicio IoT Hub.
    [!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

6. Con un editor de texto, abra el archivo `my-app\src\main\java\com\mycompany\app\App.java` y reemplace el código por lo siguiente.

    ```java
    package com.mycompany.app;

    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;


    public class App 
    {
        private static final String connectionString = "{Your service connection string here}";
        private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;

        public static void main(String[] args) throws Exception
        {
            ServiceClient sc = ServiceClient.createFromConnectionString(connectionString, protocol);

            FileUploadNotificationReceiver receiver = sc.getFileUploadNotificationReceiver();
            receiver.open();
            FileUploadNotification fileUploadNotification = receiver.receive(2000);
    
            if (fileUploadNotification != null)
            {
                System.out.println("File Upload notification received");
                System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
                System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
                System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
                System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
                System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
                System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
            else
            {
                System.out.println("No file upload notification");
            }
    
            receiver.close();
        }
    
    }
    ```


7. Guarda y cierra el archivo `my-app\src\main\java\com\mycompany\app\App.java`.

8. Use el siguiente comando para compilar la aplicación y buscar errores:
    ```cmd/sh
    mvn clean package -DskipTests
    ```
## <a name="run-the-application"></a>Ejecución de la aplicación

Ahora está preparado para ejecutar la aplicación.

En la carpeta `my-app` del símbolo del sistema, ejecute el siguiente comando:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```
En la captura de pantalla siguiente se muestra la salida de la aplicación **read-file-upload-notification**:

![Salida de la aplicación read-file-upload-notification](media/iot-hub-java-java-upload/read-file-upload-notification.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a usar la funcionalidad de carga de archivos de IoT Hub para simplificar la carga de archivos desde los dispositivos. Puede continuar explorando las características y los escenarios del centro de IoT con los siguientes artículos:

* [Creación de un centro de IoT mediante programación](iot-hub-rm-template-powershell.md)

* [Introducción a SDK para C](iot-hub-device-sdk-c-intro.md)

* [SDK de IoT de Azure](iot-hub-devguide-sdks.md)

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Simulación de un dispositivo con IoT Edge](../iot-edge/quickstart-linux.md)