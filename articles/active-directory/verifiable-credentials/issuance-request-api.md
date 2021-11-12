---
title: Especificación de la solicitud de emisión de la API REST Request Service (versión preliminar)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Aprenda a emitir una credencial verificable que ha emitido.
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 40515c37de50379f84475ad5474fb54fbbfa5f0d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440533"
---
# <a name="request-service-rest-api-issuance-specification-preview"></a>Especificación de la emisión de la API REST Request Service (versión preliminar)

Las credenciales verificables de Azure Active Directory (Azure AD) incluyen la API de REST de solicitud de servicio. Esta API permite emitir y comprobar una credencial. En este artículo se especifica la API REST del servicio de solicitudes para una solicitud de emisión.

## <a name="http-request"></a>Solicitud HTTP

La solicitud de emisión de la API de REST del servicio solicitudes admite el siguiente método HTTP:

| Método |Notas  |
|---------|---------|
|POST | Con la carga JSON que se especifica en este artículo. |

La solicitud de emisión de la API REST de servicios requiere los siguientes encabezados HTTP:

| Método |Valor  |
|---------|---------|
|`Authorization`| Adjunte el token de acceso como token de portador al encabezado de autorización de una solicitud HTTP. Por ejemplo, `Authorization: Bearer <token>`.|
|`Content-Type`| `Application/json`|

Cree una solicitud HTTP POST para la API de REST del servicio de solicitudes. Reemplace `{tenantID}` por el id. o el nombre del inquilino.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

