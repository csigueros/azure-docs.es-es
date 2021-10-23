---
title: Contrato de token de Azure Fluid Relay
description: Descripción detallada del JSON Web Token empleado en Azure Fluid Relay
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: 3fe063a6d5b3c76d300a088372e9dee08d9d79c0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661686"
---
# <a name="azure-fluid-relay-token-contract"></a>Contrato de token de Azure Fluid Relay

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Las solicitudes enviadas a Azure Fluid Relay deben contener un token JWT en el encabezado de autorización. Este token debe estar [firmado por la clave de inquilino](../concepts/authentication-authorization.md).

## <a name="claims"></a>Notificaciones

Los JWT (JSON Web Token) se dividen en tres partes: 

- **Encabezado**: proporciona información sobre cómo validar el token, incluida la información sobre el tipo de token y cómo fue firmado. 
- **Carga**: contiene todos los datos importantes sobre el usuario o la aplicación que está intentando llamar a su servicio. 
- **Firma**: es la materia prima utilizada para validar el token. 

Cada parte se separa por un punto (.) y se codifica por separado en Base64. 

## <a name="header-claims"></a>Notificaciones de encabezado

| Notificación | Formato | Descripción  |
|-------|--------|--------------|
| alg   | string | El algoritmo utilizado para firmar el token. Por ejemplo, "HS256" |
| typ   | string | Este valor siempre debe ser "JWT". |

## <a name="payload-claims"></a>Notificaciones de carga

| Notificación      | Formato                   | Descripción |
|------------|--------------------------|-------------|
| documentId | string                   | Generado por FRS, identifica el documento para el que se genera el token. |
| scope      | string[]                 | Identifica los permisos que requiere el cliente en el documento o resumen. Para cada ámbito, puede definir los permisos que desea conceder al cliente.  |
| tenantId   | string                   | Identifica el inquilino. |
| usuario       | JSON                     | *Opcional* `{ displayName: <display_name>, id: <user_id>, name: <user_name>, }` Identifica los usuarios de la aplicación. Esto se envía de vuelta a la aplicación mediante Alfred, el servicio de ordenación.  La aplicación puede usarlo para identificar a los usuarios a partir de la respuesta que obtiene de Alfred. Azure Fluid Relay no valida esta información. |
| iat        | numero, una marca de tiempo de UNIX | La notificación "iat" (emitido a las) indica cuándo se produjo la autenticación de este token. |
| exp        | numero, una marca de tiempo de UNIX | La notificación "exp" (fecha de expiración) identifica la hora de expiración en la que o después de la que el token JWT no debe ser aceptado para su procesamiento. La vigencia del token no puede ser superior a 1 hora. |
| ver        | string                   | Indica la versión del token de acceso. Debe ser `1.0`. |
| jti        | string                   | *Opcional.*  La notificación "jti" (Id. de JWT) proporciona un identificador único para el JWT. El valor del identificador se DEBE asignar de manera que se garantice que la probabilidad de que el mismo valor se asigne accidentalmente a un objeto de datos diferente sea insignificante. Le recomendamos que use esta notificación para evitar errores debidos al uso del mismo token para la creación de documentos.  |

## <a name="a-sample-azure-fluid-relay-token"></a>Un token de ejemplo de Azure Fluid Relay

```typescript
{ 
  "alg": "HS256",  
  "typ": "JWT" 
}.{ 
  "documentId": "746c4a6f-f778-4970-83cd-9e21bf88326c", 
  "scopes": [ "doc:read", "doc:write", "summary:write" ],   
  "iat": 1599098963,  
  "exp": 1599098963,  
  "tenantId": "AzureFluidTenantId",  
  "ver": "1.0",
  "jti": "d7cd6602-2179-11ec-9621-0242ac130002"
}.[Signature] 
```

## <a name="how-can-you-generate-an-azure-fluid-relay-token"></a>¿Cómo se puede generar un token de Azure Fluid Relay? 

Puede usar el paquete NPM [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) y firmar el token mediante este método.

```typescript
export function getSignedToken(
    tenantId: string,
    documentId: string,
    tokenLifetime: number = 60 * 60,
    ver: string = "1.0") {
        jwt.sign(
            {
                documentId, 
                user: {
                    displayName: "displayName", 
                    id: "userId", 
                    name: "userName" 
                }, 
                scopes: ["doc:read", "doc:write", "summary:write"], 
                iat: Math.round((new Date()).getTime() / 1000), 
                exp: Math.round((new Date()).getTime() / 1000) + tokenLifetime, //set the expiry date based on your needs but max-limit is one hour.
                tenantId, 
                ver,
                jti: uuid(), 
            },
            "<tenant_key>");
    }
```
