---
title: 'Referencia: SDK del servidor Java para el servicio Azure Web PubSub'
description: En la referencia se describe el SDK del servidor Java para el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: b36bf3715458fdf66d8c1a113e96e048bd414d0a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113682"
---
# <a name="java-server-sdk-for-azure-web-pubsub-service"></a>SDK del servidor Java para el servicio Azure Web PubSub

Use la biblioteca para lo siguiente:

- Enviar mensajes a centros y grupos.
- Enviar mensajes a determinados usuarios y conexiones.
- Organizar usuarios y conexiones en grupos.
- Cerrar conexiones.
- Conceder, revocar y comprobar permisos para una conexión existente.

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
    <version>1.0.0-beta.2</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-web-pubsub-client-using-connection-string"></a>Creación de un cliente de Web PubSub mediante una cadena de conexión

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-client-using-access-key"></a>Creación de un cliente de Web PubSub mediante una clave de acceso

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-group-client"></a>Creación de un cliente de grupo de Web PubSub
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .hub("chat")
    .buildClient();
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("java");
```

## <a name="key-concepts"></a>Conceptos clave

[!INCLUDE [Terms](includes/terms.md)]


## <a name="examples"></a>Ejemplos

### <a name="broadcast-message-to-entire-hub"></a>Difusión de un mensaje a todo un centro

```java
webPubSubServiceClient.sendToAll("Hello world!");
```

### <a name="broadcast-message-to-a-group"></a>Difusión de un mensaje a un grupo

```java
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("Java");
javaGroup.sendToAll("Hello Java!");
```

### <a name="send-message-to-a-connection"></a>Envío de un mensaje a una conexión

```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!");
```

### <a name="send-message-to-a-user"></a>Envío de un mensaje a un usuario
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!");
```

## <a name="troubleshooting"></a>Solución de problemas

### <a name="enable-client-logging"></a>Habilitación del registro de cliente
Puede establecer la variable de entorno `AZURE_LOG_LEVEL` para ver las instrucciones de registro realizadas en la biblioteca cliente. Por ejemplo, al establecer `AZURE_LOG_LEVEL=2`, se mostrarán todos los mensajes informativos, de advertencia y de registro de errores. Los niveles de registro se pueden encontrar aquí: [niveles de registro][log_levels].

### <a name="default-http-client"></a>Cliente HTTP predeterminado
Todas las bibliotecas cliente usan de forma predeterminada el cliente HTTP de Netty. Al agregar la dependencia anterior, se configurará automáticamente la biblioteca cliente para usar el cliente HTTP de Netty. La configuración o el cambio del cliente HTTP se detalla en la [wiki de clientes HTTP](/azure/developer/java/sdk/http-client-pipeline).

### <a name="default-ssl-library"></a>Biblioteca SSL predeterminada
De forma predeterminada, todas las bibliotecas cliente usan la biblioteca Boring SSL nativa de Tomcat para habilitar el rendimiento de nivel nativo para las operaciones SSL. La biblioteca Boring SSL es un archivo uber-jar que contiene bibliotecas nativas para Linux, macOS o Windows, que proporciona un mejor rendimiento en comparación con la implementación SSL predeterminada del JDK. Para obtener más información, incluido cómo reducir el tamaño de las dependencias, consulte la sección [optimización del rendimiento][performance_tuning] de la wiki.

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: https://aka.ms/awps/doc
[samples_readme]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[api]: /java/api/com.azure.messaging.webpubsub

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
