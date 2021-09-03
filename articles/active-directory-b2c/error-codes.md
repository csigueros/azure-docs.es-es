---
title: Referencia de código de error
titleSuffix: Azure AD B2C
description: Lista de los códigos de error que puede devolver el servicio Azure Active Directory B2C.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/16/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c24bcf45b2440aada43af62035f0b988128c8f8
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284552"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Códigos de error: Azure Active Directory B2C

El servicio Azure Active Directory B2C puede devolver los errores siguientes.

| Código de error | Message |
| ---------- | ------- |
| `AADB2C90002` | El recurso CORS "{0}" ha devuelto un error 404 no encontrado. |
| `AADB2C90006` | El URI de redireccionamiento "{0}" proporcionado en la solicitud no está registrado para el id. de cliente "{1}". |
| `AADB2C90007` | La aplicación asociada con el id. de cliente "{0}" no tiene ningún URI de redireccionamiento registrado. |
| `AADB2C90008` | La solicitud no contiene ningún parámetro de id. de cliente. |
| `AADB2C90010` | La solicitud no contiene ningún parámetro de ámbito. |
| `AADB2C90011` | El id. de cliente "{0}" proporcionado en la solicitud no coincide con el id. de cliente "{1}" registrado en la directiva. |
| `AADB2C90012` | El ámbito "{0}" proporcionado en la solicitud no es compatible. |
| `AADB2C90013` | El tipo de respuesta "{0}" solicitado proporcionado en la solicitud no es compatible. |
| `AADB2C90014` | El modo de respuesta "{0}" solicitado proporcionado en la solicitud no es compatible. |
| `AADB2C90016` | El tipo de aserción de cliente "{0}" solicitado no coincide con el tipo "{1}" esperado. |
| `AADB2C90017` | La aserción del cliente proporcionada en la solicitud no es válida: {0} |
| `AADB2C90018` | El id. de cliente "{0}" especificado en la solicitud no está registrado en el inquilino "{1}". |
| `AADB2C90019` | En el contenedor de claves con el id. "{0}" del inquilino "{1}" no hay ninguna clave válida. Motivo: {2}. |
| `AADB2C90021` | El perfil técnico "{0}" no existe en la directiva "{1}" del inquilino "{2}". |
| `AADB2C90022` | No se pueden devolver metadatos para la directiva "{0}" en el inquilino "{1}". |
| `AADB2C90023` | El perfil "{0}" no contiene la clave de metadatos "{1}" obligatoria. |
| `AADB2C90025` | El perfil "{0}" de la directiva "{1}" del inquilino "{2}" no contiene la clave criptográfica "{3}" obligatoria. |
| `AADB2C90027` | Las credenciales básicas especificadas para "{0}" no son válidas. Compruebe que las credenciales son correctas y que el recurso le ha concedido acceso. |
| `AADB2C90028` | El certificado de cliente especificado para "{0}" no es válido. Compruebe que el certificado es correcto y contiene una clave privada, y que el recurso le ha concedido acceso. |
| `AADB2C90031` | La directiva "{0}" no especifica ningún recorrido de usuario predeterminado. Asegúrese de que la directiva o sus elementos principales especifican un recorrido del usuario predeterminado como parte de una sección de usuarios de confianza. |
| `AADB2C90035` | El servicio no está disponible temporalmente. Vuelva a intentarlo después de unos minutos. |
| `AADB2C90036` | La solicitud no contiene ningún URI para redirigir al usuario después del cierre de sesión. Especifique un URI en el campo del parámetro post_logout_redirect_uri. |
| `AADB2C90037` | Se produjo un error al procesar la solicitud. Póngase en contacto con el administrador del sitio al que intenta acceder. |
| `AADB2C90039` | La solicitud contiene una aserción de cliente, pero a la directiva "{0}" proporcionada en el inquilino "{1}" le falta un elemento client_secret en RelyingPartyPolicy. |
| `AADB2C90040` | El recorrido del usuario "{0}" no contiene un paso de enviar notificaciones. |
| `AADB2C90043` | El mensaje incluido en la solicitud contiene valores no válidos. Se esperaba "none", "login", "consent" o "select_account". |
| `AADB2C90044` | El solucionador de reclamaciones "{1}" no admite la reclamación "{0}". |
| `AADB2C90046` | Estamos experimentando problemas para cargar el estado actual. Puede intentar iniciar la sesión desde el principio. |
| `AADB2C90047` | El recurso "{0}" contiene errores de script que impiden que se cargue. |
| `AADB2C90048` | Se produjo una excepción no controlada en el servidor. |
| `AADB2C90051` | No se han encontrado proveedores de notificaciones adecuados. |
| `AADB2C90052` | Nombre de usuario o contraseña no válidos. |
| `AADB2C90053` | No se encontró ningún usuario con las credenciales especificadas. |
| `AADB2C90054` | Nombre de usuario o contraseña no válidos. |
| `AADB2C90055` | El ámbito "{0}" proporcionado en la solicitud debe especificar un recurso, como "https://example.com/calendar.read". |
| `AADB2C90057` | La aplicación proporcionada no está configurada para permitir el flujo implícito de OAuth. |
| `AADB2C90058` | La aplicación proporcionada no está configurada para permitir clientes públicos. |
| `AADB2C99059` | La solicitud proporcionada debe presentar un elemento code_challenge. Es necesario para las aplicaciones de página única que usan el flujo de código de autorización.| 
| `AADB2C90067` | El URI de redireccionamiento posterior al cierre de sesión "{0}" tiene un formato no válido. Especifique una dirección URL basada en https como "https://example.com/return" o, bien, para los clientes nativos, utilice el URI de cliente nativo de IETF "urn:ietf:wg:oauth:2.0:oob". |
| `AADB2C90068` | La aplicación proporcionada con el identificador "{0}" no es válida para este servicio. Use una aplicación creada a través del portal de B2C e inténtelo de nuevo. |
| `AADB2C90075` | El intercambio de notificaciones "{0}" especificado en el paso "{1}" ha devuelto una respuesta de error HTTP con código "{2}" y motivo "{3}". |
| `AADB2C90077` | El usuario no tiene una sesión existente y el parámetro de aviso de solicitud tiene un valor de "{0}". |
| `AADB2C90079` | Los clientes deben enviar un client_secret al canjear una concesión confidencial. |
| `AADB2C90080` | La concesión proporcionada ha expirado. Vuelva a realizar la autenticación e inténtelo de nuevo. Hora actual: {0}, hora de emisión de la concesión: {1}, hora de expiración de la ventana deslizante de la concesión: {2}. |
| `AADB2C90081` | El valor de client_secret especificado no coincide con el esperado para este cliente. Corrija el valor de client_secret e inténtelo de nuevo. |
| `AADB2C90083` | A la solicitud le falta el parámetro obligatorio: {0}. |
| `AADB2C90084` | Los clientes públicos no deberían enviar un client_secret al canjear una concesión adquirida públicamente. |
| `AADB2C90085` | El servicio detectó un error interno. Vuelva a realizar la autenticación e inténtelo de nuevo. |
| `AADB2C90086` | El valor de grant_type [{0}] proporcionado no es compatible. |
| `AADB2C90087` | La concesión proporcionada no se ha emitido para esta versión del punto de conexión del protocolo. |
| `AADB2C90088` | La concesión proporcionada no se ha emitido para este punto de conexión. Valor real: {0} y valor esperado: {1} |
| `AADB2C90091` | Cancelación del usuario. |
| `AADB2C90092` | La aplicación proporcionada con el identificador "{0}" está deshabilitada para el inquilino "{1}". Habilite la aplicación e inténtelo de nuevo. |
| `AADB2C90107` | La aplicación con el id. "{0}" no puede obtener un token de id. ya sea porque no se ha proporcionado el ámbito de openid en la solicitud, ya sea porque la aplicación no está autorizada para ello. |
| `AADB2C90108` | El paso de orquestación "{0}" no especifica un objeto CpimIssuerTechnicalProfileReferenceId, cuando se esperaba uno. |
| `AADB2C90110` | El parámetro de ámbito debe incluir "openid" al solicitar un response_type que incluya "id_token". |
| `AADB2C90111` | Su cuenta se ha bloqueado. Contacte con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo. |
| `AADB2C90114` | Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde. |
| `AADB2C90115` | Al solicitar el valor "code" de response_type, el parámetro de ámbito debe incluir un id. de recurso o cliente para los tokens de acceso y "openid" para los tokens de id. Incluya también "offline_access" para los tokens de actualización. |
| `AADB2C90117` | El ámbito "{0}" proporcionado en la solicitud no es compatible. |
| `AADB2C90118` | El usuario ha olvidado su contraseña. |
| `AADB2C90120` | El parámetro de edad máxima "{0}" especificado en la solicitud no es válido. La edad máxima debe ser un entero entre "{1}" y "{2}", ambos incluidos. |
| `AADB2C90122` | La entrada de "{0}" recibida en la solicitud no ha superado la validación de la solicitud HTTP. Asegúrese de que la entrada no contiene caracteres como < o &. |
| `AADB2C90128` | La cuenta asociada a esta concesión ya no existe. Vuelva a realizar la autenticación e inténtelo de nuevo. |
| `AADB2C90129` | La concesión proporcionada se ha revocado. Vuelva a realizar la autenticación e inténtelo de nuevo. |
| `AADB2C90145` | No se encontró ningún número de teléfono sin comprobar y la directiva no permite ningún número escrito por usuarios. |
| `AADB2C90146` | El ámbito "{0}" proporcionado en la solicitud especifica más de un recurso para un token de acceso, cosa que no se admite. |
| `AADB2C90149` | No se pudo cargar el script "{0}". |
| `AADB2C90151` | El usuario ha superado el número máximo de reintentos de autenticación multifactor. |
| `AADB2C90152` | Una solicitud de sondeo multifactor no pudo obtener una respuesta del servicio. |
| `AADB2C90154` | Una solicitud de verificación multifactor no pudo obtener un id. de sesión del servicio. |
| `AADB2C90155` | Error en una solicitud de verificación multifactor. Motivo: "{0}". |
| `AADB2C90156` | Error en una solicitud de validación multifactor. Motivo: "{0}". |
| `AADB2C90157` | El usuario ha superado el número máximo de reintentos de un paso autoafirmado. |
| `AADB2C90158` | Error en una solicitud de validación autoafirmada. Motivo: "{0}". |
| `AADB2C90159` | Error en una solicitud de verificación autoafirmada. Motivo: "{0}". |
| `AADB2C90161` | Error en una respuesta de envío autoafirmada. Motivo: "{0}". |
| `AADB2C90165` | El mensaje de inicio de SAML con el id. "{0}" no se puede encontrar en el estado. |
| `AADB2C90168` | La solicitud de redireccionamiento HTTP no contiene el parámetro obligatorio "{0}" para una solicitud firmada. |
| `AADB2C90178` | El certificado de firma "{0}" no tiene ninguna clave privada. |
| `AADB2C90182` | El valor de code_verifier proporcionado no coincide con el valor de code_challenge asociado. |
| `AADB2C90183` | El valor de code_verifier proporcionado no es válido. |
| `AADB2C90184` | El valor de code_challenge_method proporcionado no se admite. Los valores admitidos son: sin formato o S256. |
| `AADB2C90188` | El perfil técnico de SAML "{0}" especifica una URL de PartnerEntity de "{1}", pero se produjo un error en la obtención de metadatos debido al motivo siguiente: "{2}". |
| `AADB2C90194` | La notificación "{0}" especificada para el token de portador no se encuentra en las notificaciones disponibles. Notificaciones disponibles "{1}". |
| `AADB2C90205` | Esta aplicación no tiene suficientes permisos para este recurso web como para poder realizar la operación. |
| `AADB2C90206` | Se agotó el tiempo de espera al inicializar el cliente. |
| `AADB2C90208` | El parámetro id_token_hint proporcionado ha expirado. Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90209` | El parámetro id_token_hint proporcionado no contiene ninguna audiencia aceptada. Valores de audiencia válidos: "{0}". Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90210` | El parámetro id_token_hint proporcionado no se pudo validar. Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90211` | La solicitud incluía una cookie de estado incompleta. |
| `AADB2C90212` | La solicitud incluía una cookie de estado no válida. |
| `AADB2C90220` | El contenedor de claves del inquilino "{0}" con el identificador de almacenamiento "{1}" existe pero no contiene un certificado válido. Es posible que el certificado haya expirado o que se active en el futuro (nbf). |
| `AADB2C90223` | Error al inmunizar el recurso CORS. |
| `AADB2C90224` | El flujo del propietario del recurso no está habilitado para la aplicación. |
| `AADB2C90225` | El nombre de usuario o la contraseña que proporcionó en la solicitud no son válidos. |
| `AADB2C90226` | El intercambio de token especificado solo se admite por HTTP POST. |
| `AADB2C90232` | El parámetro id_token_hint proporcionado no contiene ningún emisor aceptado. Emisores válidos: "{0}". Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90233` | El parámetro id_token_hint proporcionado no pudo validar la firma. Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90235` | El parámetro id_token proporcionado ha expirado. Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90237` | El parámetro id_token proporcionado no contiene ninguna audiencia válida. Valores de audiencia válidos: "{0}". Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90238` | El parámetro id_token proporcionado no contiene ningún emisor válido. Valores de emisor válidos: "{0}". Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90239` | El parámetro id_token proporcionado no pudo validar la firma. Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90240` | El parámetro id_token proporcionado tiene un formato incorrecto y no se pudo analizar. Proporcione otro token e inténtelo de nuevo. |
| `AADB2C90242` | El perfil técnico de SAML "{0}" especifica el elemento de CDATA PartnerEntity, el cual no puede cargarse debido al siguiente motivo: "{1}". |
| `AADB2C90243` | La clave o el secreto del cliente de IDP no está configurado correctamente. |
| `AADB2C90244` | Hay demasiadas solicitudes en este momento. Espere un poco y vuelva a intentarlo. |
| `AADB2C90248` | Solo las aplicaciones creadas a través del portal de administración de B2C pueden usar el flujo del propietario del recurso. |
| `AADB2C90250` | No se admite el uso de un punto de conexión de inicio de sesión genérico. |
| `AADB2C90255` | El intercambio de notificaciones especificado en el perfil técnico "{0}" no se completó como se esperaba. Puede intentar iniciar la sesión desde el principio. |
| `AADB2C90261` | El intercambio de notificaciones "{0}" especificado en el paso "{1}" ha devuelto una respuesta de error HTTP que no se ha podido analizar. |
| `AADB2C90272` | El parámetro id_token_hint no se especificó en la solicitud. Proporcione el token y vuelva a intentarlo. |
| `AADB2C90273` | Se recibió una respuesta no válida: "{0}". |
| `AADB2C90274` | Los metadatos del proveedor no especifican ningún servicio de cierre de sesión único o el enlace del punto de conexión no es "urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" ni "urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST". |
| `AADB2C90276` | La solicitud no es coherente con la opción de configuración del control "{0}": "{1}" en el elemento technicalProfile "{2}" para la directiva "{3}" y el inquilino "{4}". |
| `AADB2C90277` | El paso de orquestación "{0}" del recorrido del usuario "{1}" de la directiva "{2}" no contiene ninguna referencia de definición de contenido. |
| `AADB2C90279` | El id. de cliente proporcionado "{0}" no coincide con el id. de cliente con el que se ha emitido la concesión. |
| `AADB2C90284` | La aplicación con identificador "{0}" no tiene consentimiento y no puede usarse para las cuentas locales. |
| `AADB2C90285` | La aplicación con el identificador "{0}" no se ha encontrado. |
| `AADB2C90288` | El valor UserJourney con id. "{0}" al que se hace referencia en TechnicalProfile "{1}" para el canje del token de actualización en el inquilino "{2}" no existe en la directiva "{3}" ni en ninguna de sus directivas de base. |
| `AADB2C90287` | La solicitud contiene un URI de redireccionamiento "{0}" que no es válido.|
| `AADB2C90289` | Se ha producido un error en la conexión al proveedor de identidades. Inténtelo de nuevo más tarde. |
| `AADB2C90296` | La aplicación no se ha configurado correctamente. Póngase en contacto con el administrador del sitio al que intenta acceder. |
| `AADB2C99005` | La solicitud contiene un parámetro de ámbito no válido que incluye un carácter "{0}" que no es válido. |
| `AADB2C99006` | Azure AD B2C no encuentra la aplicación de extensiones con id. de aplicación "{0}". Para obtener más información, visite https://go.microsoft.com/fwlink/?linkid=851224. |
| `AADB2C99011` | El valor de metadatos "{0}" no se ha especificado en el elemento TechnicalProfile "{1}" en la directiva "{2}". |
| `AADB2C99013` | La combinación de los valores grant_type [{0}] y token_type [{1}] proporcionados no se admite. |
| `AADB2C99015` | El perfil "{0}" de la directiva "{1}" del inquilino "{2}" carece de todos los valores InputClaim necesarios para el flujo de credenciales de contraseña del propietario del recurso. |
