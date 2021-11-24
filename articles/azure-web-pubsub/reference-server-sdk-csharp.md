---
title: 'Referencia: SDK de .NET para Azure Web PubSub'
description: En esta referencia se describe el .NET SDK de JavaScript para el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: ac940b7760ce94beaca5148bfc4e33a59e14b626
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709792"
---
# <a name="azure-web-pubsub-service-client-library-for-net"></a>Biblioteca cliente del servicio Azure Web PubSub para .NET

[Azure Web PubSub](./index.yml) es un servicio administrado de Azure que ayuda a los desarrolladores a compilar fácilmente aplicaciones web con características en tiempo real y patrón de publicación-suscripción. Cualquier escenario que requiera mensajería de publicación y suscripción en tiempo real entre el servidor y los clientes o entre clientes, puede usar el servicio Azure Web PubSub. Las características tradicionales en tiempo real que a menudo requieren tener que sondear desde el servidor o enviar solicitudes HTTP, también pueden usar el servicio Azure Web PubSub.

Puede usar esta biblioteca en el lado del servidor de aplicaciones para administrar las conexiones de cliente de WebSocket, como se muestra en el diagrama siguiente:

![El diagrama de desbordamiento muestra el desbordamiento que supone el uso de la biblioteca cliente del servicio.](media/sdk-reference/service-client-overflow.png)

Use esta biblioteca para:
- Enviar mensajes a centros y grupos. 
- Enviar mensajes a determinados usuarios y conexiones.
- Organizar usuarios y conexiones en grupos.
- Cerrar conexiones.
- Conceder, revocar y comprobar permisos para una conexión existente.

(Los detalles sobre los términos usados aquí se describen en la sección [Conceptos clave](#key-concepts)):
 
[Código fuente](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/src) |
[Paquete](https://www.nuget.org/packages/Azure.Messaging.WebPubSub) |
[Documentación de referencia de API]() |
[Documentación del producto](./index.yml) |
[Ejemplos][samples_ref]

## <a name="getting-started"></a>Introducción

### <a name="install-the-package"></a>Instalar el paquete

Instale la biblioteca cliente desde [NuGet](https://www.nuget.org/):

```dotnetcli
dotnet add package Azure.Messaging.WebPubSub
```

### <a name="prerequisites"></a>Requisitos previos

- Una [suscripción de Azure][azure_sub].
- Una instancia existente del servicio Azure Web PubSub.

### <a name="create-and-authenticate-a-webpubsubserviceclient"></a>Crear y autenticar una `WebPubSubServiceClient`

Para interactuar con el servicio, tiene que crear una instancia de la clase `WebPubSubServiceClient`. Para que esto sea posible, necesitará la cadena de conexión o una clave, a las que puede acceder en Azure Portal.

```C# Snippet:WebPubSubAuthenticate
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));
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

Cuando un cliente está conectado, puede enviar mensajes a la aplicación de nivel superior o recibir mensajes de esta, mediante la conexión WebSocket.

## <a name="examples"></a>Ejemplos

### <a name="broadcast-a-text-message-to-all-clients"></a>Difusión de un mensaje de texto a todos los clientes

```C# Snippet:WebPubSubHelloWorld
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll("Hello World!");
```

### <a name="broadcast-a-json-message-to-all-clients"></a>Difusión de un mensaje JSON a todos los clientes

```C# Snippet:WebPubSubSendJson
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll(RequestContent.Create(
        new
        {
            Foo = "Hello World!",
            Bar = 42
        }),
        ContentType.ApplicationJson);
```

### <a name="broadcast-a-binary-message-to-all-clients"></a>Difusión de un mensaje binario a todos los clientes

```C# Snippet:WebPubSubSendBinary
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

Stream stream = BinaryData.FromString("Hello World!").ToStream();
serviceClient.SendToAll(RequestContent.Create(stream), ContentType.ApplicationOctetStream);
```

## <a name="troubleshooting"></a>Solución de problemas

### <a name="setting-up-console-logging"></a>Configuración del registro de la consola
También puede [habilitar el registro de la consola](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/core/Azure.Core/samples/Diagnostics.md#logging) fácilmente si desea profundizar más en las solicitudes que realiza en el servicio.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]


[azure_sub]: https://azure.microsoft.com/free/dotnet/
[samples_ref]: https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/tests/Samples/
[awps_sample]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp