---
title: 'Referencia: SDK de Java para Azure Web PubSub'
description: En esta referencia se describe el SDK de Java para el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: e004e32235330509051d635774b0ff685fb2eb4d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493073"
---
# <a name="azure-web-pubsub-service-client-library-for-java"></a>Biblioteca cliente del servicio Azure Web PubSub para Java

[Azure Web PubSub](https://aka.ms/awps/doc) es un servicio administrado de Azure que ayuda a los desarrolladores a compilar fácilmente aplicaciones web con características en tiempo real y patrón de publicación-suscripción. Cualquier escenario que requiera mensajería de publicación y suscripción en tiempo real entre el servidor y los clientes o entre clientes, puede usar el servicio Azure Web PubSub. Las características tradicionales en tiempo real que a menudo requieren tener que sondear desde el servidor o enviar solicitudes HTTP, también pueden usar el servicio Azure Web PubSub.

Puede usar esta biblioteca en el lado del servidor de aplicaciones para administrar las conexiones de cliente de WebSocket, como se muestra en el diagrama siguiente:

![El diagrama de desbordamiento muestra el desbordamiento que supone el uso de la biblioteca cliente del servicio.](media/sdk-reference/service-client-overflow.png)

Use esta biblioteca para:
- Enviar mensajes a centros y grupos. 
- Enviar mensajes a determinados usuarios y conexiones.
- Organizar usuarios y conexiones en grupos.
- Cerrar conexiones.
- Conceder, revocar y comprobar permisos para una conexión existente.

(Los detalles sobre los términos usados aquí se describen en la sección [Conceptos clave](#key-concepts)):

[Código fuente][source_code] | [Documentación de referencia de API][api] | [Documentación del producto][product_documentation] | [Ejemplos][samples_readme]

## <a name="getting-started"></a>Introducción

### <a name="prerequisites"></a>Requisitos previos

- Un [kit de desarrollo de Java (JDK)][jdk_link], versión 8 o posterior.
- [Suscripción de Azure][azure_subscription]

### <a name="include-the-package"></a>Inclusión del paquete

[//]: # ({x-version-update-start;com.azure:azure-messaging-webpubsub;current})

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-webpubsub</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-webpubsubserviceclient-using-connection-string"></a>Creación de un `WebPubSubServiceClient` mediante una cadena de conexión

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L21-L24 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-webpubsubserviceclient-using-access-key"></a>Creación de un `WebPubSubServiceClient` mediante una clave de acceso

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L31-L35 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
```

## <a name="key-concepts"></a>Conceptos clave

### <a name="connection"></a>Conexión

Una conexión, también conocida como "cliente" o "conexión de cliente", representa una conexión WebSocket individual conectada al servicio Web PubSub. Cuando se conecta correctamente, el servicio Web PubSub asigna un identificador de conexión único a esta conexión.

### <a name="hub"></a>Hub

El centro de conectividad es un concepto lógico para un conjunto de conexiones de clientes. Normalmente se usa un centro de conectividad para una finalidad, por ejemplo, un centro de conectividad de chats o uno de notificaciones. Cuando se establece una conexión de cliente, esta se realiza con un centro de conectividad y, durante su vigencia, pertenece a dicho centro. Diferentes aplicaciones pueden compartir un servicio de Azure Web PubSub mediante el uso de nombres de centros de conectividad diferentes.

### <a name="group"></a>Grupo

El grupo es un subconjunto de conexiones al centro de conectividad. Puede agregar una conexión de cliente a un grupo o quitarla de este cuando quiera. Por ejemplo, cuando un cliente se une a una sala de chat o sale de ella, dicha sala puede considerarse un grupo. Un cliente puede unirse a varios grupos, y un grupo puede contener varios clientes.

### <a name="user"></a>Usuario

Las conexiones a Web PubSub pueden pertenecer a un usuario. Un usuario puede tener varias conexiones, por ejemplo, cuando está conectado a través de varios dispositivos o distintas pestañas del explorador.

### <a name="message"></a>Message

Cuando el cliente está conectado, puede enviar mensajes a la aplicación de nivel superior o recibir mensajes de esta, mediante la conexión WebSocket.

## <a name="examples"></a>Ejemplos

* [Difusión de un mensaje a todo un centro](#broadcast-all "Difusión de un mensaje a todo un centro")
* [Difusión de un mensaje a un grupo](#broadcast-group "Difusión de un mensaje a un grupo")
* [Envío de un mensaje a una conexión](#send-to-connection "Envío de un mensaje a una conexión")
* [Envío de un mensaje a un usuario](#send-to-user "Envío de un mensaje a un usuario")

<a name="broadcast-all"></a>

### <a name="broadcast-message-to-entire-hub"></a>Difusión de un mensaje a todo un centro

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L47-L47 -->
```java
webPubSubServiceClient.sendToAll("Hello world!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="broadcast-group"></a>

### <a name="broadcast-message-to-a-group"></a>Difusión de un mensaje a un grupo

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L59-L59 -->
```java
webPubSubServiceClient.sendToGroup("java", "Hello Java!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-connection"></a>

### <a name="send-message-to-a-connection"></a>Envío de un mensaje a una conexión

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L71-L71 -->
```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-user"></a>

### <a name="send-message-to-a-user"></a>Envío de un mensaje a un usuario
<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L83-L83 -->
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!", WebPubSubContentType.TEXT_PLAIN);
```

## <a name="troubleshooting"></a>Solución de problemas

### <a name="enable-client-logging"></a>Habilitación del registro de cliente
Puede establecer la variable de entorno `AZURE_LOG_LEVEL` para ver las instrucciones de registro realizadas en la biblioteca cliente. Por ejemplo, al establecer `AZURE_LOG_LEVEL=2`, se mostrarán todos los mensajes informativos, de advertencia y de registro de errores. Los niveles de registro se pueden encontrar aquí: [niveles de registro][log_levels].

### <a name="default-http-client"></a>Cliente HTTP predeterminado
Todas las bibliotecas cliente usan de forma predeterminada el cliente HTTP de Netty. Al agregar la dependencia anterior, se configurará automáticamente la biblioteca cliente para usar el cliente HTTP de Netty. La configuración o el cambio del cliente HTTP se detalla en la [wiki de clientes HTTP](https://github.com/Azure/azure-sdk-for-java/wiki/HTTP-clients).

### <a name="default-ssl-library"></a>Biblioteca SSL predeterminada
De forma predeterminada, todas las bibliotecas cliente usan la biblioteca Boring SSL nativa de Tomcat para habilitar el rendimiento de nivel nativo para las operaciones SSL. La biblioteca Boring SSL es un archivo uber-jar que contiene bibliotecas nativas para Linux, macOS o Windows, que proporciona un mejor rendimiento en comparación con la implementación SSL predeterminada del JDK. Para obtener más información, incluido cómo reducir el tamaño de las dependencias, consulte la sección [optimización del rendimiento][performance_tuning] de la wiki.

[!INCLUDE [next step](includes/include-next-step.md)]


<!-- LINKS -->

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: https://aka.ms/awps/doc
[samples_readme]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/webpubsub/azure-messaging-webpubsub/src/samples/README.md
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[cla]: https://cla.microsoft.com
[coc]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[api]: https://aka.ms/awps/sdk/java
