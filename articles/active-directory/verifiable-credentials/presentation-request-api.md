---
title: Especificación de la solicitud de comprobación de la API REST del servicio de solicitudes (versión preliminar)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Aprenda a iniciar una solicitud de presentación de credenciales verificables
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: c670f060e7849f844997c0feefd60229b90e5202
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474010"
---
# <a name="request-service-rest-api-presentation-specification-preview"></a>Especificación de presentación de la API REST del servicio de solicitudes (versión preliminar)

Las credenciales verificables de Azure Active Directory (Azure AD) incluyen la API de REST de solicitud de servicio. Esta API permite emitir y comprobar una credencial. En este artículo se especifica la API de REST del servicio de solicitudes para una solicitud de presentación. La solicitud de presentación pide al usuario que presente una credencial verificable y luego la comprueba.

## <a name="http-request"></a>Solicitud HTTP

La solicitud de presentación de la API de REST del servicio de solicitudes admite el siguiente método HTTP:

| Método |Notas  |
|---------|---------|
|POST | Con la carga JSON que se especifica en este artículo. |

La solicitud de presentación de la API de REST del servicio de solicitudes requiere los siguientes encabezados HTTP:

| Método |Valor  |
|---------|---------|
|`Authorization`| Adjunte el token de acceso como token de portador al encabezado de autorización de una solicitud HTTP. Por ejemplo, `Authorization: Bearer <token>`.|
|`Content-Type`| `Application/json`|

Cree una solicitud HTTP POST para la API de REST del servicio de solicitudes. Reemplace `{tenantID}` por el identificador o el nombre del inquilino.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

