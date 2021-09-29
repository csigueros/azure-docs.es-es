---
title: Referencia de modelo de datos de la plantilla de Azure API Management | Microsoft Docs
description: Aprenda sobre las representaciones de entidad y tipo de elementos comunes que se usan en los modelos de datos en las plantillas de portal para desarrolladores de Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: danlep
ms.openlocfilehash: 7b5ccd7f539332885cdaae242e9e33416633bea9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639078"
---
# <a name="azure-api-management-template-data-model-reference"></a>Referencia de modelo de datos de la plantilla de Azure API Management
Este tema describe las representaciones de entidad y tipo de elementos comunes que se usan en los modelos de datos en las plantillas de portal para desarrolladores de Azure API Management.  
  
 Para más información sobre cómo trabajar con plantillas, consulte [Cómo personalizar el portal para desarrolladores de API Management mediante plantillas](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Referencia

-   [API](#API)  
-   [Resumen de API](#APISummary)  
-   [Aplicación](#Application)  
-   [Datos adjuntos](#Attachment)  
-   [Código de ejemplo](#Sample)  
-   [Comentario](#Comment)  
-   [Filtros](#Filtering)  
-   [Header](#Header)  
-   [Solicitud HTTP](#HTTPRequest)  
-   [Respuesta HTTP](#HTTPResponse)  
-   [Problema](#Issue)  
-   [operación](#Operation)  
-   [Operation menu](#Menu)  
-   [Elemento de menú de operaciones](#MenuItem)  
-   [Paginación](#Paging)  
-   [Parámetro](#Parameter)  
-   [Producto](#Product)  
-   [Proveedor](#Provider)  
-   [Representación](#Representation)  
-   [Suscripción](#Subscription)  
-   [Resumen de suscripción](#SubscriptionSummary)  
-   [Información de cuenta de usuario](#UserAccountInfo)  
-   [Inicio de sesión de usuario](#UseSignIn)  
-   [Registro de usuario](#UserSignUp)  
  
##  <a name="api"></a>API <a name="API"></a>  
 La entidad `API` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recursos. Identifica de forma única la API de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `apis/{id}`, donde `{id}` es un identificador de API. Esta propiedad es de sólo lectura.|  
|`name`|string|Nombre de la API. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|`description`|string|Descripción de la API. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
|`serviceUrl`|string|Dirección URL absoluta del servicio back-end que implementa esta API.|  
|`path`|string|Dirección URL relativa que identifica de forma única esta API y todas las rutas de acceso a sus recursos dentro de la instancia del servicio API Management. Se anexa a la dirección URL base del punto de conexión de API que se especificó durante la creación de la instancia de servicio para formar una dirección URL pública para esta API.|  
|`protocols`|matriz de número|Describe en qué protocolos se pueden invocar las operaciones en esta API. Los valores permitidos son `1 - http` y `2 - https`, o ambos.|  
|`authenticationSettings`|[Configuración de autenticación del servidor de autorización](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Colección de ajustes de autenticación que se incluyen en esta API.|  
|`subscriptionKeyParameterNames`|object|Propiedad opcional que puede utilizarse para especificar nombres personalizados para los parámetros de la consulta o encabezado que contiene la clave de suscripción. Cuando esta propiedad está presente, debe contener al menos una de las dos propiedades siguientes.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a> Resumen de API  
 La entidad `API summary` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recursos. Identifica de forma única la API de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `apis/{id}`, donde `{id}` es un identificador de API. Esta propiedad es de sólo lectura.|  
|`name`|string|Nombre de la API. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|`description`|string|Descripción de la API. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
  
##  <a name="application"></a><a name="Application"></a> Application  
 La entidad `application` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador único de la aplicación.|  
|`Title`|string|Título de la aplicación.|  
|`Description`|string|Descripción de la aplicación.|  
|`Url`|URI|Identificador URI de la aplicación.|  
|`Version`|string|Información de versión de la aplicación.|  
|`Requirements`|string|Descripción de los requisitos de la aplicación.|  
|`State`|number|Estado actual de la aplicación.<br /><br /> -0 - registrada<br /><br /> -1 - enviada<br /><br /> -2 - publicada<br /><br /> -3 - rechazada<br /><br /> -4 - no publicada|  
|`RegistrationDate`|DateTime|Fecha y hora a las que se registró la aplicación.|  
|`CategoryId`|number|Categoría de la aplicación (finanzas, entretenimiento, etcétera).|  
|`DeveloperId`|string|Identificador único del desarrollador que envió la aplicación.|  
|`Attachments`|Colección de entidades [Attachment](#Attachment).|Cualquier tipo de datos adjuntos de la aplicación, como capturas de pantalla o iconos.|  
|`Icon`|[Datos adjuntos](#Attachment)|Icono de la aplicación.|  
  
##  <a name="attachment"></a><a name="Attachment"></a> Datos adjuntos  
 La entidad `attachment` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`UniqueId`|string|Identificador único de los datos adjuntos.|  
|`Url`|string|Dirección URL del recurso.|  
|`Type`|string|Tipo de datos adjuntos.|  
|`ContentType`|string|Tipo de medios de los datos adjuntos.|  
  
##  <a name="code-sample"></a><a name="Sample"></a> Código de ejemplo  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`title`|string|Nombre de la operación.|  
|`snippet`|string|Esta propiedad está en desuso y no debe utilizarse.|  
|`brush`|string|Especifica qué plantilla de color de sintaxis de código se usará al mostrar el ejemplo de código. Los valores permitidos son `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby` y `csharp`.|  
|`template`|string|Nombre de esta plantilla de ejemplo de código.|  
|`body`|string|Un marcador de posición para la parte del ejemplo de código del fragmento de código.|  
|`method`|string|Método HTTP de la operación.|  
|`scheme`|string|Protocolo que se debe usar en la solicitud de operación.|  
|`path`|string|Ruta de acceso de la operación.|  
|`query`|string|Ejemplo de cadena de consulta con parámetros definidos.|  
|`host`|string|Dirección URL de la puerta de enlace del servicio API Management para la API que contiene esta operación.|  
|`headers`|Colección de entidades [Header](#Header).|Encabezados de esta operación.|  
|`parameters`|Colección de entidades [Parameter](#Parameter).|Los parámetros definidos para esta operación.|  
  
##  <a name="comment"></a><a name="Comment"></a> Comment  
 La entidad `API` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Id`|number|Identificador del comentario.|  
|`CommentText`|string|Cuerpo del comentario. Puede incluir HTML.|  
|`DeveloperCompany`|string|Nombre de la empresa del desarrollador.|  
|`PostedOn`|DateTime|Fecha y hora de publicación del comentario.|  
  
##  <a name="issue"></a><a name="Issue"></a> Issue  
 La entidad `issue` tiene las siguientes propiedades.  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador único del problema.|  
|`ApiID`|string|Identificador de la API sobre la que se notificó este problema.|  
|`Title`|string|Título del problema.|  
|`Description`|string|Descripción del problema.|  
|`SubscriptionDeveloperName`|string|Nombre del desarrollador que notificó el problema.|  
|`IssueState`|string|Estado actual del problema. Los valores posibles son Proposed (Propuesto), Opened (Abierto), Closed (Cerrado).|  
|`ReportedOn`|DateTime|Fecha y hora a las que se notificó el problema.|  
|`Comments`|Colección de entidades [Comment](#Comment).|Comentarios sobre este problema.|  
|`Attachments`|Colección de entidades [Attachment](api-management-template-data-model-reference.md#Attachment).|Datos adjuntos al problema.|  
|`Services`|Colección de entidades [API](#API).|Las API a las que está suscrito el usuario que archivó el problema.|  
  
##  <a name="filtering"></a><a name="Filtering"></a> Filtrado  
 La entidad `filtering` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Pattern`|string|El término de búsqueda actual; o `null` si no hay ningún término de búsqueda.|  
|`Placeholder`|string|Texto que se muestra en el cuadro de búsqueda cuando no hay ningún término de búsqueda especificado.|  
  
##  <a name="header"></a><a name="Header"></a> Header  
 Esta sección describe la representación de `parameter`.  
  
|Propiedad|Tipo|Descripción|  
|--------------|-----------------|----------|  
|`name`|string|Nombre del parámetro.|  
|`description`|string|Descripción del parámetro.|  
|`value`|string|Valor del encabezado.|  
|`typeName`|string|Tipo de datos del valor del encabezado.|  
|`options`|string|Opciones.|  
|`required`|boolean|Especifica si el encabezado es necesario.|  
|`readOnly`|boolean|Especifica si el encabezado es de solo lectura.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a> HTTP Request  
 Esta sección describe la representación de `request`.  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`description`|string|Descripción de la solicitud de operación.|  
|`headers`|matriz de entidades [Header](#Header).|Encabezados de solicitud.|  
|`parameters`|matriz de [Parameter](#Parameter)|Colección de parámetros de solicitud de operación.|  
|`representations`|matriz de [Representation](#Representation)|Colección de representaciones de solicitud de operación.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a> HTTP Response  
 Esta sección describe la representación de `response`.  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`statusCode`|entero positivo|Código de estado de la respuesta de la operación.|  
|`description`|string|Descripción de la respuesta de la operación.|  
|`representations`|matriz de [Representation](#Representation)|Colección de representaciones de respuesta de operación.|  
  
##  <a name="operation"></a><a name="Operation"></a> Operation  
 La entidad `operation` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recursos. Identifica de forma única la operación de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `apis/{aid}/operations/{id}`, donde `{aid}` es un identificador de API y `{id}` es un identificador de operación. Esta propiedad es de sólo lectura.|  
|`name`|string|Nombre de la operación. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|`description`|string|Descripción de la operación. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
|`scheme`|string|Describe en qué protocolos se pueden invocar las operaciones en esta API. Los valores permitidos son `http`, `https` o tanto `http` como `https`.|  
|`uriTemplate`|string|Plantilla de dirección URL relativa identifica el recurso de destino para esta operación. Puede incluir parámetros. Ejemplo: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|Dirección URL de la puerta de enlace de API Management que hospeda la API.|  
|`httpMethod`|string|Método HTTP de la operación.|  
|`request`|[Solicitud HTTP](#HTTPRequest)|Una entidad que contiene los detalles de la solicitud.|  
|`responses`|matriz de [HTTP Response](#HTTPResponse)|Matriz de entidades [HTTP Response](#HTTPResponse) de la operación.|  
  
##  <a name="operation-menu"></a><a name="Menu"></a> Menú de operaciones  
 La entidad `operation menu` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`ApiId`|string|Identificador de la API actual.|  
|`CurrentOperationId`|string|Identificador de la operación actual.|  
|`Action`|string|Tipo de menú.|  
|`MenuItems`|Colección de entidades [Operation menu item](#MenuItem).|Operaciones de la API actual.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a> Elemento de menú de operaciones  
 La entidad `operation menu item` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de la operación.|  
|`Title`|string|Descripción de la operación.|  
|`HttpMethod`|string|Método HTTP de la operación.|  
  
##  <a name="paging"></a><a name="Paging"></a> Paginación  
 La entidad `paging` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Page`|number|Número de página actual.|  
|`PageSize`|number|Número máximo de resultados que se mostrarán en una misma página.|  
|`TotalItemCount`|number|Número de elementos que se muestran.|  
|`ShowAll`|boolean|Especifica si se deben mostrar todos los resultados en una misma página.|  
|`PageCount`|number|Número de páginas de resultados.|  
  
##  <a name="parameter"></a><a name="Parameter"></a> Parameter  
 Esta sección describe la representación de `parameter`.  
  
|Propiedad|Tipo|Descripción|  
|--------------|-----------------|----------|  
|`name`|string|Nombre del parámetro.|  
|`description`|string|Descripción del parámetro.|  
|`value`|string|Valor del parámetro.|  
|`options`|matriz de cadena|Valores definidos para los valores de parámetro de consulta.|  
|`required`|boolean|Especifica si el parámetro es necesario o no.|  
|`kind`|number|Determina si este parámetro es un parámetro de ruta de acceso (1) o un parámetro de cadena de consulta (2).|  
|`typeName`|string|Tipo de parámetro.|  
  
##  <a name="product"></a><a name="Product"></a> Product  
 La entidad `product` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recursos. Identifica de forma única el producto en la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `products/{pid}`, donde `{pid}` es un identificador de producto. Esta propiedad es de sólo lectura.|  
|`Title`|string|Nombre del producto. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|`Description`|string|Descripción del producto. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
|`Terms`|string|Términos de uso del producto. Los desarrolladores que traten de suscribirse al producto verán y deberán aceptar estos términos para poder completar el proceso de suscripción.|  
|`ProductState`|number|Especifica si el producto se ha publicado o no. Los desarrolladores pueden consultar los productos publicados en el portal para desarrolladores. Solo los administradores pueden ver los productos que no se han publicado.<br /><br /> Los valores de estado del producto permitidos son:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Especifica si un usuario puede tener varias suscripciones a este producto al mismo tiempo.|  
|`MultipleSubscriptionsCount`|number|Número máximo de suscripciones a este producto que un usuario puede tener al mismo tiempo.|  
  
##  <a name="provider"></a><a name="Provider"></a> Provider  
 La entidad `provider` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Properties`|diccionario de cadenas|Propiedades de este proveedor de autenticación.|  
|`AuthenticationType`|string|Tipo de proveedor. (Azure Active Directory, inicio de sesión de Facebook, cuenta de Google, cuenta de Microsoft, Twitter).|  
|`Caption`|string|Nombre para mostrar del proveedor.|  
  
##  <a name="representation"></a><a name="Representation"></a> Representación  
 En esta sección se describe `representation`.  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`contentType`|string|Especifica un tipo de contenido personalizado o registrado para esta representación, por ejemplo, `application/xml`.|  
|`sample`|string|Un ejemplo de la representación.|  
  
##  <a name="subscription"></a>Suscripción a <a name="Subscription"></a>  
 La entidad `subscription` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recursos. Identifica de forma única la suscripción de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `subscriptions/{sid}`, donde `{sid}` es un identificador de suscripción. Esta propiedad es de sólo lectura.|  
|`ProductId`|string|El identificador de recurso de producto del producto suscrito. El valor es una dirección URL relativa válida con el formato `products/{pid}`, donde `{pid}` es un identificador de producto.|  
|`ProductTitle`|string|Nombre del producto. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|`ProductDescription`|string|Descripción del producto. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
|`ProductDetailsUrl`|string|Dirección URL relativa a los detalles del producto.|  
|`state`|string|Estado de la suscripción. Los estados posibles son:<br /><br /> - `0 - suspended`: la suscripción está bloqueada y el suscriptor no puede llamar a ninguna API del producto.<br /><br /> - `1 - active`: la suscripción está activa.<br /><br /> - `2 - expired`: la suscripción ha alcanzado su fecha de expiración y se ha desactivado.<br /><br /> - `3 - submitted`: el desarrollador ha realizado una solicitud de suscripción, pero esta aún no se ha aprobado ni rechazado.<br /><br /> - `4 - rejected`: un administrador ha rechazado la solicitud de suscripción.<br /><br /> - `5 - cancelled`: el desarrollador o el administrador han cancelado la suscripción.|  
|`DisplayName`|string|Nombre para mostrar de la suscripción.|  
|`CreatedDate`|dateTime|Fecha en la que se creó la suscripción, en formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|boolean|Especifica si el usuario actual puede cancelar la suscripción.|  
|`IsAwaitingApproval`|boolean|Especifica si la suscripción está en espera de aprobación.|  
|`StartDate`|dateTime|Fecha de inicio de la suscripción, en formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|dateTime|Fecha de expiración de la suscripción, en formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|dateTime|Fecha de notificación de la suscripción, en formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|Clave de suscripción principal. La longitud máxima es de 256 caracteres.|  
|`secondaryKey`|string|Clave de suscripción secundaria. La longitud máxima es de 256 caracteres.|  
|`CanBeRenewed`|boolean|Especifica si el usuario actual puede renovar la suscripción.|  
|`HasExpired`|boolean|Especifica si la suscripción ha expirado.|  
|`IsRejected`|boolean|Especifica si la solicitud de suscripción ha sido denegada.|  
|`CancelUrl`|string|La dirección URL relativa para cancelar la suscripción.|  
|`RenewUrl`|string|La dirección URL relativa para renovar la suscripción.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a> Resumen de suscripción  
 La entidad `subscription summary` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recursos. Identifica de forma única la suscripción de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `subscriptions/{sid}`, donde `{sid}` es un identificador de suscripción. Esta propiedad es de sólo lectura.|  
|`DisplayName`|string|Nombre para mostrar de la suscripción.|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a> Información de cuenta de usuario  
 La entidad `user account info` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`FirstName`|string|Nombre. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|`LastName`|string|Apellidos. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|`Email`|string|Dirección de correo electrónico. No debe estar vacía y debe ser única dentro de la instancia de servicio. La longitud máxima es de 254 caracteres.|  
|`Password`|string|Contraseña de la cuenta de usuario.|  
|`NameIdentifier`|string|Identificador de cuenta, es igual al correo electrónico del usuario.|  
|`ProviderName`|string|Nombre del proveedor de autenticación.|  
|`IsBasicAccount`|boolean|Es True si esta cuenta se registró mediante un correo electrónico y una contraseña; False si la cuenta se registró con un proveedor.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a> Inicio de sesión de usuario  
 La entidad `user sign in` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`Email`|string|Dirección de correo electrónico. No debe estar vacía y debe ser única dentro de la instancia de servicio. La longitud máxima es de 254 caracteres.|  
|`Password`|string|Contraseña de la cuenta de usuario.|  
|`ReturnUrl`|string|Dirección URL de la página donde el usuario hizo clic en el vínculo de inicio de sesión.|  
|`RememberMe`|boolean|Especifica si se desea guardar la información del usuario actual.|  
|`RegistrationEnabled`|boolean|Especifica si el registro se ha habilitado.|  
|`DelegationEnabled`|boolean|Especifica si está habilitado el inicio de sesión delegado.|  
|`DelegationUrl`|string|Dirección URL de inicio de sesión delegado, si está habilitado.|  
|`SsoSignUpUrl`|string|Inicio de sesión único en la dirección URL del usuario, si está presente.|  
|`AuxServiceUrl`|string|Si el usuario actual es un administrador, esto es un vínculo a la instancia de servicio en Azure Portal.|  
|`Providers`|Colección de entidades [Provider](#Provider).|Los proveedores de autenticación de este usuario.|  
|`UserRegistrationTerms`|string|Condiciones que un usuario debe aceptar para poder iniciar sesión.|  
|`UserRegistrationTermsEnabled`|boolean|Especifica si las condiciones están habilitadas.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a> Suscripción de usuario  
 La entidad `user sign up` tiene las siguientes propiedades:  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|`Password`|string|Contraseña de la cuenta de usuario.|  
|`PasswordVerdictLevel`|number|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|`UserRegistrationTerms`|string|Condiciones que un usuario debe aceptar para poder iniciar sesión.|  
|`UserRegistrationTermsOptions`|number|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|`ConsentAccepted`|boolean|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|`Email`|string|Dirección de correo electrónico. No debe estar vacía y debe ser única dentro de la instancia de servicio. La longitud máxima es de 254 caracteres.|  
|`FirstName`|string|Nombre. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|`LastName`|string|Apellidos. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|`UserData`|string|Valor utilizado por el control de [sign-up](api-management-page-controls.md#sign-up).|  
|`NameIdentifier`|string|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|`ProviderName`|string|Nombre del proveedor de autenticación.|

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo trabajar con plantillas, consulte [Cómo personalizar el portal para desarrolladores de API Management mediante plantillas](api-management-developer-portal-templates.md).
