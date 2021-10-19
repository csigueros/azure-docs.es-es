---
title: Autenticación y autorización de las API de Azure Healthcare
description: En este artículo se proporciona información general sobre la autenticación y autorización de las API de atención sanitaria.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 53f31790d5b92234bfd35e723e377e682cd13a4d
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252519"
---
# <a name="authentication--authorization-for-the-healthcare-apis-preview"></a>Autenticación & autorización para las API de atención sanitaria (versión preliminar)

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo se proporciona información general sobre el proceso de autenticación y autorización para acceder a los servicios de las API de atención sanitaria.

## <a name="authentication"></a>Authentication

Las API de atención sanitaria son una colección de servicios administrados protegidos mediante [Azure Active Directory (Azure AD),](../active-directory/index.yml)un proveedor de identidades global que admite [OAuth 2.0.](https://oauth.net/2/)

Para que los servicios de las API de atención sanitaria accedan a recursos de Azure, como cuentas de almacenamiento y centros de eventos, debe habilitar la identidad administrada del sistema y conceder los permisos **adecuados** a la identidad administrada. Para más información, consulte [Identidades administradas de Azure.](../active-directory/managed-identities-azure-resources/overview.md)

Las API de atención sanitaria no admiten otros proveedores de identidades. Sin embargo, los clientes pueden usar su propio proveedor de identidades para proteger las aplicaciones y permitirles interactuar con las API de atención sanitaria mediante la administración de aplicaciones cliente y controles de acceso a datos de usuario.

Las aplicaciones cliente se registran en el Azure AD y se pueden usar para acceder a las API de atención sanitaria. Los controles de acceso a datos de usuario se realizan en las aplicaciones o servicios que implementan lógica de negocios.

### <a name="application-roles"></a>Roles de aplicación

Los usuarios autenticados y las aplicaciones cliente de las API de atención sanitaria deben tener los roles de aplicación adecuados.

El servicio FHIR de las API de atención sanitaria proporciona los siguientes roles:

* **Lector de datos de FHIR:** puede leer (y buscar) datos de FHIR.
* **FHIR Data Writer:** puede leer, escribir y eliminar datos de FHIR de forma flexible.
* **Exportador de datos de FHIR:** puede leer y exportar ($export operador).
* **Colaborador de datos de FHIR:** puede realizar todas las operaciones del plano de datos.
* **Convertidor de datos de FHIR:** puede usar el convertidor para realizar la conversión de datos.

El servicio DICOM de las API de atención sanitaria proporciona los siguientes roles:

* **Propietario de datos DICOM:** puede leer, escribir y eliminar datos DICOM.
* **Lectura de datos DICOM:** puede leer datos DICOM.

El IoT Connector no requiere roles de aplicación, pero se basa en el "receptor de datos de Azure Event Hubs" para recuperar los datos almacenados en el centro de eventos de la suscripción del cliente.

## <a name="authorization"></a>Authorization

Una vez concedidos los roles de aplicación adecuados, los usuarios autenticados y las aplicaciones cliente pueden acceder a los servicios de las API de atención sanitaria mediante la obtención de un **token** de acceso válido emitido por Azure AD y realizar operaciones específicas definidas por los roles de aplicación.
 
* Para el servicio FHIR, el token de acceso es específico del servicio o recurso.
* Para el servicio DICOM, el token de acceso se concede al `dicom.healthcareapis.azure.com` recurso, no a un servicio específico.
* Para la IoT Connector, el token de acceso no es necesario porque no se expone a los usuarios o aplicaciones cliente.

### <a name="steps-for-authorization"></a>Pasos para la autorización

Hay dos maneras comunes de obtener un token de acceso, que se describen en detalle en la documentación de [Azure AD:](../active-directory/develop/v2-oauth2-auth-code-flow.md) flujo de código de autorización y flujo de [credenciales de cliente](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

Para obtener un token de acceso para las API de atención sanitaria, estos son los pasos que usan el flujo **de código de autorización:**

1. **El cliente envía una solicitud al punto de conexión Azure AD autorización.** Azure AD redirige al cliente a una página de inicio de sesión en la que el usuario se autentica con las credenciales adecuadas (por ejemplo: nombre de usuario y contraseña, o una autenticación en dos fases). **Tras la autenticación correcta, se devuelve un código de autorización al cliente.** Azure AD solo permite que este código de autorización se devuelva a una dirección URL de respuesta registrada configurada en el registro de la aplicación cliente.

2. **La aplicación cliente intercambia el código de autorización para un token de acceso en el punto de conexión Azure AD token.** Al solicitar un token, es posible que la aplicación cliente tenga que proporcionar un secreto de cliente (que puede agregar durante el registro de la aplicación).
 
3. **El cliente realiza una solicitud a las API** de atención sanitaria, por ejemplo, una solicitud para buscar todos los pacientes en el servicio `GET` FHIR. Al realizar la solicitud, incluye **el token de acceso en un encabezado de `HTTP` solicitud**, por ejemplo, **`Authorization: Bearer xxx`** .

4. **El servicio healthcare APIs valida que el token contiene las notificaciones adecuadas (propiedades en el token).** Si es válido, completa la solicitud y devuelve datos al cliente.

En el **flujo de credenciales de cliente**, los permisos se conceden directamente a la propia aplicación. Cuando la aplicación presenta un token a un recurso, el recurso exige que la propia aplicación tenga autorización para realizar una acción, ya que no hay ningún usuario implicado en la autenticación. Por lo tanto, es diferente del flujo **de código de autorización** de las maneras siguientes:

- El usuario o el cliente no necesitan iniciar sesión de forma interactiva
- El código de autorización no es necesario.
- El token de acceso se obtiene directamente a través de los permisos de la aplicación.

### <a name="access-token"></a>Access token

El token de acceso es una colección firmada y codificada en [Base64](https://en.wikipedia.org/wiki/Base64) de propiedades (notificaciones) que transmiten información sobre la identidad, los roles y los privilegios del cliente concedidos al usuario o cliente.

Las API de atención sanitaria normalmente esperan [un JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token). Consta de tres partes: 
* Encabezado
* Carga (las notificaciones)
* Firma, como se muestra en la imagen siguiente. Para más información, consulte [Tokens de acceso de Azure.](../active-directory/develop/active-directory-configurable-token-lifetimes.md)

[![Firma del token web de JASON. ](media/azure-access-token.png) ](media/azure-access-token.png#lightbox)

Puede usar herramientas en línea como para [https://jwt.ms](https://jwt.ms/) ver el contenido del token. Por ejemplo, puede ver los detalles de las notificaciones.

|**Tipo de notificación**          |**Valor**               |**Notas**                               |
|------------------------|------------------------|----------------------------------------|
|aud                     |https://xxx.fhir.azurehealthcareapis.com|Identifica al destinatario previsto del token. En los `id_tokens`, la audiencia es el identificador de aplicación de la aplicación, asignado a la aplicación en Azure Portal. La aplicación debe validar este valor y rechazar el token si el valor no coincide.|
|iss                     |https://sts.windows.net/{tenantid}/|Identifica el servicio de token de seguridad (STS) que construye y devuelve el token, así como el inquilino de Azure AD en el que se autenticó al usuario. Si el token fue emitido por el punto de conexión de la versión 2.0, el identificador URI finalizará con `/v2.0`. El GUID que indica que el usuario es un usuario consumidor desde una cuenta de Microsoft es `9188040d-6c67-4c5b-b112-36a304b66dad`. La aplicación debe usar la parte GUID de la notificación para restringir el conjunto de inquilinos que pueden iniciar sesión en la aplicación, si es aplicable.|
|iat                     |(marca de tiempo)            |La notificación "iat" (emitido a las) indica cuándo se produjo la autenticación de este token.|
|nbf                     |(marca de tiempo)            |La notificación "nbf" (no antes de) identifica la hora antes de la cual no debe ser aceptado el token JWT para su procesamiento.|
|exp                     |(marca de tiempo)            |La notificación "exp" (fecha de expiración) identifica la hora de expiración en la que o después de la que el token JWT no debe ser aceptado para su procesamiento. Es importante tener en cuenta que un recurso también puede rechazar el token antes de esta hora, si, por ejemplo, se requiere un cambio en la autenticación o se ha detectado una revocación de tokens.|
|aio                     |E2ZgYxxx                |Una notificación interna usada por Azure AD para registrar los datos para la reutilización de tokens. Se debe omitir.|
|appid                   |e97e1b8c-xxx            |Este es el identificador de aplicación del cliente que usa el token. La aplicación puede actuar por sí misma o en nombre de un usuario. Normalmente, el id. de aplicación representa un objeto de aplicación, pero también puede representar un objeto de entidad de servicio en Azure AD.|
|appidacr                |1                       |Indica cómo se autenticó el cliente. Para un cliente público, el valor es 0. Si se usan el identificador y el secreto de cliente, el valor es 1. Si se usa un certificado de cliente para la autenticación, el valor es 2.|
|idp                     |https://sts.windows.net/{tenantid}/|Registra el proveedor de identidades que autenticó al firmante del token. Este valor es idéntico al valor de la notificación issuer, a menos que la cuenta de usuario no esté en el mismo inquilino que el emisor: invitados, por ejemplo. Si la notificación no está presente, significa que el valor de iss se puede usar en su lugar. Para las cuentas personales que se usan en un contexto organizativo (por ejemplo, una cuenta personal invitada a un inquilino de Azure AD), la notificación de idp puede ser "live.com" o un URI de STS que contenga el inquilino de la cuenta Microsoft 9188040d-6c67-4c5b-b112-36a304b66dad.|
|oid                     |Por ejemplo, tenantid         |Se trata del identificador inmutable de un objeto en el sistema de identidad de Microsoft, en este caso, una cuenta de usuario. Este identificador identifica de forma única al usuario entre aplicaciones: dos aplicaciones diferentes que inician sesión en el mismo usuario recibirán el mismo valor en la notificación oid. Microsoft Graph devolverá este identificador como la propiedad ID de una cuenta de usuario determinada. Dado que el oid permite que varias aplicaciones correlacionan a los usuarios, el ámbito del perfil es necesario para recibir esta notificación. Nota: Si existe un único usuario en varios inquilinos, el usuario contendrá un identificador de objeto diferente en cada inquilino; se consideran cuentas diferentes, aunque el usuario inicie sesión en cada cuenta con las mismas credenciales.|
|Rh                       |0.ARoxxx              |Una notificación interna que Azure usa para volver a validar los tokens. Debe omitirse.|
|sub                      |Por ejemplo, tenantid        |La entidad de seguridad sobre la que el token declara información como, por ejemplo, el usuario de una aplicación. Este valor es inmutable y no se puede reasignar ni volver a usar. El firmante es un identificador en pares: es único para un identificador de aplicación determinado. Por lo tanto, si un usuario inicia sesión en dos aplicaciones diferentes con dos identificadores de cliente diferente, esas aplicaciones recibirán dos valores diferentes para la notificación de asunto. Esto puede ser o no deseable dependiendo de los requisitos de arquitectura y privacidad.|
|tid                      |Por ejemplo, tenantid        |Un GUID que representa el inquilino de Azure AD de donde proviene el usuario. En el caso de las cuentas profesionales y educativas, el GUID es el identificador del inquilino inmutable de la organización a la que pertenece el usuario. Para las cuentas personales, el valor es 9188040d-6c67-4c5b-b112-36a304b66dad. El ámbito del perfil es necesario para recibir esta notificación.
|uti                      |bY5glsxxx             |Una notificación interna que Azure usa para volver a validar los tokens. Debe omitirse.|
|ver                      |1                     |Indica la versión del token.|
 
**El token de acceso es válido durante una hora de forma predeterminada. Puede obtener un nuevo token o renovarlo mediante el token de actualización antes de que expire.**

Para obtener un token de acceso, puede usar herramientas como Postman, la extensión de cliente rest en Visual Studio Code, PowerShell, cli, curl y las [bibliotecas](../active-directory/develop/reference-v2-libraries.md)de autenticación de Azure AD .

## <a name="encryption"></a>Cifrado

Al crear un nuevo servicio de las API de Azure Healthcare, los datos se cifran mediante **claves administradas por Microsoft** de forma predeterminada. 

* El servicio FHIR proporciona cifrado de datos en reposo cuando los datos se conservan en el almacén de datos.
* El servicio DICOM proporciona cifrado de datos en reposo cuando los datos de creación de imágenes, incluidos los metadatos incrustados, se conservan en el almacén de datos. Cuando los metadatos se extraen y se conservan en el servicio FHIR, se cifran automáticamente.
* IoT Connector, después de la asignación y normalización de datos, conserva los mensajes del dispositivo en el servicio FHIR, que se cifra automáticamente. En los casos en los que se envían mensajes de dispositivo a Azure Event Hubs, que usa Azure Storage para almacenar los datos, los datos se cifran automáticamente con Azure Storage Service Encryption (Azure SSE).

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido la autenticación y autorización de las API de atención sanitaria. Para obtener información sobre cómo implementar una instancia del servicio Healthcare API, consulte

>[!div class="nextstepaction"]
>[Implementación del área de trabajo de Healthcare API (versión preliminar) mediante Azue Portal](healthcare-apis-quickstart.md)
