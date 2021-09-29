---
title: Control de errores en las directivas de Azure API Management | Microsoft Docs
description: Aprenda a responder a las condiciones de error que se pueden producir durante el procesamiento de solicitudes en Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: c2ad72d55bc5056b3bb3faf0d520c07f44b4940e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647658"
---
# <a name="error-handling-in-api-management-policies"></a>Control de errores en las directivas de API Management

Azure API Management proporciona un objeto `ProxyError` que permite a los publicadores responder a las condiciones de error que se pueden producir durante el procesamiento de solicitudes. Para acceder al objeto `ProxyError` se usa la propiedad [context.LastError](api-management-policy-expressions.md#ContextVariables). Este objeto se puede usar en las directivas de la sección de directivas `on-error`. En este tema se proporciona una referencia a las funcionalidades de control de errores de Azure API Management.

## <a name="error-handling-in-api-management"></a>Control de errores en API Management

Las directivas de Azure API Management están divididas en las secciones `inbound`, `backend`, `outbound` y `on-error`, como se muestra en el ejemplo siguiente.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

Durante el procesamiento de una solicitud, se ejecutan pasos integrados junto con las directivas que están en el ámbito de la solicitud. Si se produce un error, el procesamiento salta inmediatamente a la sección de directivas `on-error`.
La sección de directivas `on-error` se puede utilizar en cualquier ámbito. Los publicadores de API pueden configurar comportamientos predeterminados, como registrar el error en los centros de eventos o crear una nueva respuesta para devolver al autor de la llamada.

> [!NOTE]
> La sección `on-error` no está presente en las directivas de forma predeterminada. Para agregar la sección `on-error` a una directiva, vaya a la directiva deseada en el editor de directivas y agréguela. Para más información sobre cómo configurar directivas, consulte [Directivas en API Management](./api-management-howto-policies.md).
>
> Si no hay ninguna sección `on-error`, los autores de la llamada recibirán mensajes de respuesta HTTP 400 o 500 si se produce una condición de error.

### <a name="policies-allowed-in-on-error"></a>Directivas permitidas en on-error

Las siguientes directivas se pueden usar en la sección de directivas `on-error`.

-   [choose](api-management-advanced-policies.md#choose)
-   [set-variable](api-management-advanced-policies.md#set-variable)
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [return-response](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)
-   [set-status](api-management-advanced-policies.md#SetStatus)
-   [send-request](api-management-advanced-policies.md#SendRequest)
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)
-   [limit-concurrency](api-management-advanced-policies.md#LimitConcurrency)
-   [mock-response](api-management-advanced-policies.md#mock-response)
-   [retry](api-management-advanced-policies.md#Retry)
-   [trace](api-management-advanced-policies.md#Trace)

## <a name="lasterror"></a>lastError

Cuando se produce un error y el control salta a la sección de directivas `on-error`, el error se almacena en la propiedad [context.LastError](api-management-policy-expressions.md#ContextVariables) a la que pueden acceder las directivas de la sección `on-error`. LastError tiene las siguientes propiedades:

| Nombre       | Tipo   | Descripción                                                                                               | Obligatorio |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | string | Nombre del elemento donde se produjo el error. Puede ser el nombre de una directiva o el nombre de un paso de canalización integrado.      | Sí      |
| `Reason`   | string | Código de error reconocible por la máquina, que se puede utilizar en el control de errores.                                       | No       |
| `Message`  | string | Descripción del error legible para el usuario.                                                                         | Sí      |
| `Scope`    | string | Nombre del ámbito donde se produjo el error; podría ser "global", "producto", "api" u "operación" | No       |
| `Section`  | string | Nombre de la sección donde se produjo el error. Valores posibles: "inbound", "backend", "outbound" u "on-error".      | No       |
| `Path`     | string | Especifica directivas anidadas, por ejemplo, "choose[3]/when[2]".                                                 | No       |
| `PolicyId` | string | Valor del atributo `id`, si lo especifica el cliente, en la directiva donde se produjo el error.             | No       |

> [!TIP]
> Se puede acceder al código de estado con context.Response.StatusCode.

> [!NOTE]
> Todas las directivas tienen un atributo `id` opcional que se pude agregar al elemento raíz de la directiva. Si este atributo está presente en una directiva cuando se produce una condición de error, el valor del atributo se puede recuperar mediante la propiedad `context.LastError.PolicyId`.

## <a name="predefined-errors-for-built-in-steps"></a>Errores predefinidos para pasos integrados

Los errores siguientes están predefinidos para condiciones de error que se pueden producir durante la evaluación de pasos de procesamiento integrados.

| Source        | Condición                                 | Motivo                  | Message                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| configuración | El identificador URI no coincide con ninguna API u operación. | OperationNotFound       | No se puede hacer coincidir la solicitud entrante con una operación.                                                                      |
| authorization | No se ha proporcionado la clave de suscripción.             | SubscriptionKeyNotFound | Acceso denegado debido a que falta la clave de suscripción. Asegúrese de incluir la clave de la suscripción al realizar solicitudes a esta API. |
| authorization | El valor de la clave de suscripción no es válido.         | SubscriptionKeyInvalid  | Acceso denegado debido a una clave de suscripción no válida. Asegúrese de proporcionar una clave válida para una suscripción activa.            |
| varios | El cliente anuló la conexión de bajada (de un cliente a una puerta de enlace de API Management) mientras la solicitud estaba pendiente. | ClientConnectionFailure | varios |
| varios | El back-end anuló o no estableció la conexión ascendente (de una puerta de enlace de API Management a un servicio back-end). | BackendConnectionFailure | varios |
| varios | Se produjo una excepción en tiempo de ejecución durante la evaluación de una expresión determinada. | ExpressionValueEvaluationFailure | varios |

## <a name="predefined-errors-for-policies"></a>Errores predefinidos en directivas

Los errores siguientes están predefinidos para condiciones de error que se pueden producir durante la evaluación de directivas.

| Source       | Condición                                                       | Motivo                    | Message                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| rate-limit   | Límite de velocidad excedido                                             | RateLimitExceeded         | Rate limit is exceeded (Se ha excedido el límite de velocidad).                                                                                                               |
| quota        | Cuota superada                                                  | QuotaExceeded             | Out of call volume quota. (Fuera de la cuota de volumen de la llamada.) La cuota se reabastecerá en xx:xx:xx. O bien, Out of bandwidth quota (Fuera de la cuota de ancho de banda). La cuota se reabastecerá en xx:xx:xx. |
| jsonp        | El valor del parámetro de devolución de llamada no es válido (contiene caracteres incorrectos). | CallbackParameterInvalid  | Value of callback parameter {callback-parameter-name} is not a valid JavaScript identifier (El valor del parámetro de devolución de llamada {callback-parameter-name} no es un identificador de JavaScript válido).                                          |
| ip-filter    | No se pudo analizar la IP de autor de llamada de la solicitud.                          | FailedToParseCallerIP     | Failed to establish IP address for the caller. Acceso denegado.                                                                        |
| ip-filter    | La IP del autor de la llamada no está en la lista de permitidos.                                | CallerIpNotAllowed        | Caller IP address {ip-address} is not allowed. Acceso denegado.                                                                        |
| ip-filter    | La IP del autor de la llamada está en la lista de bloqueados.                                    | CallerIpBlocked           | Caller IP address is blocked. Acceso denegado.                                                                                         |
| check-header | El encabezado necesario no está presente o falta un valor.               | HeaderNotFound            | Header {header-name} was not found in the request. Acceso denegado.                                                                    |
| check-header | El encabezado necesario no está presente o falta un valor.               | HeaderValueNotAllowed     | Header {header-name} value of {header-value} is not allowed. Acceso denegado.                                                          |
| validate-jwt | Falta el token Jwt en la solicitud.                                 | TokenNotFound             | JWT not found in the request. Acceso denegado.                                                                                         |
| validate-jwt | Error de validación de contraseña.                                     | TokenSignatureInvalid     | <mensaje de la biblioteca de jwt\>. Acceso denegado.                                                                                          |
| validate-jwt | Audiencia no válida.                                                | TokenAudienceNotAllowed   | <mensaje de la biblioteca de jwt\>. Acceso denegado.                                                                                          |
| validate-jwt | Emisor no válido                                                  | TokenIssuerNotAllowed     | <mensaje de la biblioteca de jwt\>. Acceso denegado.                                                                                          |
| validate-jwt | Token expirado                                                   | TokenExpired              | <mensaje de la biblioteca de jwt\>. Acceso denegado.                                                                                          |
| validate-jwt | La clave de firma no se resolvió por el identificador.                            | TokenSignatureKeyNotFound | <mensaje de la biblioteca de jwt\>. Acceso denegado.                                                                                          |
| validate-jwt | Faltan las notificaciones necesarias del token.                          | TokenClaimNotFound        | JWT token is missing the following claims (Falta el token de JWT en las siguientes notificaciones): <c1\>, <c2\>, … Acceso denegado.                                                            |
| validate-jwt | Error de coincidencia de valores de notificación.                                           | TokenClaimValueNotAllowed | Claim {claim-name} value of {claim-value} is not allowed. Acceso denegado.                                                             |
| validate-jwt | Otros errores de validación                                       | JwtInvalid                | <mensaje de la biblioteca de jwt\>.                                                                                                          |
| forward-request o send-request | No se recibieron los encabezados y el código de estado de respuesta HTTP del back-end durante el tiempo de espera configurado. | Tiempo de espera | varios |

## <a name="example"></a>Ejemplo

Establecer una directiva de API en:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

y enviar una solicitud no autorizada dará como resultado la respuesta siguiente:

![Respuesta de error no autorizado](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo trabajar con directivas, consulte:

-   [Directivas de Azure API Management](api-management-howto-policies.md)
-   [API de transformación](transform-api.md)
-   En la [Referencia de directivas](./api-management-policies.md) se muestra una lista completa de declaraciones de directivas y su configuración
-   [Ejemplos de directivas](./policy-reference.md)