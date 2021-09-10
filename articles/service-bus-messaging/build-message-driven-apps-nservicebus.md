---
title: Compilación de aplicaciones controladas por mensajes con NServiceBus y Azure Service Bus
description: Obtenga información sobre cómo resolver problemas complejos con sistemas distribuidos en Azure Service Bus usando el marco NServiceBus.
author: kbaley
ms.author: spelluru
ms.service: service-bus-messaging
ms.topic: how-to
ms.date: 07/26/2021
ms.custom: template-how-to
ms.openlocfilehash: c9f503cb600c87e1dac590dcbbe7edf46bc7be76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779841"
---
# <a name="build-message-driven-business-applications-with-nservicebus-and-azure-service-bus"></a>Compilación de aplicaciones empresariales controladas por mensajes con NServiceBus y Azure Service Bus
NServiceBus es un marco de mensajería comercial proporcionado por Particular Software. Se basa en Azure Service Bus ayuda a los desarrolladores a centrarse en la lógica de negocios abstrayendo de problemas de infraestructura. En esta guía se va a compilar una solución que intercambia mensajes entre dos servicios. También se mostrará cómo reintentar automáticamente los mensajes con error y se revisarán opciones para hospedar estos servicios en Azure.

> [!NOTE]
> El código de este tutorial está disponible en el [sitio web de documentación de Particular Software](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/). 

## <a name="prerequisites"></a>Requisitos previos

En el ejemplo se da por supuesto que [ha creado un espacio de nombres de Azure Service Bus](service-bus-create-namespace-portal.md).

> [!IMPORTANT]
> NServiceBus requiere al menos el nivel Estándar. No es compatible con el nivel Básico.