En la solicitud HTTP siguiente se muestra una solicitud a la API REST del servicio de solicitudes:

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true, 
        "callback": {  
            "url": "https://wwww.contoso.com/vc/callback",  
            "state": "Aaaabbbb11112222", 
            "headers": { 
                "api-key": "an-api-key-can-go-here" 
              }  
        }, 
    ... 
}
```  

El siguiente permiso es necesario para llamar a la API de REST del servicio de solicitudes. Si desea más información, consulte [Concesión permisos para obtener tokens de acceso](verifiable-credentials-configure-tenant.md#grant-permissions-to-get-access-tokens).

| Tipo de permiso | Permiso  |
|---------|---------|
| Application | bbb94529-53a3-4be5-a069-7eaf2712b826/.default|

## <a name="issuance-request-payload"></a>Carga útil de solicitud de emisión

La carga útil de la solicitud de emisión contiene información sobre la solicitud de emisión de credenciales verificables. En el ejemplo siguiente se muestra una solicitud de emisión mediante un flujo de código PIN con notificaciones de usuario, como el nombre y el apellido. El resultado de esta solicitud devuelve un código QR con un vínculo para iniciar el proceso de emisión.

```json
{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```

La carga contiene las propiedades siguientes:  

|Parámetro |Tipo  | Descripción |
|---------|---------|---------|
| `includeQRCode` |  Boolean |   Determina si se incluye un código QR en la respuesta de esta solicitud. Presente el código QR y pida al usuario que lo analice. Al examinar el código QR, se inicia la aplicación de autenticación con esta solicitud de emisión. Los valores posibles son `true` (valor predeterminado) o `false`. Si establece el valor en `false`, use la propiedad `url` de devolución para representar un vínculo profundo.  |
| `authority` | string|  Identificador descentralizado del emisor (DID). Para más información, consulte [Recopilación de credenciales y detalles del entorno para configurar la aplicación de ejemplo](verifiable-credentials-configure-issuer.md).|
| `registration` | [RequestRegistration](#requestregistration-type)|  Proporciona información sobre el emisor que se puede mostrar en la aplicación de autenticación. |
| `issuance` | [RequestIssuance](#requestissuance-type)| Proporciona información sobre la solicitud de emisión.  |
|`callback`|  [Callback](#callback-type)| Permite al desarrollador obtener información de forma asincrónica sobre el flujo durante el proceso de emisión de credenciales verificable. Por ejemplo, es posible que el desarrollador quiera una llamada cuando el usuario haya examinado el código QR.|

### <a name="requestregistration-type"></a>Tipo RequestRegistration

El tipo `RequestRegistration` proporciona el registro de información para el emisor. El tipo `RequestRegistration` contiene las propiedades siguientes:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `clientName` | string|  Nombre para mostrar del emisor de la credencial verificable.  |
| `logoUrl` |  string |  Opcional. Dirección URL del logotipo del emisor.  |
| `termsOfServiceUrl` |  string | Opcional. Dirección URL de los términos de uso de la credencial verificable que se va a emitir.  |

> [!NOTE]
> En este momento, la información de `RequestRegistration` no se presenta durante la emisión en la aplicación Microsoft Authenticator. Pero esta información se puede usar en la carga.

### <a name="requestissuance-type"></a>Tipo RequestIssuance

El tipo `RequestIssuance` proporciona información necesaria para la emisión de credenciales verificables. Actualmente hay tres tipos de entrada que puede enviar en `RequestIssuance`. Las credenciales verificables de Azure AD usan estos tipos para insertar notificaciones en una credencial verificable y atestiguar esa información con el DID del emisor. A continuación, se muestran los tres tipos:

- token de identificador
- Credenciales verificables mediante una presentación comprobable
- Notificaciones con autoatestación

Puede encontrar información detallada sobre los tipos de entrada en [Personalización de la credencial verificable](credential-design.md). 

El tipo `RequestIssuance` contiene las propiedades siguientes:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `type` |  string |  El tipo de credencial verificable. Debe coincidir con el tipo tal como se define en el manifiesto de credencial verificable. Por ejemplo: `VerifiedCredentialExpert`. Para más información, vea [Creación de la tarjeta de experto de credenciales verificables en Azure](verifiable-credentials-configure-issuer.md). |
| `manifest` | string| Dirección URL del documento de manifiesto de credencial verificable. Para más información, consulte [Recopilación de credenciales y detalles del entorno para configurar la aplicación de ejemplo](verifiable-credentials-configure-issuer.md).|
| `claims` | string| Opcional. Incluya una colección de aserciones realizadas sobre el asunto en la credencial verificable. Para el flujo de código PIN, es importante que proporcione el nombre y los apellidos del usuario. Para más información, vea [Nombres de credenciales verificables](verifiable-credentials-configure-issuer.md#verifiable-credential-names). |
| `pin` | [PIN](#pin-type)| Opcional. Número de PIN para proporcionar seguridad adicional durante la emisión. Para el flujo de código PIN, esta propiedad es obligatoria. Se genera un código PIN y se presenta al usuario en la aplicación. El usuario debe proporcionar el código PIN que ha generado. |

### <a name="pin-type"></a>Tipo de PIN

El tipo `pin` define un código PIN que se puede mostrar como parte de la emisión. `pin` es opcional y, si se usa, siempre se debe enviar fuera de banda. Cuando se usa un código PIN HASH, debe definir las propiedades `salt`, `alg` y `iterations`. `pin` contiene las propiedades siguientes:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `value` | string| Contiene el valor de PIN en texto sin formato. Cuando se usa un PIN con hash, la propiedad value contiene el hash cifrado con sal, codificado en base64.|
| `type` | string|  Tipo del código PIN. Valor posible: `numeric` (predeterminado). |
| `length` | integer|  La longitud del código PIN. La longitud predeterminada es 6, la mínima es 4 y la máxima es 16.|
| `salt` | string|  Sal del código PIN con hash. La sal se antepone durante el cálculo hash. Codificación = UTF-8. |
| `alg` | string|  Algoritmo de hash para el PIN con hash. Algoritmo compatible: `sha256`. |
| `iterations` | integer| El número de iteraciones de hash. Valor posible: `1`.|

### <a name="callback-type"></a>Tipo de devolución de llamada

La API de REST del servicio de solicitudes genera varios eventos para el punto de conexión de devolución de llamada. Esos eventos permiten actualizar la interfaz de usuario y continuar con el proceso una vez que se devuelven los resultados a la aplicación. El tipo `Callback` contiene las propiedades siguientes:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `url` | string| URI al punto de conexión de devolución de llamada de la aplicación. |
| `state` | string| Se asocia al estado pasado en la carga original. |
| `headers` | string| Opcional. Puede incluir una colección de encabezados HTTP necesarios para el extremo receptor del mensaje POST. Los encabezados solo deben incluir `api-key` o cualquier encabezado necesario para la autorización.|

## <a name="successful-response"></a>Respuesta correcta

Si se completa correctamente, este método devuelve un código de respuesta (*HTTP 201 Created*) y una colección de objetos de evento en el cuerpo de la respuesta. En el siguiente código JSON se muestra una respuesta correcta:

```json
{  
    "requestId": :"799f23ea-5241-45af-99ad-cf8e5018814e",  
    "url": "openid://vc?request_uri=https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiablecredentials/request/178319f7-20be-4945-80fb-7d52d47ae82e",  
    "expiry": 1622227690,  
    "qrCode": "data:image/png;base64,iVBORw0KggoA<SNIP>"  
} 
```

La respuesta contiene las siguientes propiedades:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `requestId`| string | Id. de correlación generado automáticamente. La [devolución de llamada](#callback-events) usa la misma solicitud y permite realizar el seguimiento de la solicitud de emisión y sus devoluciones de llamada. |
| `url`|  string| Una dirección URL que inicia la aplicación de autenticación e inicia el proceso de emisión. Puede presentar esta dirección URL al usuario si no puede examinar el código QR. |
| `expiry`| integer| Indica cuándo expirará la respuesta. |
| `qrCode`| string | Código QR que el usuario puede examinar para iniciar el flujo de emisión. |

Cuando la aplicación recibe la respuesta, la aplicación debe presentar el código QR al usuario. El usuario examina el código QR, que abre la aplicación de autenticación e inicia el proceso de emisión.

## <a name="error-response"></a>Respuesta de error

También se pueden devolver respuestas de error para que la aplicación las gestione según corresponda. En el siguiente código JSON se muestra un mensaje de error no autorizado:


```json
{
    "requestId": "d60b068e7fbd975896e179b99347866a",
    "date": "Wed, 29 Sep 2021 21:49:00 GMT",
    "error": {
        "code": "unauthorized",
        "message": "Failed to authenticate the request."
    }
}
```

La respuesta contiene las siguientes propiedades:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `requestId`| string | Id. de solicitud generado automáticamente.|
| `date`| date| Hora del error. |
| `error.code` | string| Código de error devuelto. |
| `error.message`| string| El mensaje de error. |

## <a name="callback-events"></a>Eventos de devolución de llamada

Se llama al punto de conexión de devolución de llamada cuando un usuario examina el código QR, usa el vínculo profundo de la aplicación de autenticación o finaliza el proceso de emisión. 

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `requestId`| string | Se asigna a la solicitud original cuando la carga se ha publicado en el servicio de credenciales verificables.|
| `code` |string |Código devuelto cuando la aplicación autenticadora ha recuperado la solicitud. Valores posibles: <ul><li>`request_retrieved`: el usuario ha examinado el código QR o ha seleccionado el vínculo que inicia el flujo de emisión.</li><li>`issuance_successful`: la emisión de las credenciales verificables se ha realizado correctamente.</li><li>`Issuance_error`: error durante la emisión. Para obtener más información, vea la propiedad `error`.</li></ul>    |
| `state` |string| Devuelve el valor de estado que ha pasado en la carga original.   |
| `error`| error | Cuando el valor de propiedad `code` es `Issuance_error`, esta propiedad contiene información sobre el error.| 
| `error.code` | string| Código de error devuelto. |
| `error.message`| string| El mensaje de error. |

En el ejemplo siguiente se muestra una carga de devolución de llamada cuando la aplicación de autenticación inicia la solicitud de emisión:

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

En el ejemplo siguiente se muestra una carga de devolución de llamada después de que el usuario haya completado correctamente el proceso de emisión:

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",
    "code":"issuance_successful",
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
} 
```

### <a name="callback-errors"></a>Errores de devolución de llamada  

Es posible llamar al punto de conexión de devolución de llamada con un mensaje de error. En la tabla siguiente se enumeran los códigos de error:

|Mensaje  |Definición    |
|---------|---------|
| `fetch_contract_error*`| No se puede capturar el contrato de credenciales verificable. Este error se suele producir cuando la API no puede capturar el manifiesto especificado en el [objeto RequestIssuance](#requestissuance-type) de la carga de la solicitud.|
| `issuance_service_error*` | El servicio de credenciales verificables no puede validar los requisitos, o bien se ha producido un error en Credenciales verificables.|
| `unspecified_error`| Este error es poco frecuente, pero merece la pena investigarlo. |

En el ejemplo siguiente se muestra una carga de devolución de llamada cuando se produce un error:

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",  
    "code": "issuance_error",  
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",  
    "error": { 
      "code":"IssuanceFlowFailed", 
      "message":"issuance_service_error”, 
    } 
} 
``` 

## <a name="next-steps"></a>Pasos siguientes

Obtenga información [sobre cómo llamar a la API REST del servicio de solicitudes](get-started-request-api.md).
