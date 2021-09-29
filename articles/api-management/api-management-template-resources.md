---
title: Recursos de plantilla de Azure API Management | Microsoft Docs
description: Descubra los tipos de los recursos disponibles para su uso en las plantillas del portal para desarrolladores en Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: danlep
ms.openlocfilehash: 608299143de1e480548efaf8a62966c4de96736e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654430"
---
# <a name="azure-api-management-template-resources"></a>Recursos de plantilla de Azure API Management
Azure API Management proporciona los siguientes tipos de recursos para su uso en las plantillas del portal para desarrolladores.  
  
-   [Recursos de cadena](#strings)  
  
-   [Recursos de glifo](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a> Recursos de cadena  
 API Management proporciona un conjunto completo de recursos de cadena para su uso en el portal para desarrolladores. Estos recursos están localizados en todos los idiomas admitidos por API Management. El conjunto predeterminado de plantillas utiliza estos recursos para encabezados de páginas etiquetas y cualquier cadena constante que se muestren en el portal para desarrolladores. Para usar un recurso de cadena en sus plantillas, especifique el prefijo de la cadena del recurso seguido por el nombre de la cadena, como se muestra en el siguiente ejemplo.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 El siguiente ejemplo está tomado de la plantilla Lista de productos y muestra **Productos** en la parte superior de la página.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Se admiten las siguientes opciones de localización:

| Configuración regional    | Idioma               |
|-----------|------------------------|
| "en"      | "English"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "it"      | "Italiano"             |
| "ja-JP"   | "日本語"                |
| "ko"      | "한국어"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brasil)"   |
| "pt-pt"   | "Português (Portugal)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "中文(简体)"           |
| "zh-hant" | "中文(繁體)"           |

 Consulte las siguientes tablas a fin de obtener los recursos de cadena disponibles para su uso en sus plantillas del portal para desarrolladores. Use el nombre de la tabla como el prefijo para los recursos de cadena de esa tabla.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Documentación](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a> ApisStrings  
  
|Nombre|Texto|  
|----------|----------|  
|PageTitleApis|API existentes|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Nombre|Texto|  
|----------|----------|  
|WebApplicationsDetailsTitle|Versión preliminar de la aplicación|  
|WebApplicationsRequirementsHeader|Requisitos|  
|WebApplicationsScreenshotAlt|Instantánea|  
|WebApplicationsScreenshotsHeader|Capturas de pantalla|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Nombre|Texto|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|¿Está seguro de que desea quitar la aplicación?|  
|WebDevelopersAppNotPublished|No publicado|  
|WebDevelopersAppNotSubmitted|No enviado|  
|WebDevelopersAppTableCategoryHeader|Category|  
|WebDevelopersAppTableNameHeader|Nombre|  
|WebDevelopersAppTableStateHeader|State|  
|WebDevelopersEditLink|Editar|  
|WebDevelopersRegisterAppLink|Registre la aplicación|  
|WebDevelopersRemoveLink|Quitar|  
|WebDevelopersSubmitLink|Enviar|  
|WebDevelopersYourApplicationsHeader|Sus aplicaciones|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a> AppStrings  
  
|Nombre|Texto|  
|----------|----------|  
|WebApplicationsHeader|Aplicaciones|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a> CommonResources  
  
|Nombre|Texto|  
|----------|----------|  
|NoItemsToDisplay|No se encontró ningún resultado.|  
|GeneralExceptionMessage|Se ha producido algún problema. Podría ser un problema temporal o un error. Vuelva a intentarlo.|  
|GeneralJsonExceptionMessage|Se ha producido algún problema. Podría ser un problema temporal o un error. Vuelva a cargar la página y pruebe otra vez.|  
|ConfirmationMessageUnsavedChanges|Hay algunos cambios no guardados. ¿Está seguro de que desea cancelar y descartar los cambios?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|El cuerpo de la solicitud HTTP es demasiado extenso.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a> CommonStrings  
  
|Nombre|Texto|  
|----------|----------|  
|ButtonLabelCancel|Cancelar|  
|ButtonLabelSave|Guardar|  
|GeneralExceptionMessage|Se ha producido algún problema. Podría ser un problema temporal o un error. Vuelva a intentarlo.|  
|NoItemsToDisplay|No hay ningún elemento para mostrar.|  
|PagerButtonLabelFirst|First|  
|PagerButtonLabelLast|Último|  
|PagerButtonLabelNext|Siguiente|  
|PagerButtonLabelPrevious|Anterior|  
|PagerLabelPageNOfM|Página {0} de {1}|  
|PasswordTooShort|La contraseña es demasiado extensa.|  
|EmailAsPassword|No use su correo electrónico como la contraseña.|  
|PasswordSameAsUserName|La contraseña no puede contener su nombre de usuario.|  
|PasswordTwoCharacterClasses|Utilice diferentes clases de caracteres.|  
|PasswordTooManyRepetitions|Hay demasiados caracteres repetidos.|  
|PasswordSequenceFound|La contraseña contiene secuencias.|  
|PagerLabelPageSize|Tamaño de página|  
|CurtainLabelLoading|Cargando...|  
|TablePlaceholderNothingToDisplay|No hay ningún dato para el intervalo de tiempo y el ámbito seleccionados.|  
|ButtonLabelClose|Cerrar|  
  
###  <a name="documentation"></a><a name="Documentation"></a> Documentation  
  
|Nombre|Texto|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Encabezado "{0}" no válido|  
|WebDocumentationInvalidRequestErrorMessage|Dirección URL de solicitud no válida|  
|TextboxLabelAccessToken|Token de acceso*|  
|DropdownOptionPrimaryKeyFormat|Primary-{0}|  
|DropdownOptionSecondaryKeyFormat|Secondary-{0}|  
|WebDocumentationSubscriptionKeyText|Su clave de suscripción|  
|WebDocumentationTemplatesAddHeaders|Agregue los encabezados HTTP necesarios.|  
|WebDocumentationTemplatesBasicAuthSample|Ejemplo de autorización básica|  
|WebDocumentationTemplatesCurlForBasicAuth|Para la autorización básica, use lo siguiente: --user {nombre de usuario}: {contraseña}|  
|WebDocumentationTemplatesCurlValuesForPath|Especifique valores para los parámetros de ruta de acceso (que se muestran como {...}), la clave de la suscripción y valores para los parámetros de consulta.|  
|WebDocumentationTemplatesDeveloperKey|Especifique la clave de suscripción.|  
|WebDocumentationTemplatesJavaApache|Este ejemplo utiliza el cliente HTTP Apache desde HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Especifique valores para los parámetros opcionales, según se requiera.|  
|WebDocumentationTemplatesPhpPackage|Esta muestra utiliza el paquete HTTP_Request2. (para obtener más información: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Especifique valores para los parámetros de ruta de acceso (que se muestran como {...}) y el cuerpo de la solicitud, si así se requiere.|  
|WebDocumentationTemplatesRequestBody|Especifique el cuerpo de la solicitud.|  
|WebDocumentationTemplatesRequiredParams|Especifique valores para los siguientes parámetros requeridos.|  
|WebDocumentationTemplatesValuesForPath|Especifique valores para los parámetros de ruta de acceso (que se muestran como {...}).|  
|OAuth2AuthorizationEndpointDescription|El punto de conexión de autorización se utiliza para interactuar con el propietario del recurso y obtener la concesión de una autorización.|  
|OAuth2AuthorizationEndpointName|Punto de conexión de autorización|  
|OAuth2TokenEndpointDescription|El cliente utiliza el punto de conexión de token para obtener un token de acceso presentando su concesión de autorización o token de actualización.|  
|OAuth2TokenEndpointName|Punto de conexión de token|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p\>El cliente inicia el flujo dirigiendo el agente de usuario del propietario del recurso al punto de conexión de autorización.  El cliente incluye su identificador de cliente, el ámbito solicitado, el estado local y un URI de redirección al que el servidor de autorización enviará al agente de usuario una vez concedido el acceso (o denegado).     </p\> <p\>El servidor de autorización autentica al propietario del recurso (por medio del agente de usuario) y establece si aquel acepta o rechaza la solicitud de acceso del cliente.     </p\> <p\>Suponiendo que el propietario del recurso conceda el acceso, el servidor de autorización redirige el agente de usuario al cliente con el URI de redirección especificado antes (en la solicitud o durante el registro del cliente).  El URI de redirección incluye un código de autorización y cualquier estado que haya proporcionado el cliente con anterioridad.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p\>Si el usuario rechaza la solicitud de acceso o si esta no es válida, se informará al cliente con los siguientes parámetros, que se agregan a la redirección: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Solicitud de autorización|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p\>La aplicación cliente debe enviar al usuario al punto de conexión de autorización con el fin de iniciar el proceso de OAuth.          En el punto de conexión de autorización, el usuario se autentica y, después, se concede o deniega el acceso a la aplicación.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p\>Suponiendo que el propietario del recurso conceda el acceso, el servidor de autorización redirige el agente de usuario al cliente con el URI de redirección especificado antes (en la solicitud o durante el registro del cliente).  El URI de redirección incluye un código de autorización y cualquier estado que haya proporcionado el cliente con anterioridad. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p\>El cliente solicita un token de acceso del punto de conexión de token del servidor de autorización incluyendo el código de autorización recibido en el paso anterior.  Al realizar la solicitud, el cliente se autentica con el servidor de autorización.  El cliente incluye el URI de redirección utilizado con el objetivo de obtener el código de autorización para la comprobación. </p\> <p\>El servidor de autorización autentica el cliente, valida el código de autorización y garantiza que el URI de redirección recibido coincida con el URI empleado para redirigir el cliente en el paso C.  Si es válido, el servidor de autorización responde con un token de acceso y, de forma opcional, otro de actualización. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p\>Si la solicitud no es válida o no ha superado la autenticación del cliente, el servidor de autorización responde con un código de estado HTTP 400 (solicitud incorrecta) —a menos que se especifique lo contrario— e incluye en la respuesta los siguientes parámetros. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p\>El cliente realiza una solicitud al punto de conexión de token enviando los siguientes parámetros en el formato "application/x-www-form-urlencoded" con una codificación de caracteres de UTF-8 en el cuerpo de la entidad de la solicitud HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p\>El servidor de autorización emite un token de acceso y otro de actualización opcional. Además, construye la respuesta agregando los siguientes parámetros al cuerpo de entidad de la respuesta HTTP con un código de estado 200 (correcto). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p\>El cliente se autentica con el servidor de autorización y solicita un token de acceso del punto de conexión de token. </p\> <p\>El servidor de autorización autentica al cliente y, si es válido, emite un token de acceso. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>Si la solicitud no es válida o no ha superado la autenticación del cliente, el servidor de autorización responde con un código de estado HTTP 400 (solicitud incorrecta) —a menos que se especifique lo contrario— e incluye en la respuesta los siguientes parámetros. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p\>El cliente realiza una solicitud al punto de conexión de token agregando los siguientes parámetros en el formato "application/x-www-form-urlencoded" con una codificación de caracteres de UTF-8 en el cuerpo de la entidad de la solicitud HTTP. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\>Si la solicitud de acceso es válida y se autoriza, el servidor de autorización emite un token de acceso y otro de actualización opcional. Además, construye la respuesta agregando los siguientes parámetros al cuerpo de entidad de la respuesta HTTP con un código de estado 200 (correcto). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p\>El cliente inicia el flujo dirigiendo el agente de usuario del propietario del recurso al punto de conexión de autorización.  El cliente incluye su identificador de cliente, el ámbito solicitado, el estado local y un URI de redirección al que el servidor de autorización enviará al agente de usuario una vez concedido el acceso (o denegado). </p\> <p\>El servidor de autorización autentica al propietario del recurso (por medio del agente de usuario) y establece si aquel acepta o rechaza la solicitud de acceso del cliente. </p\> <p\>Suponiendo que el propietario del recurso conceda el acceso, el servidor de autorización redirige el agente de usuario al cliente con el URI de redirección especificado antes.  El URI de redirección incluye el token de acceso en el fragmento del URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p\>Si el propietario del recurso rechaza la solicitud de acceso o esta produce algún error por un motivo distinto a un URI de redirección ausente o no válido, el servidor de autorización informa al cliente agregando los siguientes parámetros al componente de fragmento del URI de redirección con el formato "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p\>La aplicación cliente debe enviar al usuario al punto de conexión de autorización con el fin de iniciar el proceso de OAuth.      En el punto de conexión de autorización, el usuario se autentica y, después, se concede o deniega el acceso a la aplicación. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p\>Si el propietario del recurso acepta la solicitud de acceso, el servidor de autenticación emite un token de acceso y lo envía al cliente agregando los siguientes parámetros en el componente de fragmento del URI de redirección con el formato "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|El flujo del código de autorización está optimizado para clientes con la capacidad de mantener la confidencialidad de sus credenciales (p. ej., aplicaciones de servidor web implementadas mediante PHP, Java, Python, Ruby, ASP.NET, etc.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Concesión del código de autorización|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|El flujo de credenciales del cliente es adecuado para casos en los que este último (es decir, su aplicación) solicita acceso a los recursos protegidos bajo su control. El cliente se considera un propietario de recursos, por lo que no se requiere ninguna interacción por parte del usuario final.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Concesión de las credenciales de cliente|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|El flujo implícito está optimizado para clientes sin capacidad de mantener la confidencialidad de sus credenciales y de los que se tiene constancia que emplean un URI de redirección concreto. Estos clientes se suelen implementar en un explorador mediante un lenguaje de scripting como JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Concesión implícita|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|El flujo de credenciales de contraseña del propietario del recurso es adecuado en casos en este último mantiene una relación de confianza con el cliente (es decir, su aplicación), como el sistema operativo del dispositivo o una aplicación con privilegios elevados. Este flujo resulta adecuado para clientes con capacidad de obtener las credenciales del propietario del recurso (nombre de usuario y contraseña; normalmente mediante un formulario interactivo).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Concesión de credenciales de contraseña de propietario del recurso|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p\>El propietario del recurso proporciona al cliente su nombre de usuario y contraseña. </p\> <p\>El cliente solicita un token de acceso del punto de conexión de token del servidor de autorización; para ello, incluye las credenciales recibidas del propietario del recurso.  Al realizar la solicitud, el cliente se autentica con el servidor de autorización. </p\> <p\>El servidor de autorización autentica el cliente y valida las credenciales del propietario del recurso y, en caso de determinar su validez, emite un token de acceso. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>Si la solicitud no es válida o no ha superado la autenticación del cliente, el servidor de autorización responde con un código de estado HTTP 400 (solicitud incorrecta) —a menos que se especifique lo contrario— e incluye en la respuesta los siguientes parámetros. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p\>El cliente realiza una solicitud al punto de conexión de token agregando los siguientes parámetros en el formato "application/x-www-form-urlencoded" con una codificación de caracteres de UTF-8 en el cuerpo de la entidad de la solicitud HTTP. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\>Si la solicitud de acceso es válida y se autoriza, el servidor de autorización emite un token de acceso y otro de actualización opcional. Además, construye la respuesta agregando los siguientes parámetros al cuerpo de entidad de la respuesta HTTP con un código de estado 200 (correcto). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Solicitud de token de acceso|  
|OAuth2Step_AuthorizationRequest_Name|Solicitud de autorización|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|REQUERIDO. El token de acceso emitido por el servidor de autorización.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|REQUERIDO. El token de acceso emitido por el servidor de autorización.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|REQUERIDO. El token de acceso emitido por el servidor de autorización.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|REQUERIDO. El token de acceso emitido por el servidor de autorización.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|REQUERIDO. Identificador del cliente.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|REQUERIDO si el cliente no se autentica con el servidor de autorización.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|REQUERIDO. El identificador del cliente.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|REQUERIDO. El código de autorización que genera el servidor de autorización.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|REQUERIDO. El código de autorización recibido del servidor de autorización.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. Texto ASCII en lenguaje natural que proporciona información adicional.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. Texto ASCII en lenguaje natural que proporciona información adicional.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. Texto ASCII en lenguaje natural que proporciona información adicional.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. Texto ASCII en lenguaje natural que proporciona información adicional.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. Texto ASCII en lenguaje natural que proporciona información adicional.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. Un URI que identifica una página web en lenguaje natural con información sobre el error.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. Un URI que identifica una página web en lenguaje natural con información sobre el error.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. Un URI que identifica una página web en lenguaje natural con información sobre el error.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. Un URI que identifica una página web en lenguaje natural con información sobre el error.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. Un URI que identifica una página web en lenguaje natural con información sobre el error.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|REQUERIDO. Un solo código de error ASCII entre las opciones siguientes: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error y temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|REQUERIDO. Un solo código de error ASCII entre las opciones siguientes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type e invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|REQUERIDO. Un solo código de error ASCII entre las opciones siguientes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type e invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|REQUERIDO. Un solo código de error ASCII entre las opciones siguientes: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error y temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|REQUERIDO. Un solo código de error ASCII entre las opciones siguientes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type e invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|RECOMENDADO. La vigencia en segundos del token de acceso.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|RECOMENDADO. La vigencia en segundos del token de acceso.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|RECOMENDADO. La vigencia en segundos del token de acceso.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|RECOMENDADO. La vigencia en segundos del token de acceso.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|REQUERIDO. El valor debe establecerse en "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|REQUERIDO. El valor debe establecerse en "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|REQUERIDO. El valor debe establecerse en "password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|REQUERIDO. La contraseña de propietario del recurso.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. El URI del punto de conexión de redirección debe ser un URI absoluto.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|REQUERIDO si el parámetro se incluyó "redirect_uri" en la solicitud de autorización y sus valores deben ser idénticos.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|OPCIONAL. El URI del punto de conexión de redirección debe ser un URI absoluto.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|OPCIONAL. El token de actualización, que puede utilizarse para obtener nuevos tokens de acceso.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|OPCIONAL. El token de actualización, que puede utilizarse para obtener nuevos tokens de acceso.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL. El token de actualización, que puede utilizarse para obtener nuevos tokens de acceso.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|REQUERIDO. El valor debe establecerse en "code".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|REQUERIDO. El valor debe establecerse en "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. El ámbito de la solicitud de acceso.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCIONAL si es idéntico al ámbito solicitado por el cliente; de lo contrario REQUERIDO.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|OPCIONAL. El ámbito de la solicitud de acceso.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCIONAL si es idéntico al ámbito solicitado por el cliente; de lo contrario REQUERIDO.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|OPCIONAL. El ámbito de la solicitud de acceso.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCIONAL si es idéntico al ámbito solicitado por el cliente; de lo contrario REQUERIDO.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OPCIONAL. El ámbito de la solicitud de acceso.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL si es idéntico al ámbito solicitado por el cliente; de lo contrario REQUERIDO.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|REQUERIDO si el parámetro "state" estaba presente en la solicitud de autorización de cliente.  El valor exacto recibido del cliente.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|RECOMENDADO. Un valor opaco que utiliza el cliente para mantener el estado entre la solicitud y la devolución de llamada.  El servidor de autorización incluye este valor al redirigir el agente de usuario al cliente.  El parámetro debe usarse para evitar la falsificación de solicitudes entre sitios.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|REQUERIDO si el parámetro "state" estaba presente en la solicitud de autorización de cliente.  El valor exacto recibido del cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|REQUERIDO si el parámetro "state" estaba presente en la solicitud de autorización de cliente.  El valor exacto recibido del cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|RECOMENDADO. Un valor opaco que utiliza el cliente para mantener el estado entre la solicitud y la devolución de llamada.  El servidor de autorización incluye este valor al redirigir el agente de usuario al cliente.  El parámetro debe usarse para evitar la falsificación de solicitudes entre sitios.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|REQUERIDO si el parámetro "state" estaba presente en la solicitud de autorización de cliente.  El valor exacto recibido del cliente.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|REQUERIDO. El tipo del token emitido.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|REQUERIDO. El tipo del token emitido.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|REQUERIDO. El tipo del token emitido.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|REQUERIDO. El tipo del token emitido.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|REQUERIDO. El nombre de usuario del propietario del recurso.|  
|OAuth2UnsupportedTokenType|El tipo de token "{0}" no se admite.|  
|OAuth2InvalidState|Respuesta no válida del servidor de autorización|  
|OAuth2GrantType_AuthorizationCode|Código de autorización|  
|OAuth2GrantType_Implicit|Implícita|  
|OAuth2GrantType_ClientCredentials|Credenciales de cliente|  
|OAuth2GrantType_ResourceOwnerPassword|Contraseña del propietario del recurso|  
|WebDocumentation302Code|302 (encontrado)|  
|WebDocumentation400Code|400 (solicitud incorrecta)|  
|OAuth2SendingMethod_AuthHeader|Encabezado de autorización|  
|OAuth2SendingMethod_QueryParam|Parámetro de consulta|  
|OAuth2AuthorizationServerGeneralException|Se ha producido un error al autorizar el acceso a través de {0}.|  
|OAuth2AuthorizationServerCommunicationException|No se ha podido establecer una conexión HTTP con el servidor de autorización o se ha interrumpido de forma inesperada.|  
|WebDocumentationOAuth2GeneralErrorMessage|Se ha producido un error inesperado.|  
|AuthorizationServerCommunicationException|Se ha producido una excepción de comunicación con el servidor de autorización. Póngase en contacto con el administrador.|  
|TextblockSubscriptionKeyHeaderDescription|Clave de suscripción que proporciona acceso a esta API. Se encontró en el <a href='/developer'\>perfil</a\>.|  
|TextblockOAuthHeaderDescription|Token de acceso OAuth 2.0 obtenido de <i\>{0}</i\>. Tipos de concesión admitidos: <i\>{1}</i\>.|  
|TextblockContentTypeHeaderDescription|Tipo de soporte del cuerpo enviado a la API.|  
|ErrorMessageApiNotAccessible|A la API a la que está tratando de llamar no se puede acceder en este momento. Póngase en contacto con el publicador de la API <a href="/issues"\>aquí</a\>.|  
|ErrorMessageApiTimedout|La API a la que está tratando de llamar está tardando más de lo normal en devolver una respuesta. Póngase en contacto con el publicador de la API <a href="/issues"\>aquí</a\>.|  
|BadRequestParameterExpected|"Se espera el parámetro '{0}'"|  
|TooltipTextDoubleClickToSelectAll|Haga doble clic para seleccionarlo todo.|  
|TooltipTextHideRevealSecret|Mostrar/ocultar|  
|ButtonLinkOpenConsole|Pruébelo|  
|SectionHeadingRequestBody|Cuerpo de la solicitud|  
|SectionHeadingRequestParameters|Parámetros de solicitud|  
|SectionHeadingRequestUrl|URL de solicitud|  
|SectionHeadingResponse|Response|  
|SectionHeadingRequestHeaders|Encabezados de solicitud|  
|FormLabelSubtextOptional|opcional|  
|SectionHeadingCodeSamples|Ejemplos de código|  
|TextblockOpenidConnectHeaderDescription|Token de identificador de OpenID Connect obtenido de <i\>{0}</i\>. Tipos de concesión admitidos: <i\>{1}</i\>.|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Nombre|Texto|  
|----------|----------|  
|LinkLabelBack|atrás|  
|LinkLabelHomePage|página principal|  
|LinkLabelSendUsEmail|Envíenos un correo electrónico|  
|PageTitleError|Lo sentimos, se ha producido un problema al mostrar la página solicitada.|  
|TextblockPotentialCauseIntermittentIssue|Esto puede ser un problema de acceso a los datos intermitente que ya ha pasado.|  
|TextblockPotentialCauseOldLink|El vínculo en el que ha hecho clic puede ser antiguo y no apuntar ya al sitio correcto.|  
|TextblockPotentialCauseTechnicalProblem|Es posible que haya un problema técnico por nuestra parte.|  
|TextblockPotentialSolutionRefresh|Pruebe a actualizar la página.|  
|TextblockPotentialSolutionStartOver|Empiece de nuevo desde nuestro {0}.|  
|TextblockPotentialSolutionTryAgain|Vaya a {0} e intente de nuevo la acción que realizó.|  
|TextReportProblem|{0} en el que se describa el error y lo analizaremos lo antes posible.|  
|TitlePotentialCause|Causa posible|  
|TitlePotentialSolution|Es posible que se trate únicamente de un problema temporal. Hay varias soluciones que puede probar.|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a> IssuesStrings  
  
|Nombre|Texto|  
|----------|----------|  
|WebIssuesIndexTitle|Issues|  
|WebIssuesNoActiveSubscriptions|No tiene ninguna suscripción activa. Debe suscribirse a un producto para informar de un problema.|  
|WebIssuesNotSignin|No ha iniciado sesión. {0} para informar de un problema o publicar un comentario.|  
|WebIssuesReportIssueButton|Informar sobre un problema|  
|WebIssuesSignIn|iniciar sesión|  
|WebIssuesStatusReportedBy|Estado: {0} &#124; notificado por {1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a> NotFoundStrings  
  
|Nombre|Texto|  
|----------|----------|  
|LinkLabelHomePage|página principal|  
|LinkLabelSendUsEmail|envíenos un correo electrónico|  
|PageTitleNotFound|No encontramos la página que busca.|  
|TextblockPotentialCauseMisspelledUrl|Es posible que haya escrito mal la URL si la ha especificado a mano.|  
|TextblockPotentialCauseOldLink|El vínculo en el que ha hecho clic puede ser antiguo y no apuntar ya al sitio correcto.|  
|TextblockPotentialSolutionRetype|Pruebe a volver a escribir la URL.|  
|TextblockPotentialSolutionStartOver|Empiece de nuevo desde nuestro {0}.|  
|TextReportProblem|{0} en el que se describa el error y lo analizaremos lo antes posible.|  
|TitlePotentialCause|Causa posible|  
|TitlePotentialSolution|Posible solución|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Nombre|Texto|  
|----------|----------|  
|WebProductsAgreement|Al suscribirse al producto {0}, acepta los `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Términos de uso|  
|WebProductsSubscribeButton|Suscribirse|  
|WebProductsUsageLimitsHeader|Límites de uso|  
|WebProductsYouAreNotSubscribed|Está suscrito a este producto.|  
|WebProductsYouRequestedSubscription|Ha solicitado una suscripción a este producto.|  
|ErrorYouNeedToAgreeWithLegalTerms|Debe aceptar los términos de uso antes de continuar.|  
|ButtonLabelAddSubscription|Agregar una suscripción|  
|LinkLabelChangeSubscriptionName|cambiar|  
|ButtonLabelConfirm|Confirm|  
|TextblockMultipleSubscriptionsCount|Tiene {0} suscripciones a este producto:|  
|TextblockSingleSubscriptionsCount|Tiene {0} suscripción a este producto:|  
|TextblockSingleApisCount|Este producto contiene {0} API:|  
|TextblockMultipleApisCount|Este producto contiene {0} API:|  
|TextblockHeaderSubscribe|Suscribirse al producto|  
|TextblockSubscriptionDescription|Se creará una nueva suscripción de la siguiente manera:|  
|TextblockSubscriptionLimitReached|Ha alcanzado el límite de suscripciones.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a> ProductsStrings  
  
|Nombre|Texto|  
|----------|----------|  
|PageTitleProducts|Productos|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Nombre|Texto|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Los administradores han deshabilitado el inicio de sesión actualmente.|  
|TextboxExternalIdentitiesSigninInvitation|O bien inicie sesión con|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Inicie sesión con:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a> SigninResources  
  
|Nombre|Texto|  
|----------|----------|  
|PrincipalNotFound|No se encuentra la entidad de seguridad o la firma no es válida.|  
|ErrorSsoAuthenticationFailed|Error de autenticación SSO.|  
|ErrorSsoAuthenticationFailedDetailed|Se ha proporcionado un token no válido o no se ha podido comprobar la firma.|  
|ErrorSsoTokenInvalid|El token SSO no es válido.|  
|ValidationErrorSpecificEmailAlreadyExists|El correo electrónico "{0}" ya está registrado.|  
|ValidationErrorSpecificEmailInvalid|El correo electrónico "{0}" no es válido.|  
|ValidationErrorPasswordInvalid|La contraseña no es válida. Corrija los errores y vuelva a intentarlo.|  
|PropertyTooShort|La propiedad {0} es demasiado corta.|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Dirección de correo electrónico no válida.|  
|ValidationMessageNewPasswordConfirmationRequired|Confirme la nueva contraseña.|  
|ValidationErrorPasswordConfirmationRequired|El campo de la contraseña de confirmación está vacío.|  
|WebAuthenticationEmailChangeNotice|El mensaje de correo electrónico de confirmación del cambio va de camino a {0}. Siga las instrucciones incluidas en él para confirmar la nueva dirección de correo electrónico. Si no recibe el correo electrónico en su bandeja de entrada en los próximos minutos, consulte su carpeta de correo no deseado.|  
|WebAuthenticationEmailChangeNoticeHeader|Se ha procesado correctamente la solicitud de cambio de correo electrónico.|  
|WebAuthenticationEmailChangeNoticeTitle|Cambio de correo electrónico solicitado|  
|WebAuthenticationEmailHasBeenRevertedNotice|Su dirección de correo electrónico ya existe. Se ha revertido la solicitud.|  
|ValidationErrorEmailAlreadyExists|La dirección de correo electrónico ya existe.|  
|ValidationErrorEmailInvalid|Dirección de correo electrónico no válida|  
|TextboxLabelEmail|Email|  
|ValidationErrorEmailRequired|Se requiere un correo electrónico.|  
|WebAuthenticationErrorNoticeHeader|Error|  
|WebAuthenticationFieldLengthErrorMessage|{0} debe tener una extensión máxima de {1}|  
|TextboxLabelEmailFirstName|Nombre|  
|ValidationErrorFirstNameRequired|Debe especificar un nombre.|  
|ValidationErrorFirstNameInvalid|Nombre no válido|  
|NoticeInvalidInvitationToken|Tenga en cuenta que los vínculos de confirmación solo son válidos durante 48 horas. Si aún no ha superado ese plazo, asegúrese de que el vínculo sea correcto. Si ha expirado el vínculo, repita la acción que trata de confirmar.|  
|NoticeHeaderInvalidInvitationToken|Token de invitación no válido|  
|NoticeTitleInvalidInvitationToken|Correo electrónico de confirmación|  
|WebAuthenticationLastNameInvalidErrorMessage|Apellidos no válidos|  
|TextboxLabelEmailLastName|Apellido|  
|ValidationErrorLastNameRequired|Debe especificar un apellido.|  
|WebAuthenticationLinkExpiredNotice|El vínculo de confirmación que se le ha enviado ha expirado. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|El vínculo de restablecimiento de la contraseña no es válido o ha expirado.|  
|WebAuthenticationLinkExpiredNoticeTitle|Vínculo enviado|  
|WebAuthenticationNewPasswordLabel|Contraseña nueva|  
|ValidationMessageNewPasswordRequired|Se requiere una nueva contraseña.|  
|TextboxLabelNotificationsSenderEmail|Correo electrónico del remitente de las notificaciones|  
|TextboxLabelOrganizationName|Nombre de la organización|  
|WebAuthenticationOrganizationRequiredErrorMessage|El campo del nombre de la organización está vacío.|  
|WebAuthenticationPasswordChangedNotice|La contraseña se ha actualizado correctamente.|  
|WebAuthenticationPasswordChangedNoticeTitle|Contraseña actualizada|  
|WebAuthenticationPasswordCompareErrorMessage|Las contraseñas no coinciden.|  
|WebAuthenticationPasswordConfirmLabel|Confirmación de la contraseña|  
|ValidationErrorPasswordInvalidDetailed|La contraseña es demasiado poco segura.|  
|WebAuthenticationPasswordLabel|Contraseña|  
|ValidationErrorPasswordRequired|La contraseña es obligatoria.|  
|WebAuthenticationPasswordResetSendNotice|El mensaje de correo electrónico de confirmación del cambio de contraseña va de camino a {0}. Siga las instrucciones incluidas en el mensaje de correo electrónico para continuar con el proceso de cambio de la contraseña.|  
|WebAuthenticationPasswordResetSendNoticeHeader|La solicitud de restablecimiento de contraseña se ha procesado correctamente.|  
|WebAuthenticationPasswordResetSendNoticeTitle|Se solicitó el restablecimiento de contraseña.|  
|WebAuthenticationRequestNotFoundNotice|Solicitud no encontrada|  
|WebAuthenticationSenderEmailRequiredErrorMessage|El campo del correo electrónico del remitente de las notificaciones está vacío.|  
|WebAuthenticationSigninPasswordLabel|Confirme el cambio especificando una contraseña.|  
|WebAuthenticationSignupConfirmNotice|Se ha enviado un correo electrónico de confirmación del registro a {0}.<br /\> Siga las instrucciones incluidas en el correo electrónico para activar la cuenta.<br /\> Si no recibe el correo electrónico en la bandeja de entrada en los próximos minutos, consulte la carpeta de correo no deseado.|  
|WebAuthenticationSignupConfirmNoticeHeader|La cuenta se ha creado correctamente.|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Se ha vuelto a enviar el mensaje de correo electrónico de confirmación de registro.|  
|WebAuthenticationSignupConfirmNoticeTitle|Cuenta creada|  
|WebAuthenticationTokenRequiredErrorMessage|El token está vacío.|  
|WebAuthenticationUserAlreadyRegisteredNotice|Parece que ya hay un usuario con esa dirección de correo electrónico registrado en el sistema. Si ha olvidado la contraseña, intente restaurarla o póngase en contacto con nuestro equipo de soporte técnico.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|El usuario ya está registrado.|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Ya registrado|  
|ButtonLabelChangePassword|Cambiar contraseña|  
|ButtonLabelChangeAccountInfo|Cambiar información de la cuenta|  
|ButtonLabelCloseAccount|Cerrar cuenta|  
|WebAuthenticationInvalidCaptchaErrorMessage|El texto escrito no coincide con el de la imagen. Inténtelo de nuevo.|  
|ValidationErrorCredentialsInvalid|El correo electrónico o la contraseña no son válidos. Corrija los errores y vuelva a intentarlo.|  
|WebAuthenticationRequestIsNotValid|La solicitud no es válida.|  
|WebAuthenticationUserIsNotConfirm|Confirme el registro antes de tratar de iniciar sesión.|  
|WebAuthenticationInvalidEmailFormated|El correo electrónico no es válido: {0}|  
|WebAuthenticationUserNotFound|Usuario no encontrado|  
|WebAuthenticationTenantNotRegistered|La cuenta pertenece a un inquilino de Azure Active Directory que no está autorizado para acceder a este portal.|  
|WebAuthenticationAuthenticationFailed|Error de autenticación.|  
|WebAuthenticationGooglePlusNotEnabled|Error de autenticación. Si ha autorizado la aplicación, póngase en contacto con el administrador para asegurarse de que la autenticación de Google está configurada correctamente.|  
|ValidationErrorAllowedTenantIsRequired|Se requiere un inquilino permitido.|  
|ValidationErrorTenantIsNotValid|El inquilino de Azure Active Directory "{0}" no es válido.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Inicie sesión con la cuenta de {0}.|  
|WebAuthenticationUserLimitNotice|Este servicio ha alcanzado el número máximo de usuarios permitidos. `<a href="mailto:{0}"\>contact the administrator</a\>` para actualizar su servicio y volver a habilitar el registro de usuarios.|  
|WebAuthenticationUserLimitNoticeHeader|Registro de usuarios deshabilitado|  
|WebAuthenticationUserLimitNoticeTitle|Registro de usuarios deshabilitado|  
|WebAuthenticationUserRegistrationDisabledNotice|El administrador ha deshabilitado el registro de usuarios. Inicie sesión con el proveedor de identidades externo.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registro de usuarios deshabilitado|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registro de usuarios deshabilitado|  
|WebAuthenticationSignupPendingConfirmationNotice|Para poder completar la creación de su cuenta, tenemos que comprobar su dirección de correo electrónico. Hemos enviado un mensaje de correo electrónico a {0}. Siga las instrucciones incluidas en el mensaje de correo electrónico para activar su cuenta. Si no le llega el mensaje de correo electrónico en unos minutos, consulte su carpeta de correo no deseado.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Se ha encontrado una cuenta no confirmada para la dirección de correo electrónico {0}. Para completar la creación de su cuenta, tenemos que comprobar su dirección de correo electrónico. Hemos enviado un mensaje de correo electrónico a {0}. Siga las instrucciones incluidas en el mensaje de correo electrónico para activar su cuenta. Si no le llega el mensaje de correo electrónico en unos minutos, consulte su carpeta de correo no deseado.|  
|WebAuthenticationSignupConfirmationAlmostDone|Casi ha terminado.|  
|WebAuthenticationSignupConfirmationEmailSent|Hemos enviado un mensaje de correo electrónico a {0}. Siga las instrucciones incluidas en el mensaje de correo electrónico para activar su cuenta. Si no le llega el mensaje de correo electrónico en unos minutos, consulte su carpeta de correo no deseado.|  
|WebAuthenticationEmailSentNotificationMessage|Se ha enviado un mensaje de correo electrónico correctamente a {0}.|  
|WebAuthenticationNoAadTenantConfigured|No hay ningún inquilino de Azure Active Directory configurado para el servicio.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Acepto los `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Revise los `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`.|  
|DialogHeadingTermsOfUse|Términos de uso|  
|ValidationMessageConsentNotAccepted|Debe aceptar los términos de uso antes de continuar.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a> SigninStrings  
  
|Nombre|Texto|  
|----------|----------|  
|WebAuthenticationForgotPassword|¿Ha olvidado la contraseña?|  
|WebAuthenticationIfAdministrator|Si es administrador, debe iniciar sesión en `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|¿Aún no es miembro? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Recordarme en este equipo|  
|WebAuthenticationSigininWithPassword|Inicie sesión con su nombre de usuario y contraseña.|  
|WebAuthenticationSigninTitle|Inicio de sesión|  
|WebAuthenticationSignUpNow|Regístrese ahora|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a> SignupStrings  
  
|Nombre|Texto|  
|----------|----------|  
|PageTitleSignup|Suscripción|  
|WebAuthenticationAlreadyAMember|¿Ya es miembro?|  
|WebAuthenticationCreateNewAccount|Cree una nueva cuenta de API Management.|  
|WebAuthenticationSigninNow|Inicie sesión ahora|  
|ButtonLabelSignup|Suscripción|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Nombre|Texto|  
|----------|----------|  
|SubscriptionCancelConfirmation|¿Está seguro de que desea cancelar esta suscripción?|  
|SubscriptionRenewConfirmation|¿Está seguro de que desea renovar esta suscripción?|  
|WebDevelopersManageSubscriptions|Administrar suscripciones|  
|WebDevelopersPrimaryKey|Clave principal|  
|WebDevelopersRegenerateLink|Regenerar|  
|WebDevelopersSecondaryKey|Clave secundaria|  
|ButtonLabelShowKey|Mostrar|  
|ButtonLabelRenewSubscription|Renovación|  
|WebDevelopersSubscriptionRequested|Solicitud realizada el {0}|  
|WebDevelopersSubscriptionRequestedState|Solicitada|  
|WebDevelopersSubscriptionTableNameHeader|Nombre|  
|WebDevelopersSubscriptionTableStateHeader|State|  
|WebDevelopersUsageStatisticsLink|Informes de análisis|  
|WebDevelopersYourSubscriptions|Sus suscripciones|  
|SubscriptionPropertyLabelRequestedDate|Solicitud realizada el|  
|SubscriptionPropertyLabelStartedDate|Iniciado el|  
|PageTitleRenameSubscription|Cambiar el nombre de la suscripción|  
|SubscriptionPropertyLabelName|Nombre de suscripción|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Nombre|Texto|  
|----------|----------|  
|SectionHeadingCloseAccount|¿Desea para cerrar su cuenta?|  
|PageTitleDeveloperProfile|Perfil|  
|ButtonLabelHideKey|Ocultar|  
|ButtonLabelRegenerateKey|Regenerar|  
|InformationMessageKeyWasRegenerated|¿Está seguro de que desea regenerar esta clave?|  
|ButtonLabelShowKey|Mostrar|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Nombre|Texto|  
|----------|----------|  
|ButtonLabelUpdateProfile|Actualizar perfil|  
|PageTitleUpdateProfile|Actualizar información de cuenta|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a> UserProfile  
  
|Nombre|Texto|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Cambiar información de la cuenta|  
|ButtonLabelChangePassword|Cambiar contraseña|  
|ButtonLabelCloseAccount|Cerrar cuenta|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|Nombre|  
|TextboxLabelEmailLastName|Apellido|  
|TextboxLabelNotificationsSenderEmail|Correo electrónico del remitente de las notificaciones|  
|TextboxLabelOrganizationName|Nombre de la organización|  
|SubscriptionStateActive|Active|  
|SubscriptionStateCancelled|Cancelado|  
|SubscriptionStateExpired|Expirada|  
|SubscriptionStateRejected|Rechazada|  
|SubscriptionStateRequested|Solicitada|  
|SubscriptionStateSuspended|Suspended|  
|DefaultSubscriptionNameTemplate|{0} (valor predeterminado)|  
|SubscriptionNameTemplate|Acceso de desarrollador n.º{0}|  
|TextboxLabelSubscriptionName|Nombre de suscripción|  
|ValidationMessageSubscriptionNameRequired|El nombre de suscripción no puede estar vacío.|  
|ApiManagementUserLimitReached|Este servicio ha alcanzado el número máximo de usuarios permitidos. Actualice a un plan de tarifa superior.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a> Recursos de glifo  
 Las plantillas del portal para desarrolladores de API Management puede usar los glifos de [Glyphicons de Bootstrap](https://getbootstrap.com/components/#glyphicons). Este conjunto de glifos incluye más de 250 glifos en formato de fuente del conjunto [Glyphicon Halflings](https://glyphicons.com/). Para utilizar un glifo de este conjunto, utilice la siguiente sintaxis.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Para obtener la lista completa de glifos, consulte [Glyphicons de Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo trabajar con plantillas, consulte [Cómo personalizar el portal para desarrolladores de API Management mediante plantillas](api-management-developer-portal-templates.md).
