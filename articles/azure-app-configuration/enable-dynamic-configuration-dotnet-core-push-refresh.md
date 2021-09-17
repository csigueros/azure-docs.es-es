---
title: 'Tutorial: Uso de la configuración dinámica mediante una actualización de inserción en una aplicación de .NET Core'
titleSuffix: Azure App Configuration
description: En este tutorial aprenderá a actualizar dinámicamente los datos de configuración de aplicaciones de .NET Core mediante una actualización de inserción.
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: jken
ms.openlocfilehash: 4ad0f2c20c0d704b2c101f975d1b62fc7dc71aca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750346"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Tutorial: Uso de la configuración dinámica mediante una actualización de inserción en una aplicación de .NET Core

La biblioteca cliente para .NET Core de App Configuration admite la actualización de la configuración a petición sin provocar el reinicio de una aplicación. Se puede configurar una aplicación para que detecte cambios en App Configuration mediante uno de los dos métodos siguientes o ambos.

1. Modelo de sondeo: este es el comportamiento predeterminado, que usa el sondeo para detectar los cambios en la configuración. Una vez que el valor almacenado en caché de una configuración expira, la siguiente llamada a `TryRefreshAsync` o `RefreshAsync` envía una solicitud al servidor para comprobar si la configuración ha cambiado y extrae la configuración actualizada si es necesario.

1. Modo de inserción: este modo utiliza [eventos de App Configuration](./concept-app-configuration-event.md) para detectar los cambios en la configuración. Una vez que App Configuration está configurado para enviar eventos de cambio de valor de clave a Azure Event Grid, la aplicación puede usar estos eventos para optimizar el número total de solicitudes necesarias para mantener la configuración actualizada. Las aplicaciones pueden optar por suscribirse a ellos directamente desde Event Grid o bien mediante uno de los [controladores de eventos admitidos](../event-grid/event-handlers.md), como un webhook, una función de Azure o un tema de Service Bus.

Las aplicaciones pueden optar por suscribirse a estos eventos directamente desde Event Grid, mediante un webhook o mediante el reenvío de eventos a Azure Service Bus. El SDK de Azure Service Bus proporciona una API para registrar un controlador de mensajes que simplifica este proceso para las aplicaciones que no tienen un punto de conexión HTTP o que no desean sondear los cambios continuamente en Event Grid.

Este tutorial le muestra cómo puede implementar las actualizaciones de configuración dinámica en el código mediante una actualización de inserción. Se basa en la aplicación que se introdujo en los inicios rápidos. Antes de continuar, finalice primero el tutorial [Creación de una aplicación .NET Core con Azure App Configuration](./quickstart-dotnet-core-app.md).

