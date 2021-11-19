---
title: Envío de una solicitud HTTP con C#
description: En este tutorial se explica la versión de C# de firma de una solicitud HTTP con una firma HMAC en Azure Communication Services.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 06/30/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 1ed686906a8233d0300b43a03d03127f870e3ae5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132430055"
---
## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, compruebe lo siguiente:

- Cree de una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale [Visual Studio](https://visualstudio.microsoft.com/downloads/).
- Cree un recurso de Azure Communication Services. Para más información, consulte [Creación de un recurso de Azure Communication Services](../../quickstarts/create-communication-resource.md). En este tutorial, deberá registrar los valores **resourceEndpoint** y **resourceAccessKey**.

## <a name="sign-an-http-request-with-c"></a>Envío de una solicitud HTTP con C#

La autenticación de clave de acceso usa una clave secreta compartida para generar una firma HMAC para cada solicitud HTTP. Esta firma se genera con el algoritmo SHA256 y se envía en el encabezado `Authorization` mediante el esquema `HMAC-SHA256`. Por ejemplo:

```
Authorization: "HMAC-SHA256 SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature` consta de:

- Verbo HTTP (por ejemplo, `GET` o `PUT`)
- Ruta de acceso de la solicitud HTTP
- x-ms-date
- administrador de flujos de trabajo
- x-ms-content-sha256

## <a name="setup"></a>Configurar

En los pasos siguientes se describe cómo construir el encabezado de autorización.

### <a name="create-a-new-c-application"></a>Creación de una aplicación de C#

En una ventana de la consola, como cmd, PowerShell o Bash, use el comando `dotnet new` para crear una aplicación de consola con el nombre `SignHmacTutorial`. Este comando crea un sencillo proyecto "Hola mundo" de C# con un solo archivo de origen: **Program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Cambie el directorio a la carpeta de aplicaciones recién creada. Use el comando `dotnet build` para compilar la aplicación.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Instalar el paquete

Instale el paquete `Newtonsoft.Json` que se usa en la serialización del cuerpo.

```console
dotnet add package Newtonsoft.Json
```

Actualice la declaración del método `Main` para admitir código asincrónico. Use el código siguiente para empezar.

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here.
        }
    }
}

```

## <a name="create-a-request-message"></a>Creación de un mensaje de solicitud

En este ejemplo se firma una solicitud para crear una identidad mediante Authentication API de Azure Communication Services (versión `2021-03-07`).

Agregue el siguiente código al método `Main`.

```csharp
string resourceEndpoint = "resourceEndpoint";
// Create a uri you are going to call.
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
// Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Reemplace `resourceEndpoint` por el valor real del punto de conexión del recurso.

## <a name="create-a-content-hash"></a>Creación de un hash de contenido

El código hash de contenido es parte de la firma HMAC. Agregue el código siguiente para procesar el código hash de contenido. Puede agregar este método a `Progam.cs` en el método `Main`.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Procesamiento de una firma

Use el código siguiente para crear un método para procesar una firma HMAC.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Reemplace `resourceAccessKey` por la clave de acceso del recurso real de Communication Services.

## <a name="create-an-authorization-header-string"></a>Creación de una cadena de encabezado de autorización

Ahora crearemos la cadena que agregaremos a nuestro encabezado de autorización.

1. Prepare los valores de los encabezados que se van a firmar.
   1. Especifique la marca de tiempo actual mediante la zona horaria hora universal coordinada (UTC).
   1. Obtenga la entidad de solicitud (nombre de host DNS o dirección IP y el número de puerto).
   1. Procese un hash de contenido.
1. Prepare una cadena para firmar.
1. Procese la firma.
1. Concatene la cadena, que se usará en el encabezado de autorización.
 
Agregue el siguiente código al método `Main`.

```csharp
// Specify the 'x-ms-date' header as the current UTC timestamp according to the RFC1123 standard
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
// Get the host name corresponding with the 'host' header.
var host = requestUri.Authority;
// Compute a content hash for the 'x-ms-content-sha256' header.
var contentHash = ComputeContentHash(serializedBody);

// Prepare a string to sign.
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{host};{contentHash}";
// Compute the signature.
var signature = ComputeSignature(stringToSign);
// Concatenate the string, which will be used in the authorization header.
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Incorporación de encabezados a requestMessage

Use el código siguiente para agregar los encabezados necesarios a `requestMessage`.

```csharp
// Add a date header.
requestMessage.Headers.Add("x-ms-date", date);

// Add a host header.
// In C#, the 'host' header is added automatically by the 'HttpClient'. However, this step may be required on other platforms such as Node.js.

// Add a content hash header.
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);

// Add an authorization header.
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Probar el cliente

Llame al punto de conexión mediante `HttpClient` y compruebe la respuesta.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
