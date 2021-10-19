---
title: Conversión de texto codificado no Unicode según la compatibilidad
description: Administre los caracteres no Unicode en Azure Logic Apps mediante la conversión de cargas de texto a UTF-8 con codificación base64 y Azure Functions.
ms.date: 10/05/2021
ms.topic: how-to
ms.reviewer: estfan, azla
ms.service: logic-apps
ms.openlocfilehash: 1251a1622e62b7940c25ac810db10f70da560000
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618997"
---
# <a name="support-non-unicode-character-encoding-in-logic-apps"></a>Compatibilidad con la codificación de caracteres no Unicode en Logic Apps

Cuando se trabaja con cargas de texto, Azure Logic Apps deduce que el texto se codifica en un formato Unicode, como UTF-8. Es posible que tenga problemas para recibir, enviar o procesar caracteres con codificaciones diferentes en el flujo de trabajo. Por ejemplo, podría obtener caracteres dañados en archivos planos al trabajar con sistemas heredados que no admiten Unicode.

Para trabajar con texto que tenga otra codificación de caracteres, aplique la codificación base64 a la carga no Unicode. Este paso evita que Logic Apps dé por supuesto que el texto está en formato UTF-8. A continuación, puede convertir cualquier codificación compatible con .NET a UTF-8 mediante Azure Functions. 

