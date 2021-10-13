---
title: Enlaces de salida HTTP de Azure Functions
description: Obtenga información sobre cómo devolver respuestas HTTP en Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 70d01dce8e5879b6b075c2ea9e305ebf90d57e18
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658888"
---
# <a name="azure-functions-http-output-bindings"></a>Enlaces de salida HTTP de Azure Functions

Use el enlace de salida HTTP para responder al remitente de la solicitud HTTP. Este enlace requiere un desencadenador HTTP y le permite personalizar la respuesta asociada con la solicitud del desencadenador.

El valor devuelto predeterminado para una función desencadenada por HTTP es:

- `HTTP 204 No Content` con el cuerpo vacío en Functions 2.x y posterior
- `HTTP 200 OK` con el cuerpo vacío en Functions 1.x

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json*. En las bibliotecas de clases de C#, no hay ninguna propiedad de atributo que corresponda a estas propiedades *function.json*.

|Propiedad  |Descripción  |
|---------|---------|
| **type** |Se debe establecer en `http`. |
| **direction** | Se debe establecer en `out`. |
| **name** | Nombre de la variable usado en el código de la función para la respuesta, o `$return` para usar el valor devuelto. |

## <a name="usage"></a>Uso

Para enviar una respuesta HTTP, use los patrones de respuesta estándar del lenguaje. En C# o un script de C#, haga que la función devuelva el tipo `IActionResult` o `Task<IActionResult>`. En C#, no se requiere un atributo de valor devuelto.

Para obtener respuestas de ejemplo, vea el [ejemplo de desencadenador](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>configuración de host.json

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

> [!NOTE]
> Para obtener una referencia de host.json en Functions 1.x, consulte la [referencia de host.json para Azure Functions, versión 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
| customHeaders|None|Permite establecer encabezados personalizados en la respuesta HTTP. En el ejemplo anterior se agrega el encabezado `X-Content-Type-Options` a la respuesta para evitar el examen de tipos de contenido. |
|dynamicThrottlesEnabled|true<sup>\*</sup>|Cuando se habilita, esta configuración hace que la canalización de procesamiento de la solicitud compruebe periódicamente contadores de rendimiento del sistema, como `connections/threads/processes/memory/cpu/etc` y, si cualquiera de esos contadores superan un umbral alto integrado (80 %), las solicitudes se rechazarán con una respuesta `429 "Too Busy"` hasta que los contadores vuelvan a niveles normales.<br/><sup>\*</sup>El valor predeterminado en un plan de consumo es `true`. El valor predeterminado en un plan dedicado es `false`.|
|hsts|no habilitado|Cuando `isEnabled` se establece en `true`, se aplica [el comportamiento de la Seguridad de transporte estricta de HTTP (HSTS) de .NET Core](/aspnet/core/security/enforcing-ssl?tabs=visual-studio#hsts), tal como se define en la [ clase `HstsOptions`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions). En el ejemplo anterior también se establece la propiedad [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) en 10 días. Las propiedades de `hsts` que se admiten son: <table><tr><th>Propiedad</th><th>Descripción</th></tr><tr><td>excludedHosts</td><td>Matriz de cadenas de nombres de host para los que no se agrega el encabezado HSTS.</td></tr><tr><td>includeSubDomains</td><td>Valor booleano que indica si está habilitado el parámetro includeSubDomain del encabezado Strict-Transport-Security.</td></tr><tr><td>maxAge</td><td>Cadena que define el parámetro max-age del encabezado Strict-Transport-Security.</td></tr><tr><td>preload</td><td>Valor booleano que indica si está habilitado el parámetro preload del encabezado Strict-Transport-Security.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Número máximo de funciones HTTP que se ejecutan en paralelo. Este valor permite controlar la simultaneidad, que a su vez puede ayudar a administrar el uso de recursos. Por ejemplo, podría tener una función HTTP que utiliza una gran cantidad de recursos del sistema (memoria/cpu/sockets) y causa problemas cuando la simultaneidad es demasiado alta. O bien podría tener una función que realiza solicitudes de salida a un servicio de terceros y puede que haya que limitar la velocidad de dichas llamadas. En estos casos puede ayudar aplicar una limitación. <br/><sup>*</sup>El valor predeterminado para un plan de consumo es 100. El valor predeterminado para un plan dedicado es ilimitado (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|Número máximo de solicitudes pendientes que se mantienen en un momento dado. Este límite incluye las solicitudes que están en cola pero no han empezado a ejecutarse, así como todas las ejecuciones en curso. Se rechazan todas las solicitudes entrantes que superen este límite con una respuesta 429 "Too Busy" (demasiado ocupado). Esto permite que los llamadores empleen estrategias de reintento basadas en tiempo y también le ayuda a controlar las latencias de solicitud máximas. Únicamente se controlan los movimientos de la cola que se producen dentro de la ruta de ejecución del host del script. Otras colas, como la cola de solicitudes de ASP.NET, siguen en efecto y no se ven alteradas por esta opción de configuración. <br/><sup>\*</sup>El valor predeterminado para un plan de consumo es 200. El valor predeterminado para un plan dedicado es ilimitado (`-1`).|
|routePrefix|api|Prefijo de ruta que se aplica a todas las rutas. Use una cadena vacía para quitar el prefijo predeterminado. |

## <a name="next-steps"></a>Pasos siguientes

- [Ejecución de una función desde una solicitud HTTP](./functions-bindings-http-webhook-trigger.md)
