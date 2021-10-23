---
title: Autenticación y autorización en una aplicación
description: Información general sobre cómo utilizar la autenticación y autorización con un servicio Azure Fluid Relay.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/auth/
ms.openlocfilehash: fc3e55a91af1e7691d1d2677435c283914521dfe
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661692"
---
# <a name="authentication-and-authorization-in-your-app"></a>Autenticación y autorización en una aplicación

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

La seguridad es fundamental para las aplicaciones web modernas. Fluid Framework, como parte de la arquitectura de la aplicación web, es una pieza importante de la infraestructura que se debe proteger. Fluid Framework es una arquitectura por capas, y los conceptos relacionados con la autenticación se implementan en función del servicio Fluid al que se conecta. Esto significa que, aunque hay temas de autenticación comunes en todos los servicios de Fluid, los detalles y las características específicas serán diferentes para cada servicio.

## <a name="azure-fluid-relay-service"></a>Servicio Azure Fluid Relay

A cada inquilino del servicio Azure Fluid Relay que cree se le asigna un **identificador de inquilino** y su propia **clave secreta de inquilino** única.

La clave secreta es un **secreto compartido**. La aplicación o el servicio lo sabe, y el servicio Azure Fluid Relay también. Como la clave secreta de inquilino está asociada de forma única al inquilino, su uso para firmar solicitudes garantiza al servicio Azure Fluid Relay que las solicitudes proceden de un usuario autorizado del inquilino.

La clave secreta es cómo sabe el servicio Azure Fluid Relay que las solicitudes proceden de su aplicación o servicio. Esto es fundamental, ya que una vez que el servicio Azure Fluid Relay puede confiar en que es *su aplicación* la que realiza las solicitudes, puede confiar en los datos que envíe. Este es también el motivo por el que es importante que el secreto se controle de forma segura.

> [!CAUTION]
> Alguien con acceso al secreto puede suplantar a su aplicación al comunicarse con el servicio Azure Fluid Relay.

## <a name="json-web-tokens-and-azure-fluid-relay-service"></a>JSON Web Tokens y el servicio Azure Fluid Relay

Azure Fluid Relay utiliza [JSON Web Tokens (JWT)](https://jwt.io/) para codificar y comprobar los datos firmados con la clave secreta. JSON Web Tokens son un bit de JSON firmado que puede incluir información adicional sobre derechos y permisos.

> [!NOTE]
> Las características específicas de los JWT están fuera del ámbito de este artículo. Para más detalles sobre el estándar JWT, consulte <https://jwt.io/introduction>.

Aunque los detalles de la autenticación difieren entre los servicios Fluid, siempre deben estar presentes varios valores.

- **containerId**: el servicio Fluid necesita el identificador de contenedor para identificar qué servicio corresponde al contenedor que realiza la llamada. *Nota*: JWT llama a este campo documentId, pero el servicio Fluid espera un identificador de contenedor en este campo.
- **tenantId**: el servicio Azure Fluid Relay utiliza el identificador de inquilino para recuperar el secreto compartido que usará para autenticar su solicitud. 
- **scopes**: los ámbitos definen los permisos del contenedor que realiza la llamada. El contenido del campo de ámbitos es flexible, lo que le permite crear sus propios permisos personalizados.

```json {linenos=inline,hl_lines=["5-6",13]}
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "documentId": "azureFluidDocumentId",
  "scopes": [ "doc:read", "doc:write", "summary:write" ],
  "user": {
    "name": "TestUser",
    "id": "Test-Id-123"
  },
  "iat": 1599098963,
  "exp": 1599098963,
  "tenantId": "AzureFluidTenantId",
  "ver": "1.0"
}.[Signature]
```

> [!NOTE]
> Tenga en cuenta que el token también incluye información de usuario (consulte las líneas 7 a 9 anteriores). Puede usarlo para aumentar la información del usuario que está disponible automáticamente para el código de Fluid mediante la característica [audiencia](../how-tos/connect-fluid-azure-service.md#getting-audience-details). Para más información, consulte [Incorporación de datos personalizados a tokens](../how-tos/connect-fluid-azure-service.md#adding-custom-data-to-tokens).

## <a name="the-token-provider"></a>El proveedor de tokens

Todas las solicitudes a Azure Fluid Relay se deben firmar con un JWT válido. Fluid delega la responsabilidad de crear y firmar estos tokens en los proveedores de tokens.

Un proveedor de tokens es responsable de crear y firmar tokens que utiliza `@fluidframework/azure-client` para realizar solicitudes al servicio Azure Fluid Relay. Debe proporcionar su propia implementación de proveedor de tokens seguros. Sin embargo, Fluid proporciona un `InsecureTokenProvider` que acepta el secreto de inquilino y, después, genera y devuelve localmente un token firmado. Este proveedor de tokens resulta útil para las pruebas, pero no se puede utilizar en escenarios de producción.

### <a name="a-secure-serverless-token-provider"></a>Un proveedor de tokens sin servidor seguros

Una opción para crear un proveedor de tokens seguros es crear una función de Azure sin servidor y exponerla como proveedor de tokens. Esto le permite almacenar la *clave secreta de inquilino* en un servidor seguro. A continuación, la aplicación llamaría a la función de Azure para generar tokens en lugar de firmarlos localmente como hace `InsecureTokenProvider`. Para más información, consulte [Escritura de un TokenProvider con una función de Azure](../how-tos/azure-function-token-provider.md).

## <a name="connecting-user-auth-to-fluid-service-auth"></a>Conexión de la autenticación de usuario a la autenticación del servicio Fluid

Los servicios Fluid autentican las llamadas entrantes mediante un secreto de cliente compartido, que no está asociado a un usuario específico. La autenticación de usuario se puede agregar en función de los detalles del servicio Fluid. 

Una opción sencilla para la autenticación de usuario sería simplemente usar una [función de Azure](../../azure-functions/index.yml) como proveedor de tokens y aplicar la autenticación de usuario como condición para obtener un token. Si una aplicación intenta llamar a la función, se producirá un error a menos que se autentique con el sistema de autenticación. Si usa Azure Active Directory (Azure AD), por ejemplo, puede crear una aplicación de Azure AD para la función de Azure y vincularla al sistema de autenticación de su organización.

En este caso, el usuario iniciaría sesión en la aplicación mediante Azure AD, a través del cual obtendría un token que podría utilizar para llamar a la función de Azure. La propia función de Azure se comporta igual, pero ahora solo es accesible para aquellos que también se hayan autenticado con Azure AD.

Puesto que la función de Azure es ahora el punto de entrada para obtener un token válido, solo los usuarios que se han autenticado correctamente en la función podrán proporcionar ese token al servicio Azure Fluid Relay desde su aplicación cliente. Este enfoque de dos pasos le permite usar su propio proceso de autenticación personalizado junto con el servicio Azure Fluid Relay.

## <a name="see-also"></a>Consulte también

- [Introducción a JWT](https://jwt.io/introduction)
- [Cómo crear un JSON Web Token](../how-tos/fluid-json-web-token.md)
- [Notificaciones de carga útil en Azure Fluid Relay](../how-tos/fluid-json-web-token.md#payload-claims)
