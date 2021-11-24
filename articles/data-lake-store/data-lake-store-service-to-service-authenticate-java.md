---
title: 'Autenticación entre servicios: Data Lake Storage Gen2: SDK de Java'
description: Aprenda a realizar la autenticación entre servicios con Azure Data Lake Storage Gen2 mediante Azure Active Directory con Java
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: normesta
ms.openlocfilehash: 8f36eaef8c84afd1010d6e4ab2714b0d538028b9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494561"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen2-using-java"></a>Autenticación entre servicios con Azure Data Lake Storage Gen2 mediante Java

> [!div class="op_single_selector"]
> * [Uso de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso de SDK de .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso de la API de REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

En este artículo va a aprender a usar el SDK de Java para realizar la autenticación entre servicios con Azure Data Lake Storage Gen2. No se admite la autenticación de usuario final con Data Lake Storage Gen2 mediante el SDK de Java.

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cree una aplicación "web" de Azure Active Directory**. Debe haber completado los pasos de [Aprenda a realizar la autenticación entre servicios con Azure Data Lake Storage Gen2 mediante Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Este tutorial usa Maven para crear las dependencias de un proyecto. Aunque es posible generarlas sin utilizar un sistema como Maven o Gradle, estos sistemas facilitan mucho la administración de las dependencias.

* (Opcional) Un IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) o similar.

## <a name="service-to-service-authentication"></a>Autenticación entre servicios

1. Cree un proyecto de Maven mediante [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) en la línea de comandos o con un entorno de desarrollo integrado. Para ver instrucciones sobre cómo crear un proyecto de Java mediante IntelliJ, consulte [este artículo](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para ver instrucciones sobre cómo crear un proyecto con Eclipse, consulte [este artículo](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Agregue las siguientes dependencias a su archivo **pom.xml** de Maven. Agregue el siguiente fragmento de código antes de la etiqueta **\</project>** :

    ```xml
    <dependencies>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-storage-file-datalake</artifactId>
          <version>12.6.0</version>
      </dependency>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-identity</artifactId>
          <version>1.3.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    La primera dependencia es el uso del SDK de Data Lake Storage Gen2 (`azure-storage-file-datalake`) desde el repositorio de Maven. La segunda dependencia consiste en especificar la plataforma de registro (`slf4j-nop`) que se va a usar para esta aplicación. El SDK de Data Lake Storage Gen2 usa la fachada de registro [slf4j](https://www.slf4j.org/), que permite elegir entre una serie de plataformas de registro populares, como log4j, registro de Java, Logback, o ningún registro. En este ejemplo, se deshabilita el registro; por tanto, se usa el enlace **slf4j-nop**. Para usar otras opciones de registro en la aplicación, vea [Declaración de dependencias del proyecto para el registro](https://www.slf4j.org/manual.html#projectDep).

3. Agregue las siguientes instrucciones de importación a la aplicación.

    ```java
    import com.azure.identity.ClientSecretCredential;
    import com.azure.identity.ClientSecretCredentialBuilder;
    import com.azure.storage.file.datalake.DataLakeDirectoryClient;
    import com.azure.storage.file.datalake.DataLakeFileClient;
    import com.azure.storage.file.datalake.DataLakeServiceClient;
    import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
    import com.azure.storage.file.datalake.DataLakeFileSystemClient;
    import com.azure.storage.file.datalake.models.ListPathsOptions;
    import com.azure.storage.file.datalake.models.PathAccessControl;
    import com.azure.storage.file.datalake.models.PathPermissions;
    ```

4. Use el siguiente fragmento de código de la aplicación de Java para obtener un token para la aplicación web de Active Directory que se ha creado anteriormente con una de las clases de `StorageSharedKeyCredential` (el siguiente ejemplo usa `credential`). El proveedor de tokens almacena en caché las credenciales que se usan para obtener el token en memoria, y renueva el token de forma automática si está a punto de expirar. Puede crear sus propias subclases de `StorageSharedKeyCredential` para que los tokens se obtengan mediante su código de cliente. Por ahora vamos a usar simplemente el que se proporciona en el SDK.

    Reemplace **FILL-IN-HERE** por los valores reales de la aplicación web de Azure Active Directory.

    ```java
    private static String clienttId = "FILL-IN-HERE";
    private static String tenantId = "FILL-IN-HERE";
    private static String clientSecret = "FILL-IN-HERE";
   
    ClientSecretCredential credential = new ClientSecretCredentialBuilder().clientId(clientId).tenantId(tenantId).clientSecret(clientSecret).build();
    ```

El SDK de Data Lake Storage Gen2 proporciona métodos útiles que permiten administrar los tokens de seguridad necesarios para comunicarse con la cuenta de Data Lake Storage Gen2. Pero el SDK no obliga a que se usen solo estos métodos. También puede usar cualquier otro medio de obtención de tokens, como la [biblioteca cliente de Azure Identity](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/identity/azure-identity) o su propio código personalizado.

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido a usar la autenticación de usuario final para autenticarse en Data Lake Storage Gen2 mediante el SDK de Java. Ahora puede ver los siguientes artículos, que tratan sobre cómo usar el SDK de Java para trabajar con Data Lake Storage Gen2.

* [Operaciones de datos en Data Lake Storage Gen2 mediante el SDK de Java](data-lake-store-get-started-java-sdk.md)
