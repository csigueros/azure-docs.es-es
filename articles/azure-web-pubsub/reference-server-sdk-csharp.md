---
title: 'Referencia: SDK del servidor de .NET para el servicio Azure Web PubSub'
description: En la referencia se describe el SDK del servidor de .NET para el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 4bb4c5d90958a31e26c39ce9d0fe7e22bec769e2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114391"
---
# <a name="net-server-sdk-for-azure-web-pubsub-service"></a>SDK del servidor de .NET para el servicio Azure Web PubSub

Esta biblioteca se puede utilizar para las siguientes acciones. (Los detalles sobre los términos usados aquí se describen en la sección [Conceptos clave](#key-concepts)):

- Enviar mensajes a centros y grupos. 
- Enviar mensajes a determinados usuarios y conexiones.
- Organizar usuarios y conexiones en grupos.
- Cerrar conexiones.
- Conceder, revocar y comprobar permisos para una conexión existente.

[Código fuente][code] |
[Paquete][package] |
[Documentación de referencia de API][api] |
[Documentación del producto](https://aka.ms/awps/doc) |
[Ejemplos][samples_ref]

## <a name="getting-started"></a>Introducción
### <a name="install-the-package"></a>Instalar el paquete

Instale la biblioteca cliente desde [NuGet](https://www.nuget.org/):

```PowerShell
dotnet add package Azure.Messaging.WebPubSub --prerelease
```

### <a name="prerequisites"></a>Requisitos previos

- Una [suscripción de Azure][azure_sub].
- Una instancia existente del servicio Azure Web PubSub.

### <a name="authenticate-the-client"></a>Autenticar el cliente

Para interactuar con el servicio, tiene que crear una instancia de la clase WebPubSubServiceClient. Para que esto sea posible, necesitará la cadena de conexión o una clave, a las que puede acceder en Azure Portal.

### <a name="create-a-webpubsubserviceclient"></a>Creación de una clase `WebPubSubServiceClient`

```csharp
var serviceClient = new WebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", new AzureKeyCredential("<access-key>"));
```

## <a name="key-concepts"></a>Conceptos clave

[!INCLUDE [Termsc](includes/terms.md)]

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
También puede [habilitar el registro de la consola](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging) fácilmente si desea profundizar más en las solicitudes que realiza en el servicio.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp
[code]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/Azure.Messaging.WebPubSub/src
[package]: https://www.nuget.org/packages/Azure.Messaging.WebPubSub
[api]: /dotnet/api/azure.messaging.webpubsub

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