Para realizar los pasos de este tutorial, puede usar cualquier editor de código. [Visual Studio Code](https://code.visualstudio.com/) es una excelente opción que está disponible en las plataformas Windows, macOS y Linux.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar una suscripción para enviar eventos de cambio de configuración desde App Configuration a un tema de Service Bus
> * Configurar la aplicación de .NET Core para actualizar su configuración en respuesta a los cambios en App Configuration.
> * Consuma en la aplicación la configuración más reciente.

## <a name="prerequisites"></a>Prerrequisitos

Para realizar este tutorial, instale el [SDK de .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Configuración de un tema y una suscripción de Azure Service Bus

En este tutorial se usa la integración de Service Bus con Event Grid para simplificar la detección de cambios de configuración para las aplicaciones que no desean sondear los cambios de App Configuration de forma continua. El SDK de Azure Service Bus proporciona una API para registrar un controlador de mensajes que se puede usar para actualizar la configuración cuando se detectan cambios en App Configuration. Siga los pasos de [Inicio rápido: Uso de Azure Portal para crear un tema de Service Bus y suscripciones a dicho tema](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) para crear un espacio de nombres, un tema y una suscripción de Service Bus.

Una vez creados los recursos, agregue las siguientes variables de entorno. Se usarán para registrar un controlador de eventos para los cambios de configuración en el código de la aplicación.

| Key | Valor |
|---|---|
| ServiceBusConnectionString | Cadena de conexión para el espacio de nombres de Service Bus |
| ServiceBusTopic | Nombre del tema de Service Bus |
| ServiceBusSubscription | Nombre de la suscripción de Service Bus |

## <a name="set-up-event-subscription"></a>Configuración de la suscripción a eventos

1. Abra el recurso de App Configuration en Azure Portal y, a continuación, haga clic en `+ Event Subscription` (+ Suscripción de eventos) en el panel `Events` (Eventos).

    ![Eventos de App Configuration](./media/events-pane.png)

1. Escriba un nombre para los campos `Event Subscription` (Suscripción de eventos) y `System Topic` (Tema del sistema).

    ![Creación de la suscripción de eventos](./media/create-event-subscription.png)

1. Seleccione `Endpoint Type` (Tipo de punto de conexión) como `Service Bus Topic` (Tema de Service Bus), seleccione el tema de Service Bus y, a continuación, haga clic en `Confirm Selection` (Confirmar selección).

    ![Punto de conexión de Service Bus de suscripción de eventos](./media/event-subscription-servicebus-endpoint.png)

1. Haga clic en `Create` (Crear) para crear la suscripción de eventos.

1. Haga clic en `Event Subscriptions` (Suscripciones de eventos) en el panel `Events` (Eventos) para asegurarse de que la suscripción se haya creado correctamente.

    ![Suscripciones de eventos de App Configuration](./media/event-subscription-view.png)

> [!NOTE]
> Al suscribirse a los cambios de configuración, se pueden usar uno o varios filtros para reducir el número de eventos que se envían a la aplicación. Se pueden configurar como [filtros de suscripción de Event Grid](../event-grid/event-filtering.md) o [filtros de suscripción de Service Bus](../service-bus-messaging/topic-filters.md). Por ejemplo, un filtro de suscripción se puede utilizar para suscribirse solo a eventos de cambios en una clave que empieza por una cadena específica.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Registro del controlador de eventos para volver a cargar los datos de App Configuration

Abra *Program.cs* y actualice el archivo con el código siguiente.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

El método [SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) se usa para establecer el valor almacenado en caché para los pares de clave y valor registrados para la actualización como modificados. Esto garantiza que la siguiente llamada a `RefreshAsync` o `TryRefreshAsync` vuelva a validar los valores almacenados en caché con App Configuration y los actualice si es necesario.

Se agrega un retraso aleatorio antes de que el valor almacenado en caché se marque como modificado para reducir la limitación potencial en caso de que varias instancias se actualicen al mismo tiempo. El retraso máximo predeterminado antes de que el valor almacenado en caché se marque como modificado es de 30 segundos, pero se puede invalidar mediante el paso del parámetro opcional `TimeSpan` al método `SetDirty`.

> [!NOTE]
> Para reducir el número de solicitudes a App Configuration cuando se usa la actualización de inserción, es importante llamar a `SetCacheExpiration(TimeSpan cacheExpiration)` con un valor adecuado del parámetro `cacheExpiration`. Esto controla el tiempo de expiración de la memoria caché para la actualización de extracción y se puede usar como una red de seguridad en caso de que haya un problema con la suscripción de eventos o la suscripción de Service Bus. `TimeSpan.FromDays(30)` es el valor recomendado.

## <a name="build-and-run-the-app-locally"></a>Compilación y ejecución de la aplicación en un entorno local

1. Establezca una variable de entorno llamada **AppConfigurationConnectionString** y defínala como la clave de acceso al almacén de App Configuration. Si usa el símbolo del sistema de Windows, ejecute el siguiente comando y reinícielo para que se aplique el cambio:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si usa Windows PowerShell, ejecute el siguiente comando:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Si usa macOS o Linux, ejecute el siguiente comando:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Ejecute el siguiente comando para compilar la aplicación de consola:

    ```console
     dotnet build
    ```

1. Una vez que la compilación se haya realizado correctamente, ejecute el siguiente comando para ejecutar la aplicación localmente:

    ```console
     dotnet run
    ```

    ![Ejecución de la actualización de inserción antes de actualizar](./media/dotnet-core-app-pushrefresh-initial.png)

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Todos los recursos** y seleccione la instancia de almacén de App Configuration que creó en el inicio rápido.

1. Seleccione **Explorador de configuración** y actualice los valores de las claves siguientes:

    | Clave | Value |
    |---|---|
    | TestApp:Settings:Message | Datos de Azure App Configuration, actualizados |

1. Espere 30 segundos para permitir que se procese el evento y se actualice la configuración.

    ![Ejecución de la actualización de inserción después de actualizar](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha habilitado la aplicación de .NET Core para actualizar dinámicamente la configuración a partir de App Configuration. Para obtener información sobre cómo usar una identidad administrada de Azure para simplificar el acceso a App Configuration, vaya al siguiente tutorial.

> [!div class="nextstepaction"]
> [Integración de identidades administradas](./howto-integrate-azure-managed-service-identity.md)
