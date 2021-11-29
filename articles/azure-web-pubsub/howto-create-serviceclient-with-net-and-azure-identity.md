---
title: Creación de un WebPubSubServiceClient con .NET y Azure Identity
description: Creación de un WebPubSubServiceClient con .NET y Azure Identity
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: 95143bce385245eb1130d2f9e7317b46f7e31e62
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725334"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-net-and-azure-identity"></a>Creación de un `WebPubSubServiceClient` con .NET y Azure Identity

En esta guía paso a paso se muestra cómo crear un `WebPubSubServiceClient` con Azure Active Directory en .NET.

## <a name="requirements"></a>Requisitos

- Instale [Azure.Identity](https://www.nuget.org/packages/Azure.Identity) desde nuget.org.

  ```bash
  Install-Package Azure.Identity
  ```

- Instale [Azure.Messaging.WebPubSub](https://www.nuget.org/packages/Azure.Messaging.WebPubSub) desde nuget.org.

  ```bash
  Install-Package Azure.Messaging.WebPubSub 
  ```

## <a name="sample-codes"></a>Códigos de ejemplo

1. Cree un `TokenCredential` con el SDK de Azure Identity.

    ```C#
    using Azure.Identity

    namespace chatapp 
    {
        public class Program
        {
            public static void Main(string[] args)
            {
                var credential = new DefaultAzureCredential();
            }
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

    Para más información, consulte [Biblioteca cliente de Azure Identity para .NET](/dotnet/api/overview/azure/identity-readme).

2. Después, cree un `client` con `endpoint`, `hub` y `credential`. 

    ```C#
    using Azure.Identity

    public class Program
    {
        public static void Main(string[] args)
        {
            var credential = new DefaultAzureCredential();
            var client = new WebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", credential);
        }
    }
    ```

    O bien inyéctelo en `IServiceCollections` con nuestro `BuilderExtensions`.

    ```C#
    using System;

    using Azure.Identity;

    using Microsoft.Extensions.Azure;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;

    namespace chatapp
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddAzureClients(builder =>
                {
                    var credential = new DefaultAzureCredential();
                    builder.AddWebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", credential);
                });
            }
        }
    }
    ```

    Para aprender a usar este cliente, consulte [Biblioteca cliente del servicio Azure Web PubSub para .NET](/dotnet/api/overview/azure/messaging.webpubsub-readme-pre).

## <a name="complete-sample"></a>Ejemplo completo

- [Sala de chat sencilla con autenticación de AAD](https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/chatapp-aad)