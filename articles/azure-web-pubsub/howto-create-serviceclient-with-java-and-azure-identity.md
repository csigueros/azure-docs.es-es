---
title: Creación de un webPubSubServiceClient con Java y Azure Identity
description: Creación de un webPubSubServiceClient con Java y Azure Identity
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: aa0f9918b971015632866eb50995e6a2d6eb2f75
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725431"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-java-and-azure-identity"></a>Creación de un `WebPubSubServiceClient` con Java y Azure Identity

En esta guía paso a paso se muestra cómo crear un `WebPubSubServiceClient` con Java y Azure Identity.

## <a name="requirements"></a>Requisitos

- Agregue la dependencia [azure-identity](https://mvnrepository.com/artifact/com.azure/azure-identity) en el archivo `pom.xml`.

  ```xml
  <dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.4.1</version>
  </dependency>
  ```

  > [!Tip]
  > La versión más reciente se puede encontrar en esta [página](https://mvnrepository.com/artifact/com.azure/azure-identity).

  Consulte [Autenticación de Azure con Java y Azure Identity](/azure/developer/java/sdk/identity) para más información.

- Agregue la dependencia [azure-messaging-webpubsub](https://mvnrepository.com/artifact/com.azure/azure-messaging-webpubsub) al archivo `pom.xml`.

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-messaging-webpubsub</artifactId>
      <version>1.0.0-beta.6</version>
  </dependency>
  ```

  > [!Tip]
  > La versión más reciente se puede encontrar en esta [página](https://mvnrepository.com/artifact/com.azure/azure-messaging-webpubsub).

## <a name="sample-codes"></a>Códigos de ejemplo

1. Cree un `TokenCredential` con el SDK de Azure Identity.

    ```java
    package com.webpubsub.tutorial;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.DefaultAzureCredentialBuilder;

    public class App {

        public static void main(String[] args) {
            TokenCredential credential = new DefaultAzureCredentialBuilder().build();
        }
    }
    ```

    `credential` puede ser cualquier clase que se herede de la clase `TokenCredential`.

    - EnvironmentCredential
    - ClientSecretCredential
    - ClientCertificateCredential
    - ManagedIdentityCredential
    - VisualStudioCredential
    - VisualStudioCodeCredential
    - AzureCliCredential

    Para más información, consulte [Biblioteca cliente de Azure Identity para Java](/java/api/overview/azure/identity-readme)

2. Después, cree un `client` con `endpoint`, `hub` y `credential`. 

    ```Java
    package com.webpubsub.tutorial;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.DefaultAzureCredentialBuilder;
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.WebPubSubServiceClientBuilder;

    public class App {
        public static void main(String[] args) {

            TokenCredential credential = new DefaultAzureCredentialBuilder().build();

            // create the service client
            WebPubSubServiceClient client = new WebPubSubServiceClientBuilder()
                    .endpoint("<endpoint>")
                    .credential(credential)
                    .hub("<hub>")
                    .buildClient();
        }
    }
    ```

    Para aprender a usar este cliente, consulte [Biblioteca cliente del servicio Azure Web PubSub para Java](/java/api/overview/azure/messaging-webpubsub-readme).

## <a name="complete-sample"></a>Ejemplo completo

- [Sala de chat sencilla con autenticación de AAD](https://github.com/Azure/azure-webpubsub/tree/main/samples/java/chatapp-aad)