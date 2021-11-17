---
title: Archivo de inclusión
description: Archivo de inclusión
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 2efbb9772e70afd699d467668fd1eadd69502b04
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461217"
---
## <a name="set-up-prerequisites"></a>Configuración de requisitos previos

- [Kit de desarrollo de Java (JDK)](/azure/developer/java/fundamentals/java-jdk-install), versión 8 o posterior.
- [Apache Maven](https://maven.apache.org/download.cgi).

## <a name="set-up"></a>Configuración

### <a name="create-a-new-java-application"></a>Creación de una aplicación Java

Abra el terminal o la ventana de comandos. Vaya al directorio en el que quiere crear la aplicación Java. Ejecute el siguiente comando para generar el proyecto de Java a partir de la plantilla `maven-archetype-quickstart`.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Observará que la tarea "generar" creó un directorio con el mismo nombre que el objeto `artifactId`. En este directorio, el directorio `src/main/java` contiene el código fuente del proyecto, el directorio `src/test/java directory` contiene el origen de la prueba y el archivo `pom.xml` es el modelo de objetos del proyecto o POM.

### <a name="install-the-package"></a>Instalar el paquete

Abra el archivo `pom.xml` en el editor de texto. Agregue los siguientes elementos de dependencia al grupo de dependencias.

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-communication-identity</artifactId>
        <version>1.2.0-beta.1</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>msal4j</artifactId>
      <version>1.11.0</version>
    </dependency>
</dependencies>
```

### <a name="set-up-the-app-framework"></a>Instalación del marco de la aplicación

Desde el directorio del proyecto:

1. Vaya al directorio `/src/main/java/com/communication/quickstart`
1. Abra el archivo `App.java` en un editor.
1. Reemplace la instrucción `System.out.println("Hello world!");`.
1. Agregue directivas `import`.

Use el código siguiente para empezar:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.InteractiveRequestParameters;
import com.microsoft.aad.msal4j.PublicClientApplication;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws Exception
    {
        System.out.println("Azure Communication Services - Communication access token Quickstart");
        // Quickstart code goes here
    }
}
```

### <a name="step-1-receive-the-azure-active-directory-user-token-via-the-msal-library"></a>Paso 1: Recibir el token de usuario de Azure Active Directory mediante la biblioteca MSAL

El primer paso del flujo de intercambio de tokens es obtener un token para el usuario de Teams mediante [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md).

```java
String appId = "<contoso_application_id>";
String authority = "https://login.microsoftonline.com/<contoso_tenant_id>";

PublicClientApplication pca = PublicClientApplication.builder(appId)
        .authority(authority)
        .build();

String redirectUri = "http://localhost";
Set<String> scope = new HashSet<String>();
scope.add("https://auth.msft.communication.azure.com/VoIP");

InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI(redirectUri))
                    .scopes(scope)
                    .build();

IAuthenticationResult result = pca.acquireToken(parameters).get();
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Paso 2: Inicializar CommunicationIdentityClient

Cree una instancia de `CommunicationIdentityClient` con la clave de acceso y el punto de conexión del recurso. Aprenda a [administrar la cadena de conexión del recurso](../create-communication-resource.md#store-your-connection-string). Además, puede inicializar el cliente con cualquier cliente HTTP personalizado que implemente la interfaz `com.azure.core.http.HttpClient`.

Agregue el código siguiente al método `main`:

```java
//You can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Paso 3: Intercambiar el token de usuario de Azure AD para el token de acceso de Teams

Use el método `getTokenForTeamsUser` para emitir un token de acceso para el usuario de Teams que se puede usar con los SDK de Azure Communication Services.

```java
var accessToken = communicationIdentityClient.getTokenForTeamsUser(result.accessToken());
System.out.println("Token: " + accessToken.getToken());
```

## <a name="run-the-code"></a>Ejecución del código

Navegue hasta el directorio que contiene el archivo `pom.xml` y compile el proyecto mediante el siguiente comando `mvn`.

A continuación, compile el paquete.

```console
mvn package
```

Ejecute el siguiente comando `mvn` para ejecutar la aplicación.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