Esta solución funciona con flujos de trabajo *multiinquilino* y de *un solo inquilino*. También puede [utilizar esta solución con el conector AS2](#convert-payloads-for-as2).

## <a name="convert-payload-encoding"></a>Conversión de la codificación de carga

En primer lugar, compruebe que el desencadenador puede identificar correctamente el tipo de contenido. Este paso garantiza que Logic Apps ya no dé por supuesto que el texto es UTF-8. 

En desencadenadores y acciones que tienen la propiedad **Inferir tipo de contenido**, seleccione **No**.  Normalmente puede encontrar esta propiedad en la lista **Agregar parámetros** de la operación. Sin embargo, si la operación no incluye esta propiedad, el tipo de contenido se establece mediante el mensaje entrante.

En la lista siguiente se muestran algunos de los conectores en los que puede deshabilitar la inferencia automática del tipo de contenido:
* [OneDrive](/connectors/onedrive/)
* [Azure Blob Storage](/connectors/azureblob/)
* [Azure File Storage](/connectors/azurefile/)
* [Sistema de archivos](/connectors/filesystem/)
* [Google Drive](/connectors/googledrive/)
* [SFTP - SSH](/connectors/sftpwithssh/)
 
Si usa el desencadenador de solicitud para el contenido `text/plain`, debe establecer el parámetro `charset` que se encuentra en el encabezado `Content-Type` de la llamada. De lo contrario, los caracteres pueden dañarse o si el parámetro no coincide con el formato de codificación de la carga. Para obtener más información, consulte [cómo controlar el tipo de contenido `text/plain`](logic-apps-content-type.md#text-plain).

Por ejemplo, el desencadenador HTTP convierte el contenido entrante en UTF-8 cuando el encabezado `Content-Type` se establece con el parámetro `charset` correcto:

```json
{
    "headers": {
        <...>
        "Content-Type": "text/plain; charset=windows-1250"
        },
        "body": "non UTF-8 text content"
}
```

Si establece el encabezado `Content-Type` en `application/octet-stream`, también podría recibir caracteres que no sean UTF-8. Para obtener más información, vea [cómo controlar el tipo de contenido `application/octet-stream`](logic-apps-content-type.md#applicationxml-and-applicationoctet-stream).

## <a name="base64-encode-content"></a>Contenido de codificación Base64

Antes de [codificar contenido en base64](workflow-definition-language-functions-reference.md#base64), asegúrese de que [ha convertido el texto a UTF-8](#convert-payload-encoding). Si descodifica el contenido en base64 en una cadena antes de convertir el texto a UTF-8, los caracteres podrían devolverse dañados.

A continuación, convierta cualquier codificación compatible con .NET a otra codificación compatible con .NET. Revise el [ejemplo de código de Azure Functions](#azure-functions-version) o el [ejemplo de código de .NET](#net-version):

> [!TIP]
> En el caso de las aplicaciones lógicas de *un solo inquilino*, puede mejorar el rendimiento y reducir la latencia mediante la ejecución local de la función de conversión.

### <a name="azure-functions-version"></a>Versión de Azure Functions

Este ejemplo va dirigido a la versión 2 de Azure Functions: 

```csharp
using System;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

public static class ConversionFunctionv2 {
  [FunctionName("ConversionFunctionv2")]
  public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, TraceWriter log) {
    log.Info("C# HTTP trigger function processing a request.");

    Encoding inputEncoding = null;

    string requestBody = new StreamReader(req.Body).ReadToEnd();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null) {
      return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
    }

    Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

    try {
      string encodingInput = data.encodingInput.Value;
      inputEncoding = Encoding.GetEncoding(name: encodingInput);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    Encoding encodingOutput = null;
    try {
      string outputEncoding = data.encodingOutput.Value;
      encodingOutput = Encoding.GetEncoding(outputEncoding);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    return (ActionResult) new JsonResult(
      value: new {
        text = Convert.ToBase64String(
          Encoding.Convert(
            srcEncoding: inputEncoding,
            dstEncoding: encodingOutput,
            bytes: Convert.FromBase64String((string) data.text)))
      });
  }
}
```

### <a name="net-version"></a>Versión de .NET

El ejemplo siguiente se usa con **.NET Standard** y la **versión 2** de Azure Functions:

```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json;

    public static class ConversionFunctionNET
    {
        [FunctionName("ConversionFunctionNET")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequest req, TraceWriter log)
        {
            log.Info("C# HTTP trigger function processing a request.");

            Encoding inputEncoding = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null)
            {
                return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
            }

            Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

            try
            {
                string encodingInput = data.encodingInput.Value;
                inputEncoding = Encoding.GetEncoding(name: encodingInput);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            Encoding encodingOutput = null;
            try
            {
                string outputEncoding = data.encodingOutput.Value;
                encodingOutput = Encoding.GetEncoding(outputEncoding);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            return (ActionResult)new JsonResult(
                value: new
                {
                    text = Convert.ToBase64String(
                        Encoding.Convert(
                            srcEncoding: inputEncoding,
                            dstEncoding: encodingOutput,
                            bytes: Convert.FromBase64String((string)data.text)))
                });
        }
    }
```

Con estos mismos conceptos, también puede [enviar una carga no Unicode desde el flujo de trabajo](#send-non-unicode-payload).

## <a name="sample-payload-conversions"></a>Conversiones de carga de ejemplo

En este ejemplo, la cadena de entrada de ejemplo codificada en base64 es un nombre personal, *H&eacute;lo&iuml;se*, que contiene caracteres acentuados.

Entrada de ejemplo:

```json
{  
    "text": "SMOpbG/Dr3Nl",
    "encodingInput": "utf-8",
    "encodingOutput": "windows-1252"
}
```

Salida de ejemplo:

```json
{
    "text": "U01PcGJHL0RyM05s"
}
```

## <a name="send-non-unicode-payload"></a>Envío de una carga no Unicode

Si necesita enviar una carga no Unicode desde el flujo de trabajo, siga los pasos para [convertir la carga a UTF-8](#convert-payload-encoding) en orden inverso. Mantenga el texto en UTF-8 tanto como sea posible dentro del sistema. A continuación, use la misma función para convertir los caracteres UTF-8 codificados en base64 a la codificación necesaria. Después, aplique la codificación en base 64 al texto y envíe la carga útil.

## <a name="convert-payloads-for-as2"></a>Conversión de cargas para AS2

También puede usar esta solución con cargas no Unicode en el [conector AS2 v2](logic-apps-enterprise-integration-as2.md). Si no convierte las cargas que pasa a AS2 a UTF-8, puede experimentar problemas con la interpretación de la carga. Estos problemas pueden dar lugar a una discrepancia con el hash MIC entre los asociados debido a caracteres mal interpretados.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Agregar una acción de descodificación de archivo plano](logic-apps-enterprise-integration-flatfile.md)