## <a name="download-and-prepare-the-solution"></a>Descarga y preparación de la solución
1. Descargue el código desde el [sitio web de documentación de Particular Software](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/). El objeto `SendReceiveWithNservicebus.sln` de la solución consta de tres proyectos:

    - **Remitente:** aplicación de consola que envía mensajes.
    - **Receptor:** aplicación de consola que recibe mensajes del remitente y envía respuestas.
    - **Compartido:** biblioteca de clases que contiene los contratos de mensaje compartidos entre el remitente y el receptor.
    
    En el siguiente diagrama generado por [ServiceInsight](https://particular.net/serviceinsight), una herramienta de visualización y depuración de Particular Software, se muestra el flujo del mensaje:
    
    :::image type="content" source="./media/nservicebus/sequence-diagram.png" alt-text="Imagen en la que se muestra el diagrama de secuencia":::    
1. Abra `SendReceiveWithNservicebus.sln` en su editor de código favorito (por ejemplo, Visual Studio 2019). 
1. Abra `appsettings.json` en los proyectos Receptor y Remitente y establezca `AzureServiceBusConnectionString` en la cadena de conexión para el espacio de nombres de Azure Service Bus.



## <a name="define-the-shared-message-contracts"></a>Definición de los contratos de mensaje compartidos

La biblioteca de clases Compartido es donde se definen los contratos usados para enviar los mensajes. Incluye una referencia al paquete NuGet `NServiceBus`, que contiene interfaces que puede usar para identificar los mensajes. Las interfaces no son necesarias, pero proporcionan validación adicional de NServiceBus y permiten que el código se documente de forma automática.

En primer lugar, revise la clase `Ping.cs`:

```csharp
public class Ping : NServiceBus.ICommand
{
    public int Round { get; set; }
}
```

La clase `Ping` define un mensaje que el remitente envía al receptor. Es una clase de C# simple que implementa `NServiceBus.ICommand`, una interfaz del paquete NServiceBus. Este mensaje indica al lector y a NServiceBus que se trata de un comando, aunque hay otras maneras de identificar mensajes [sin usar interfaces](https://docs.particular.net/nservicebus/messaging/conventions).

La otra clase de mensaje del proyecto Compartido es `Pong.cs`:

```csharp
public class Pong : NServiceBus.IMessage
{
    public string Acknowledgement { get; set; }
}
```

`Pong` es también un objeto de C# simple, aunque este implementa `NServiceBus.IMessage`. La interfaz `IMessage` representa un mensaje genérico que no es un comando ni un evento y se usa normalmente para respuestas. En el ejemplo, es una respuesta que el receptor devuelve al remitente para indicar que se ha recibido un mensaje.

`Ping` y `Pong` son los dos tipos de mensaje que va a usar. El siguiente paso consiste en configurar el remitente para que use Azure Service Bus y envíe un mensaje `Ping`.

## <a name="set-up-the-sender"></a>Configuración del remitente

El remitente es un punto de conexión que envía el mensaje `Ping`. Aquí, configure el remitente para que use Azure Service Bus como mecanismo de transporte. A continuación, cree una instancia `Ping` y envíela.

En el método `Main` de `Program.cs`, configure el punto de conexión del remitente:

```csharp
var host = Host.CreateDefaultBuilder(args)
    // Configure a host for the endpoint
    .ConfigureLogging((context, logging) =>
    {
        logging.AddConfiguration(context.Configuration.GetSection("Logging"));

        logging.AddConsole();
    })
    .UseConsoleLifetime()
    .UseNServiceBus(context =>
    {
        // Configure the NServiceBus endpoint
        var endpointConfiguration = new EndpointConfiguration("Sender");

        var transport = endpointConfiguration.UseTransport<AzureServiceBusTransport>();
        var connectionString = context.Configuration.GetConnectionString("AzureServiceBusConnectionString");
        transport.ConnectionString(connectionString);

        transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

        endpointConfiguration.EnableInstallers();
        endpointConfiguration.AuditProcessedMessagesTo("audit");

        return endpointConfiguration;
    })
    .ConfigureServices(services => services.AddHostedService<SenderWorker>())
    .Build();

await host.RunAsync();
```

Hay mucho que desempaquetar aquí, por lo que vamos a revisarlo paso a paso.

### <a name="configure-a-host-for-the-endpoint"></a>Configuración de un host para el punto de conexión

El hospedaje y el registro se configuran usando [opciones estándar de host genérico de Microsoft](/dotnet/core/extensions/generic-host). Por ahora, el punto de conexión está configurado para ejecutarse como una aplicación de consola, pero se puede modificar para ejecutarse en Azure Functions con cambios mínimos, que se tratarán más adelante en este artículo.

### <a name="configure-the-nservicebus-endpoint"></a>Configuración del punto de conexión de NServiceBus

A continuación, va a indicarle al host que use NServiceBus con el método de extensión `.UseNServiceBus(…)`. El método toma una función de devolución de llamada que devuelve un punto de conexión que se iniciará cuando se ejecute el host.

En la configuración del punto de conexión, debe especificar `AzureServiceBus` para nuestro transporte, proporcionando una cadena de conexión de `appsettings.json`. A continuación, va a configurar el enrutamiento para que los mensajes de tipo `Ping` se envíen a un punto de conexión denominado "Receptor". Este punto de conexión permite a NServiceBus automatizar el proceso de envío del mensaje al destino sin necesidad de la dirección del receptor.

La llamada a `EnableInstallers` configurará nuestra topología en el espacio de nombres de Azure Service Bus cuando se inicie el punto de conexión, creando las colas requeridas cuando sea necesario. En entornos de producción, el [scripting operativo](https://docs.particular.net/transports/azure-service-bus/operational-scripting) es otra opción para crear la topología.

### <a name="set-up-background-service-to-send-messages"></a>Configuración de un servicio en segundo plano para enviar mensajes

El último elemento del remitente es `SenderWorker`, un servicio en segundo plano configurado para enviar un mensaje `Ping` cada segundo.

```csharp
public class SenderWorker : BackgroundService
{
    private readonly IMessageSession messageSession;
    private readonly ILogger<SenderWorker> logger;

    public SenderWorker(IMessageSession messageSession, ILogger<SenderWorker> logger)
    {
        this.messageSession = messageSession;
        this.logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        try
        {
            var round = 0;
            while (!stoppingToken.IsCancellationRequested)
            {
                await messageSession.Send(new Ping { Round = round++ })
                    .ConfigureAwait(false);

                logger.LogInformation($"Message #{round}");

                await Task.Delay(1_000, stoppingToken)
                    .ConfigureAwait(false);
            }
        }
        catch (OperationCanceledException)
        {
            // graceful shutdown
        }
    }
}
```

El objeto `IMessageSession` que se usa en `ExecuteAsync` se inserta en `SenderWorker` y permite enviar mensajes usando NServiceBus fuera de un controlador de mensajes. El enrutamiento que ha configurado en `Sender` especifica el destino de los mensajes `Ping`. Mantiene la topología del sistema (qué mensajes se enrutan a qué direcciones) como un aspecto independiente del código de negocios.

La aplicación Remitente también contiene un objeto `PongHandler`. Volverá a él después de haber analizado el receptor a continuación.

## <a name="set-up-the-receiver"></a>Configuración del receptor

El receptor es un punto de conexión que escucha un mensaje `Ping`, registra cuándo se recibe un mensaje y responde al remitente. En esta sección se revisará rápidamente la configuración del punto de conexión, que es similar al remitente y, a continuación, se prestará atención al controlador de mensajes.

Al igual que el remitente, configure el receptor como una aplicación de consola mediante el host genérico de Microsoft. El receptor usa la misma configuración de registro y punto de conexión (con Azure Service Bus como transporte de mensajes), pero con un nombre diferente, para distinguirlo del remitente:

```csharp
var endpointConfiguration = new EndpointConfiguration("Receiver");
```

Puesto que este punto de conexión solo responde a su originador y no inicia nuevas conversaciones, no es necesaria ninguna configuración de enrutamiento. Tampoco necesita un trabajo en segundo plano como el remitente, ya que solo responde cuando recibe un mensaje.

### <a name="the-ping-message-handler"></a>Controlador de mensajes Ping

El proyecto Receptor contiene un _controlador de mensajes_ denominado `PingHandler`:

```csharp
public class PingHandler : NServiceBus.IHandleMessages<Ping>
{
    private readonly ILogger<PingHandler> logger;

    public PingHandler(ILogger<PingHandler> logger)
    {
        this.logger = logger;
    }

    public async Task Handle(Ping message, IMessageHandlerContext context)
    {
        logger.LogInformation($"Processing Ping message #{message.Round}");

        // throw new Exception("BOOM");

        var reply = new Pong { Acknowledgement = $"Ping #{message.Round} processed at {DateTimeOffset.UtcNow:s}" };

        await context.Reply(reply);
    }
}
```

Ignore de momento el código comentado; volverá a él más adelante cuando se aborde la recuperación ante errores.

La clase implementa `IHandleMessages<Ping>`, que define un solo método: `Handle`. Esta interfaz indica a NServiceBus que, cuando el punto de conexión recibe un mensaje de tipo `Ping`, el método `Handle` debe procesarlo en este controlador. El método `Handle` toma el mensaje como parámetro y un objeto `IMessageHandlerContext`, que permite realizar más operaciones de mensajería, como responder, enviar comandos o publicar eventos.

El objeto `PingHandler` es sencillo: cuando se recibe un mensaje `Ping`, registra los detalles del mensajes y responde al remitente con un nuevo mensaje `Pong`.

> [!NOTE]
> En la configuración del remitente, ha especificado que los mensajes `Ping` se deben enrutar al receptor. NServiceBus agrega a los mensajes metadatos que indican, entre otras cosas, el origen del mensaje. Este es el motivo por el que no es necesario especificar datos de enrutamiento para el mensaje de respuesta `Pong`; se enruta automáticamente de vuelta a su origen: el remitente.
>

Con el remitente y el receptor configurados correctamente, ya puede ejecutar la solución.

## <a name="run-the-solution"></a>Ejecución de la solución

Para iniciar la solución, debe ejecutar tanto el remitente como el receptor. Si usa Visual Studio Code, inicie la configuración "Depurar todo". Si usa Visual Studio, configure la solución para que inicie los proyectos Remitente y Receptor:

1. Haga clic con el botón derecho en el Explorador de soluciones.
1. Seleccione "Establecer proyectos de inicio…".
1. Seleccione **Proyectos de inicio múltiples**.
1. Tanto para el remitente como para el receptor, seleccione "Iniciar" en la lista desplegable.

Ejecute la solución. Aparecerán dos aplicaciones de consola, una para el remitente y otra para el receptor.

# <a name="sender"></a>[Sender](#tab/Sender)

:::image type="content" source="./media/nservicebus/sender.png" alt-text="Imagen en la que se muestra el punto de conexión que envía mensajes Ping al receptor":::

# <a name="receiver"></a>[Receiver](#tab/Receiver)

:::image type="content" source="./media/nservicebus/receiver.png" alt-text="Imagen en la que se muestra el punto de conexión que recibe mensajes Ping del remitente":::

---

En el remitente, observe que se envía un mensaje `Ping` cada segundo, gracias al trabajo en segundo plano `SenderWorker`. El receptor muestra los detalles de cada mensaje `Ping` que recibe. Por su parte, el remitente registra los detalles de cada mensaje `Pong` que recibe.

Ahora que todo funciona, toca romperlo.

## <a name="resilience-in-action"></a>Resistencia en acción

Los errores forman parte del día a día de los sistemas de software. Es inevitable que se produzcan diversos errores en el código, como errores de red, bloqueos de bases de datos, cambios en API de terceros y errores en codificación antigua sin formato.

NServiceBus cuenta con sólidas características de capacidad de recuperación para gestionar errores. Cuando se produce un error en un controlador de mensajes, los mensajes se reintentan automáticamente en función de una directiva predefinida. Hay dos tipos de directiva de reintentos: reintentos inmediatos y reintentos retrasados. La mejor manera de describir su funcionamiento es verlos en acción. Agregue una directiva de reintentos al punto de conexión del receptor:

1. Ejecute `Program.cs` en el proyecto Remitente.
1. Antes de la línea `.EnableInstallers`, agregue el siguiente código:

```csharp
endpointConfiguration.SendFailedMessagesTo("error");
var recoverability = endpointConfiguration.Recoverability();
recoverability.Immediate(
    immediate =>
    {
        immediate.NumberOfRetries(3);
    });
recoverability.Delayed(
    delayed =>
    {
        delayed.NumberOfRetries(2);
        delayed.TimeIncrease(TimeSpan.FromSeconds(5));
    });
```

Antes de analizar cómo funciona esta directiva, va a verla en acción. Antes de probar la directiva de capacidad de recuperación, debe simular un error. Abra el código de `PingHandler` en el proyecto Receptor y quite la marca de comentario de esta línea:

```csharp
throw new Exception("BOOM");
```

Ahora, cuando el receptor gestione un mensaje `Ping`, se producirá un error. Vuelva a iniciar la solución para ver qué sucede en el receptor. 

Con el controlador `PingHandler` menos fiable, se produce un error en todos los mensajes. Puede ver que la directiva de reintentos se inicia para dichos mensajes. La primera vez que se produce un error en un mensaje, se reintente inmediatamente hasta tres veces:

:::image type="content" source="./media/nservicebus/immediate-retries.png" alt-text="Imagen en la que se muestra la directiva de reintentos inmediatos, que reintenta los mensajes hasta tres veces":::

Por supuesto, los errores seguirán produciéndose, por lo que, una vez que se usan los tres reintentos inmediatos, se activa la directiva de reintentos retrasados, y el mensaje se retrasa durante cinco segundos:

:::image type="content" source="./media/nservicebus/delayed-retries.png" alt-text="Imagen que muestra la directiva de reintentos retrasados, que retrasa los mensajes en incrementos de cinco segundos antes de intentar otra ronda de reintentos inmediatos":::

 Una vez transcurridos esos cinco segundos, el mensaje se reintenta otras tres veces (es decir, otra iteración de la directiva de reintentos inmediatos). También se producirá un error, y NServiceBus retrasará el mensaje de nuevo, esta vez durante diez segundos, antes de volver a intentarlo.

Si `PingHandler` sigue sin tener éxito después de ejecutar íntegramente la directiva de reintentos, el mensaje se coloca en una cola de errores _centralizada_ denominada `error`, tal y como se define mediante la llamada a `SendFailedMessagesTo`. 

:::image type="content" source="./media/nservicebus/failed-message.png" alt-text="Imagen en la que se muestra el mensaje con error":::

El concepto de cola de errores centralizada difiere del mecanismo de mensajes fallidos de Azure Service Bus, que tiene una cola de mensajes fallidos para cada cola de procesamiento. Con NServiceBus, las colas de mensajes fallidos de Azure Service Bus actúan como auténticas colas de mensajes dudosos, mientras que los mensajes que terminan en la cola de errores centralizada se pueden volver a procesar más adelante, si es necesario.

La directiva de reintentos ayuda a solucionar [varios tipos de errores](https://particular.net/blog/but-all-my-errors-are-severe) que suelen ser de naturaleza total o parcialmente transitoria. Es decir, errores que son temporales y a menudo desaparecen si el mensaje simplemente se vuelve a procesar después de un breve retraso (por ejemplo, errores de red, bloqueos de base de datos e interrupciones de API de terceros).

Una vez que un mensaje está en la cola de errores, puede examinar sus detalles en la herramienta que prefiera y, a continuación, decidir qué hacer con él. Por ejemplo, con [ServicePulse](https://particular.net/servicepulse), una herramienta de supervisión de Particular Software, podemos ver los detalles del mensaje y el motivo del error:

:::image type="content" source="./media/nservicebus/servicepulse.png" alt-text="Imagen en la que se muestra ServicePulse, de Particular Software":::

Después de examinar los detalles, puede devolver el mensaje a su cola original para que se procese. Antes, también puede editar el mensaje. Si hay varios mensajes en la cola de errores que no se hayan podido enviar por el mismo motivo, se pueden devolver todos a su destino original como un lote.

Ha llegado el momento de averiguar dónde implementar la solución en Azure.

## <a name="where-to-host-the-services-in-azure"></a>Dónde hospedar los servicios en Azure

En este ejemplo, los puntos de conexión del remitente y el receptor están configurados para ejecutarse como aplicaciones de consola. También se pueden hospedar en varios servicios de Azure, como Azure Functions, Azure App Service, Azure Container Instances, Azure Kubernetes Services y máquinas virtuales de Azure. Por ejemplo, a continuación se describe cómo configurar el punto de conexión del remitente para que se ejecute como una instancia de Azure Functions:

```csharp
[assembly: FunctionsStartup(typeof(Startup))]
[assembly: NServiceBusEndpointName("Sender")]

public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.UseNServiceBus(() =>
        {
            var configuration = new ServiceBusTriggeredEndpointConfiguration("Sender");
            var transport = configuration.AdvancedConfiguration.Transport;
            transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

            return configuration;
        });
    }
}
```

Para obtener más información sobre el uso de NServiceBus con Functions, consulte [Azure Functions con Azure Service Bus](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus) en la documentación de NServiceBus.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo usar NServiceBus, consulte los siguientes artículos:

- [Ejemplo de envío y respuesta de Azure Service Bus](https://docs.particular.net/samples/azure-service-bus-netstandard/send-reply/)
- [Uso de NServiceBus con Azure Functions](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus)
- [Transporte de Service Bus Azure en NServiceBus](https://docs.particular.net/transports/azure-service-bus/)
- [NServiceBus y Azure](https://docs.particular.net/nservicebus/azure/)
- [NServiceBus](https://particular.net/nservicebus)
- [Tutorial de inicio rápido de NServiceBus](https://docs.particular.net/tutorials/quickstart)
