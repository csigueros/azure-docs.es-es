---
title: Introducción a las API de .NET Standard para Azure Relay | Microsoft Docs
description: En este artículo se proporciona una introducción a la API de .NET Standard de conexiones híbridas de Azure Relay.
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 06/23/2021
ms.openlocfilehash: 827df7b0344535161053e9a61f768ef42638ca84
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668172"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Introducción a la API de estándar de .NET de las conexiones híbridas de Retransmisión de Azure

En este artículo se resumen algunas de las [API de cliente](/dotnet/api/microsoft.azure.relay) estándar de .NET de conexiones híbridas de Retransmisión de Azure.
  
## <a name="relay-connection-string-builder-class"></a>Clase Generador de cadenas de conexión de retransmisión

La clase [RelayConnectionStringBuilder][RelayConnectionStringBuilder] da formato a cadenas de conexión específicas de Conexiones híbridas de Relay. Puede usarla para comprobar el formato de una cadena de conexión o para crear una cadena de conexión desde el principio. El siguiente código es un ejemplo de ello:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

También puede pasar una cadena de conexión directamente al método `RelayConnectionStringBuilder`. Esta operación permite comprobar que la cadena de conexión tiene un formato válido. Si cualquiera de los parámetros no es válido, el constructor genera una clase `ArgumentException`.

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Flujo de conexión híbrida

La clase [HybridConnectionStream][HCStream] clase es el objeto principal que se usa para enviar y recibir datos de un punto de conexión de Retransmisión de Azure, tanto si trabaja con [HybridConnectionClient][HCClient] como si lo hace con [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Obtención de un flujo de conexión híbrida

#### <a name="listener"></a>Agente de escucha

Mediante un objeto [HybridConnectionListener][HCListener], puede obtener un objeto `HybridConnectionStream` como se indica a continuación:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Cliente

Mediante un objeto [HybridConnectionClient][HCClient], puede obtener un objeto `HybridConnectionStream` como se indica a continuación:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Recibir datos

La clase [HybridConnectionStream][HCStream] permite la comunicación bidireccional. En la mayoría de los casos, recibe datos continuamente de la secuencia. Si lee el texto de la secuencia, es posible que también desee usar un objeto [StreamReader](/dotnet/api/system.io.streamreader), que facilita el análisis de los datos. Por ejemplo, puede leer los datos como texto, en lugar de como `byte[]`.

El siguiente código lee líneas individuales de texto de la secuencia hasta que se solicita una cancelación:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Envío de datos

Una vez que tenga una conexión establecida, podrá enviar un mensaje al punto de conexión de Retransmisión. Puesto que el objeto de conexión hereda [Stream](/dotnet/api/system.io.stream), envíe los datos como `byte[]`. El ejemplo siguiente muestra cómo hacerlo:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Sin embargo, si desea enviar texto directamente, sin necesidad de codificar la cadena cada vez, puede encapsular el objeto `hybridConnectionStream` con un objeto [StreamWriter](/dotnet/api/system.io.streamwriter).

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Azure Relay, visite estos vínculos:

* [Referencia de Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [¿Qué es Relay de Azure?](relay-what-is-it.md)
* [API de Relay disponibles](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