En la siguiente solicitud HTTP se muestra una solicitud de presentación a la API de REST del servicio de solicitudes:

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{  
    "includeQRCode": true,  
    "callback": {  
    "url": "https://www.contoso.com/api/verifier/presentationCallbac",  
    "state": "11111111-2222-2222-2222-333333333333",  
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

## <a name="presentation-request-payload"></a>Carga de la solicitud de presentación

La carga de la solicitud de presentación contiene información sobre su solicitud de presentación de credenciales verificables. En el siguiente ejemplo se muestra una solicitud de presentación de un emisor específico. El resultado de esta solicitud devuelve un código QR con un vínculo para iniciar el proceso de presentación.

```json
{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

La carga contiene las siguientes propiedades:  

|Parámetro |Tipo  | Descripción |
|---------|---------|---------|
| `includeQRCode` |  Boolean |   Determina si se incluye un código QR en la respuesta de esta solicitud. Presente el código QR y pida al usuario que lo digitalice. Al digitalizar el código QR, se iniciará la aplicación autenticadora con esta solicitud de presentación. Los valores posibles son `true` (valor predeterminado) o `false`. Si establece el valor en `false`, use la propiedad `url` devuelta para representar un vínculo profundo.  |
| `authority` | string|  Identificador descentralizado (DID) del inquilino de Azure AD de comprobación. Para obtener más información, consulte [Recopilación de detalles del inquilino para configurar la aplicación de ejemplo](verifiable-credentials-configure-verifier.md#gather-tenant-details-to-set-up-your-sample-application).|
| `registration` | [RequestRegistration](#requestregistration-type)|  Proporciona información sobre el comprobador. |
| `presentation` | [RequestPresentation](#requestpresentation-type)| Proporciona información sobre la solicitud de presentación de credenciales verificables.  |
|`callback`|  [Callback](#callback-type)| Permite al desarrollador actualizar la interfaz de usuario durante el proceso de presentación de credenciales verificables. Cuando el usuario complete el proceso, continúe con este una vez que los resultados se devuelvan a la aplicación.|

### <a name="requestregistration-type"></a>Tipo RequestRegistration

El tipo `RequestRegistration` proporciona el registro de información del emisor. El tipo `RequestRegistration` contiene las propiedades siguientes:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `clientName` | string|  Nombre para mostrar del emisor de la credencial verificable. Este nombre se presentará al usuario en la aplicación autenticadora. |

En la siguiente captura de pantalla se muestra la propiedad `clientName` y el nombre para mostrar de `authority` (el comprobador) en la solicitud de presentación.

![Captura de pantalla que muestra cómo aprobar la solicitud de presentación.](media/presentation-request-api/approve-presentation-request.jpg)

### <a name="requestpresentation-type"></a>Tipo RequestPresentation

El tipo `RequestPresentation` proporciona información necesaria para la presentación de credenciales verificables. `RequestPresentation` contiene las propiedades siguientes:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `includeReceipt` |  Boolean | Determina si se debe incluir un recibo en la respuesta de esta solicitud. Los valores posibles son `true` o `false` (valor predeterminado). El recibo contiene la carga original enviada desde el autenticador al servicio de credenciales verificables. El recibo es útil para solucionar problemas y no debe establecerse de manera predeterminada. En la solicitud `OpenId Connect SIOP`, el recibo contiene el token de identificación de la solicitud original. |
| `requestedCredentials` | collection| Colección de objetos [RequestCredential](#requestcredential-type).|

### <a name="requestcredential-type"></a>Tipo RequestCredential

`RequestCredential` proporciona información sobre las credenciales solicitadas que el usuario debe proporcionar. `RequestCredential` contiene las propiedades siguientes:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `type`| string| Tipo de credencial verificable. `type` debe coincidir con el tipo que se define en el manifiesto de credencial verificable `issuer` (por ejemplo, `VerifiedCredentialExpert`). Para obtener el manifiesto del emisor, vea [Recopilación de credenciales y detalles del entorno para configurar la aplicación de ejemplo](verifiable-credentials-configure-issuer.md). Copie la **dirección URL de emisión de credenciales**, ábrala en un explorador web y compruebe la propiedad **id**. |
| `purpose`| string | Proporciona información sobre el propósito de solicitar esta credencial verificable. |
| `acceptedIssuers`| string collection | DID de una colección de emisores que podría emitir el tipo de credencial verificable que los sujetos pueden presentar. Para obtener el DID del emisor, vea [Recopilación de credenciales y detalles del entorno para configurar la aplicación de ejemplo](verifiable-credentials-configure-issuer.md) y copie el valor del **identificador descentralizado (DID)** . |

### <a name="callback-type"></a>Tipo de devolución de llamada

La API de REST del servicio de solicitudes genera varios eventos para el punto de conexión de devolución de llamada. Esos eventos permiten actualizar la interfaz de usuario y continuar con el proceso una vez que se han devuelto los resultados a la aplicación. El tipo `Callback` contiene las propiedades siguientes:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `url` | string| URI al punto de conexión de devolución de llamada de la aplicación. |
| `state` | string| Se asocia al estado pasado en la carga original. |
| `headers` | string| Opcional. Puede incluir una colección de encabezados HTTP que necesita el extremo receptor del mensaje POST. Los encabezados solo deben incluir `api-key` o cualquier encabezado necesario para la autorización.|

## <a name="successful-response"></a>Respuesta correcta

Si se ejecuta correctamente, este método devuelve un código de respuesta (*HTTP 201 Created*) y una colección de objetos de evento en el cuerpo de la respuesta. En el siguiente código JSON se muestra una respuesta correcta:

```json
{  
    "requestId": "e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "url": "openid://vc/?request_uri=https://beta.did.msidentity.com/v1.0/87654321-0000-0000-0000-000000000000/verifiablecredentials/request/e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "expiry": 1633017751,
    "qrCode": "data:image/png;base64,iVBORw0KGgoA<SNIP>"
} 
```

La respuesta contiene las siguientes propiedades:

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `requestId`| string | Id. de correlación generado automáticamente. La [devolución de llamada](#callback-events) usa la misma solicitud, lo que permite realizar el seguimiento de la solicitud de presentación y sus devoluciones de llamada. |
| `url`|  string| Dirección URL que inicia la aplicación autenticadora y pone en marcha el proceso de presentación. Puede presentar esta dirección URL al usuario si no puede digitalizar el código QR. |
| `expiry`| integer| Indica cuándo va a expirar la respuesta. |
| `qrCode`| string | Código QR que el usuario puede digitalizar para iniciar el flujo de presentación. |

Cuando reciba la respuesta, su aplicación debe mostrar el código QR al usuario. El usuario digitaliza el código QR, lo que abre la aplicación autenticadora e inicia el proceso de presentación.

## <a name="error-response"></a>Respuesta de error

También se pueden devolver respuestas de error para que la aplicación las gestione según corresponda. En el siguiente código JSON se muestra un mensaje de error no autorizado:


```json
{
    "requestId": "fb888ac646c96083de83b099b2678de0",
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
| `date`| date | Hora del error. |
| `error.code` | string | Código de error devuelto. |
| `error.message`| string | El mensaje de error. |

## <a name="callback-events"></a>Eventos de devolución de llamada

Se llama al punto de conexión de devolución de llamada cuando un usuario digitaliza el código QR, usa el vínculo profundo de la aplicación autenticadora o finaliza el proceso de presentación. 

|Propiedad |Tipo |Descripción |
|---------|---------|---------|
| `requestId`| string | Se asigna a la solicitud original cuando la carga se ha publicado en el servicio de credenciales verificables.|
| `code` |string |Código devuelto cuando la aplicación autenticadora ha recuperado la solicitud. Valores posibles: <ul><li>`request_retrieved`: el usuario ha digitalizado el código QR o ha seleccionado el vínculo que inicia el flujo de presentación.</li><li>`presentation_verified`: la validación de la credencial verificable se ha completado correctamente.</li></ul>    |
| `state` |string| Devuelve el valor de estado que ha pasado en la carga original.   |
| `subject`|string | DID de usuario de la credencial verificable.|
| `issuers`| array |Devuelve una matriz de credenciales verificables solicitadas. En cada credencial verificable, proporciona: </li><li>Tipo de credencial verificable</li><li>Notificaciones recuperadas</li><li>Dominio del emisor de la credencial verificable </li><li>Estado de validación del dominio del emisor de la credencial verificable </li></ul> |
| `receipt`| string | Opcional. El recibo contiene la carga original enviada desde el autenticador a las credenciales verificables.  |

En el siguiente ejemplo se muestra una carga de devolución de llamada cuando la aplicación autenticadora inicia la solicitud de presentación:

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

En el siguiente ejemplo se muestra una carga de devolución de llamada después de que se haya completado correctamente la presentación de la credencial verificable:

```json
{
  "requestId": "87e1cb24-9096-409f-81cb-9e645f23a4ba",
  "code": "presentation_verified",
  "state": "f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
  "subject": "did:ion:EiAlrenrtD3Lsw0GlbzS1O2YFdy3Xtu8yo35W<SNIP>…",
  "issuers": [
    {
      "type": [
        "VerifiableCredential",
        "VerifiedCredentialExpert"
      ],
      "claims": {
        "firstName": "John",
        "lastName": "Doe"
      },
      "domain": "https://contoso.com/",
      "verified": "DNS",
      "issuer": "did:ion:….."
    }
  ],
  "receipt": {
    "id_token": "eyJraWQiOiJkaWQ6aW<SNIP>"
  }
} 
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre [cómo llamar a la API de REST de solicitud de servicio](get-started-request-api.md).
