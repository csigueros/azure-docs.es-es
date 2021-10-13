---
title: Protección del acceso híbrido con la integración de asociados de Azure AD
description: Ayude a los clientes a detectar aplicaciones SaaS y migrarlas a Azure AD, así como a conectar con Azure AD aplicaciones que usan métodos de autenticación heredados.
titleSuffix: Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f700b4850f68f9938bc574b26172f49c1d7c0a73
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613821"
---
# <a name="secure-hybrid-access-with-azure-active-directory-partner-integrations"></a>Protección del acceso híbrido con integraciones de asociados de Azure Active Directory

Azure Active Directory (Azure AD) admite protocolos de autenticación modernos que ayudan a mantener seguras las aplicaciones en un mundo altamente conectado basado en la nube. Pero muchas aplicaciones empresariales se crearon para funcionar en una red corporativa protegida, y algunas de ellas usan métodos de autenticación heredados. Ahora que las empresas intentan desarrollar una estrategia de Confianza cero y admitir entornos de trabajo híbridos y en la nube, necesitan soluciones que conecten las aplicaciones a Azure AD y que proporcionen autenticación moderna para las aplicaciones heredadas.

Azure AD admite de forma nativa protocolos modernos como SAML, WS-Fed y OIDC. El proxy de aplicaciones de Azure AD admite la autenticación Kerberos y basada en encabezados. Todavía no se admiten otros protocolos, como SSH, NTLM, LDAP y cookies, aunque los fabricantes independientes de software pueden crear soluciones para conectar estas aplicaciones a Azure AD a fin de dar soporte a los clientes en su recorrido hacia la Confianza cero.

Los ISV tienen la oportunidad de ayudar a los clientes a detectar y migrar aplicaciones de software como servicio (SaaS) a Azure AD. También pueden conectar aplicaciones que usan métodos de autenticación heredados a Azure AD. Esto ayudará a los clientes a consolidarse en una única plataforma (Azure AD), ya que ello les permitirá simplificar la administración de aplicaciones e implementar los principios del modelo Confianza cero. La compatibilidad con aplicaciones que usan autenticación heredada protege a los usuarios. Esta solución puede ser una medida provisional hasta que los clientes modernizan sus aplicaciones para admitir protocolos de autenticación modernos.

## <a name="solution-overview"></a>Información general de la solución

La solución que compile puede incluir lo siguiente:

1. **Detección de aplicaciones**. Es habitual que los clientes no sean conscientes de todas las aplicaciones que usan. Por tanto, el primer paso puede ser compilar capacidades de detección de aplicaciones en la solución y aplicaciones de superficie detectada en la interfaz de usuario. Esto permite al cliente priorizar cómo quiere abordar la integración de sus aplicaciones en Azure AD.
2. **Migración de aplicaciones**. Luego puede crear un flujo de trabajo en el producto donde el cliente pueda integrar directamente las aplicaciones en Azure AD sin tener que ir al portal de Azure AD. Si no implementa capacidades de detección en la solución, puede iniciar la solución aquí, integrando las aplicaciones que los clientes conocen en Azure AD.
3. **Compatibilidad con la autenticación heredada**. Puede usar métodos de autenticación heredados para conectar las aplicaciones a Azure AD, de modo que obtengan las ventajas del inicio de sesión único (SSO) y otras características.
4. **Acceso condicional**. Como característica adicional, puede permitir que los clientes apliquen directivas de [acceso condicional](/azure/active-directory/conditional-access/overview/) de Azure AD a las aplicaciones desde la propia solución sin tener que ir al portal de Azure AD.

En el resto de esta guía se explican las consideraciones técnicas de la implementación de una solución y se dan recomendaciones para hacerlo.

## <a name="publishing-your-application-to-azure-marketplace"></a>Publicación de la aplicación en Azure Marketplace

La aplicación se puede integrar previamente en Azure AD para que admita SSO y el aprovisionamiento automatizado. Para ello, siga el proceso para [publicarla en Azure Marketplace](/azure/active-directory/develop/v2-howto-app-gallery-listing/). Azure Marketplace es un origen de confianza de aplicaciones para administradores de TI. Se ha comprobado que las aplicaciones que aparecen ahí son compatibles con Azure AD. Admiten SSO, el aprovisionamiento de usuarios automatizado y se pueden integrar fácilmente en los inquilinos de los clientes con el registro de aplicaciones automatizado.

Además, se recomienda convertirse en [editor comprobado](/azure/active-directory/develop/publisher-verification-overview/) para que los clientes sepan que es el editor de confianza de la aplicación.

## <a name="enabling-single-sign-on-for-it-admins"></a>Habilitación del inicio de sesión único para administradores de TI

[Elija OIDC o SAML](/azure/active-directory/manage-apps/sso-options#choosing-a-single-sign-on-method/) para habilitar el SSO para los administradores de TI en la solución. La mejor opción es usar OIDC. 

Microsoft Graph usa [OIDC/OAuth](/azure/active-directory/develop/v2-protocols-oidc/). Si la solución usa OIDC con Azure AD para el SSO de administrador de TI, los clientes van a tener una experiencia de un extremo a otro sin problemas. Van a usar OIDC para iniciar sesión en la solución, con lo que se puede emplear el mismo JSON Web Token (JWT) emitido por Azure AD para interactuar con Microsoft Graph.

Si, por el contrario, la solución usa [SAML](/azure/active-directory/manage-apps/configure-saml-single-sign-on/) para el SSO de administrador de TI, el token SAML no permite que la solución interactúe con Microsoft Graph. Puede seguir usando SAML para el SSO de administrador de TI, pero la solución tiene que admitir la integración de OIDC con Azure AD para poder obtener un JWT de Azure AD a fin de interactuar correctamente con Microsoft Graph. Puede usar uno de los siguientes métodos:

- **Enfoque de SAML recomendado**: cree un nuevo registro en Azure Marketplace, que es [una aplicación de OIDC](/azure/active-directory/saas-apps/openidoauth-tutorial/). De esta forma se proporciona al cliente la experiencia menos problemática. Por su parte, el cliente agrega las aplicaciones de SAML y OIDC a su inquilino. Si la aplicación no está actualmente en la galería de Azure AD, puede empezar con una [aplicación multiinquilino](/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant/) que no sea de la galería.

- **Enfoque de SAML alternativo**: el cliente puede [crear un registro de aplicación de OIDC](/azure/active-directory/saas-apps/openidoauth-tutorial/) de forma manual en su inquilino de Azure AD y asegurarse de establecer los identificadores URI, los puntos de conexión y los permisos que se especifican más adelante en este artículo.

Se recomienda usar el [tipo de concesión client_credentials](/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow#get-a-token/). Eso exige que la solución permita a cada cliente especificar un Id. de cliente y un secreto en la interfaz de usuario y que se almacene esa información. Obtenga un JWT de Azure AD y úselo para interactuar con Microsoft Graph.

Si elige esta ruta, debe tener documentación preparada para los clientes sobre cómo crear este registro de aplicación en el inquilino de Azure AD. Esta información incluye los puntos de conexión, los identificadores URI y los permisos necesarios.

> [!NOTE]
> Para poder usar cualquier aplicación para el SSO de administrador de TI o usuario, el administrador de TI del cliente debe [dar su consentimiento a la aplicación en su inquilino](/azure/active-directory/manage-apps/grant-admin-consent/).

## <a name="authentication-flows"></a>Flujos de autenticación

La solución incluye tres flujos de autenticación clave que admiten los siguientes escenarios:

- El administrador de TI del cliente inicia sesión con SSO para administrar la solución.

- El administrador de TI del cliente usa la solución para integrar aplicaciones con Azure AD a través de Microsoft Graph.

- Los usuarios inician sesión en aplicaciones heredadas protegidas por la solución y Azure AD.

### <a name="your-customers-it-administrator-does-single-sign-on-to-your-solution"></a>El administrador de TI del cliente realiza el inicio de sesión único en la solución

La solución puede usar SAML u OIDC para el SSO cuando el administrador de TI del cliente inicia sesión. En cualquier caso, se recomienda que el administrador de TI pueda iniciar sesión en la solución con sus credenciales de Azure AD. Eso logra una experiencia sin problemas y le permite usar los controles de seguridad existentes ya en vigor. La solución debe integrarse en Azure AD para un SSO mediante SAML u OIDC.

Esto es un diagrama y un resumen de este flujo de autenticación de usuarios:

![Diagrama que muestra cómo la solución redirige a un administrador de TI a Azure AD para iniciar sesión y luego cómo Azure AD lo vuelve a redirigir a la solución en un flujo de autenticación de usuario.](./media/secure-hybrid-access-integrations/admin-flow.png)

1. El administrador de TI quiere iniciar sesión en la solución con sus credenciales de Azure AD.

2. La solución redirige al administrador de TI a Azure AD con una solicitud de inicio de sesión de SAML u OIDC.

3. Azure AD autentica al administrador de TI y luego lo vuelve a enviar a la solución con un token SAML o un JWT para que se autorice en la solución.

### <a name="the-it-administrator-integrates-applications-with-azure-ad-by-using-your-solution"></a>El administrador de TI usa la solución para integrar aplicaciones en Azure AD

La segunda parte del recorrido del administrador de TI es integrar aplicaciones en Azure AD mediante la solución. Para ello, la solución usará Microsoft Graph para crear registros de aplicaciones y directivas de acceso condicional de Azure AD.

Esto es un diagrama y un resumen de este flujo de autenticación de usuarios:

![Diagrama de redireccionamientos y otras interacciones entre el administrador de TI, Azure Active Directory, la solución y Microsoft Graph en un flujo de autenticación de usuario.](./media/secure-hybrid-access-integrations/registration-flow.png)


1. El administrador de TI quiere iniciar sesión en la solución con sus credenciales de Azure AD.

2. La solución redirige al administrador de TI a Azure AD con una solicitud de inicio de sesión de SAML u OIDC.

3. Azure AD autentica al administrador de TI y luego lo vuelve a enviar a la solución con un token SAML o un JWT para su autorización en la solución.

4. Cuando el administrador de TI quiere integrar una de sus aplicaciones en Azure AD, en lugar de tener que ir al portal de Azure AD, la solución llama a Microsoft Graph con su JWT existente para registrar esas aplicaciones o aplicarles directivas de acceso condicional de Azure AD.

### <a name="users-sign-in-to-the-applications-secured-by-your-solution-and-azure-ad"></a>Los usuarios inician sesión en las aplicaciones protegidas por la solución y Azure AD

Si los usuarios necesitan iniciar sesión en aplicaciones individuales protegidas por la solución y Azure AD, pueden usar OIDC o SAML. Si las aplicaciones necesitan interactuar con Microsoft Graph o cualquier API protegida por Azure AD, se recomienda configurarlas para que usen OICD. Esta configuración garantiza que el JWT que las aplicaciones obtienen de Azure AD para autenticarse en las aplicaciones también se pueda aplicar para interactuar con Microsoft Graph. Si no es necesario que las aplicaciones individuales interactúen con Microsoft Graph ni con ninguna API protegida por Azure AD, basta con SAML.

Esto es un diagrama y un resumen de este flujo de autenticación de usuarios:

![Diagrama de redireccionamientos y otras interacciones entre el usuario, Azure Active Directory, la solución y la aplicación en un flujo de autenticación de usuario.](./media/secure-hybrid-access-integrations/end-user-flow.png)

1. El usuario quiere iniciar sesión en una aplicación protegida por la solución y Azure AD.
2. La solución redirige al usuario a Azure AD con una solicitud de inicio de sesión de SAML u OIDC.
3. Azure AD autentica al usuario y luego lo vuelve a enviar a la solución con un token SAML o un JWT para su autorización en la solución.
4. Tras la autorización, la solución permite que la solicitud original a la aplicación se realice mediante el protocolo preferido de la aplicación.

## <a name="summary-of-microsoft-graph-apis"></a>Resumen de Microsoft Graph API

La solución debe usar las siguientes API. Azure AD permite configurar permisos delegados o permisos de aplicación. En el caso de esta solución, solo se necesitan permisos delegados.

- [Application Templates API](/graph/application-saml-sso-configure-api#retrieve-the-gallery-application-template-identifier/): si está interesado en buscar en Azure Marketplace, puede usar esta API para encontrar una plantilla de aplicación que coincida. **Permiso necesario**: Application.Read.All.

- [Application Registration API](/graph/api/application-post-applications): esta API se usa para crear registros de aplicaciones de OIDC o SAML para que los usuarios puedan iniciar sesión en las aplicaciones que los clientes han protegido con la solución. Al hacerlo, estas aplicaciones también se pueden proteger con Azure AD. **Permisos necesarios**: Application.Read.All y Application.ReadWrite.All.

- [Service Principal API](/graph/api/serviceprincipal-update): después de registrar la aplicación, debe actualizar el objeto de entidad de servicio para establecer algunas propiedades de SSO. **Permisos necesarios**: Application.ReadWrite.All, Directory.AccessAsUser.All y AppRoleAssignment.ReadWrite.All (para la asignación).

- [Conditional Access API](/graph/api/resources/conditionalaccesspolicy): si quiere aplicar también directivas de acceso condicional de Azure AD a estas aplicaciones de usuario, puede usar esta API. **Permisos necesarios**: Policy.Read.All, Policy.ReadWrite.ConditionalAccess y Application.Read.All.

## <a name="example-graph-api-scenarios"></a>Escenarios de Graph API de ejemplo

En esta sección se proporciona un ejemplo de referencia para usar Microsoft Graph API para implementar registros de aplicaciones, conectar aplicaciones heredadas y habilitar directivas de acceso condicional mediante la solución. Además, se ofrecen instrucciones para automatizar el consentimiento del administrador, obtener el certificado de firma de tokens y asignar usuarios y grupos. Esta funcionalidad puede ser útil en la solución.

### <a name="use-the-graph-api-to-register-apps-with-azure-ad"></a>Use Graph API para registrar aplicaciones en Azure AD

#### <a name="add-apps-that-are-in-azure-marketplace"></a>Incorporación de aplicaciones que están en Azure Marketplace

Algunas de las aplicaciones que usa el cliente ya están disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps). Puede crear una solución que agregue estas aplicaciones mediante programación al inquilino del cliente. El siguiente código es un ejemplo del uso de Microsoft Graph API para buscar una plantilla coincidente en Azure Marketplace y luego registrar la aplicación en el inquilino de Azure AD del cliente.

Busque una aplicación coincidente en Azure Marketplace. Cuando se usa Application Templates API, el nombre para mostrar distingue mayúsculas de minúsculas.

```http
Authorization: Required with a valid Bearer token
Method: Get

https://graph.microsoft.com/v1.0/applicationTemplates?$filter=displayname eq "Salesforce.com"
```

Si se encuentra alguna coincidencia desde la llamada API anterior, capture el identificador y luego realice la siguiente llamada API con un nombre para mostrar descriptivo para la aplicación en el cuerpo de JSON:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/cd3ed3de-93ee-400b-8b19-b61ef44a0f29/instantiate
{
    "displayname": "Salesforce.com"
}
```

Al realizar la llamada API anterior, también se genera un objeto de entidad de servicio, lo que puede tardar unos segundos. Asegúrese de capturar el identificador de la aplicación y el identificador de la entidad de servicio. Los va a usar en las siguientes llamadas API.

Después revise el objeto de entidad de servicio con el protocolo SAML y la dirección URL de inicio de sesión adecuada:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.salesforce.com"
}
```

Por último, revise el objeto de aplicación con los identificadores URI de redireccionamiento adecuados y los URI de identificador:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.salesforce.com"]},
    "identifierUris":["https://www.salesforce.com"]
}
```

#### <a name="add-apps-that-are-not-in-azure-marketplace"></a>Incorporación de aplicaciones que no están en Azure Marketplace

Si no encuentra ninguna coincidencia en Azure Marketplace o solo quiere integrar una aplicación personalizada, puede registrar una aplicación personalizada en Azure AD mediante este identificador de plantilla: **8adf8e6e-67b2-4cf2-a259-e3dc5476c621**. Después realice la siguiente llamada API con un nombre para mostrar descriptivo de la aplicación en el cuerpo de JSON:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

Al realizar la llamada API anterior, también se genera un objeto de entidad de servicio, lo que puede tardar unos segundos. Asegúrese de capturar el identificador de la aplicación y el identificador de la entidad de servicio. Los va a usar en las siguientes llamadas API.

Después revise el objeto de entidad de servicio con el protocolo SAML y la dirección URL de inicio de sesión adecuada:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

Por último, revise el objeto de aplicación con los identificadores URI de redireccionamiento adecuados y los URI de identificador:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="cut-over-to-azure-ad-single-sign-on"></a>Migración total al inicio de sesión único de Azure AD

Una vez que haya registrado las aplicaciones SaaS en Azure AD, aún las debe migrar totalmente para empezar a usar Azure AD como su proveedor de identidades. Existen dos modos para hacer esto:

- Si las aplicaciones admiten SSO con un clic, Azure AD puede migrarlas totalmente por el cliente. Este solo tiene que ir al portal de Azure AD y realizar el SSO con un clic con las credenciales administrativas de las aplicaciones SaaS admitidas. Para obtener más información, vea [Configuración del inicio de sesión único mediante un solo clic de una aplicación](/azure/active-directory/manage-apps/one-click-sso-tutorial/).
- Si las aplicaciones no admiten el SSO con un clic, el cliente debe migrarlas totalmente de forma manual para empezar a usar Azure AD. Para obtener más información, vea [Tutoriales para integrar aplicaciones SaaS con Azure Active Directory](/azure/active-directory/saas-apps/tutorial-list/).

### <a name="connect-apps-by-using-legacy-authentication-methods-to-azure-ad"></a>Conexión de aplicaciones a Azure AD mediante métodos de autenticación heredados

Aquí es donde se puede colocar la solución entre Azure AD y la aplicación para permitir que el cliente obtenga las ventajas del SSO y otras características de Azure Active Directory, incluso en aplicaciones que no se admiten. Para ello, la aplicación llama a Azure AD para autenticar al usuario y aplicar directivas de acceso condicional de Azure AD antes de que el usuario pueda acceder a estas aplicaciones con protocolos heredados.

Puede permitir que los clientes realicen esta integración directamente desde la consola de modo que la detección y la integración constituyan una experiencia de un extremo a otro sin problemas. Esto implica que la plataforma cree un registro de aplicación de SAML u OIDC entre la plataforma y Azure AD.

#### <a name="create-a-saml-application-registration"></a>Creación de un registro de aplicación SAML

Para crear un registro de aplicación de SAML, use este identificador de plantilla de aplicación personalizado para una aplicación personalizada: **8adf8e6e-67b2-4cf2-a259-e3dc5476c621**. Después realice la siguiente llamada API con un nombre para mostrar descriptivo en el cuerpo de JSON:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

Al realizar la llamada API anterior, también se genera un objeto de entidad de servicio, lo que puede tardar unos segundos. Asegúrese de capturar el identificador de la aplicación y el identificador de la entidad de servicio. Los va a usar en las siguientes llamadas API.

Después revise el objeto de entidad de servicio con el protocolo SAML y la dirección URL de inicio de sesión adecuada:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

Por último, revise el objeto de aplicación con los identificadores URI de redireccionamiento adecuados y los URI de identificador:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="create-an-oidc-application-registration"></a>Creación de un registro de aplicación OIDC

Para crear un registro de aplicación de OIDC, use este identificador de plantilla para una aplicación personalizada: **8adf8e6e-67b2-4cf2-a259-e3dc5476c621**. Después realice la siguiente llamada API con un nombre para mostrar descriptivo en el cuerpo de JSON:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom OIDC App"
}
```

En la llamada API, capture el identificador de la aplicación y el identificador de la entidad de servicio. Los va a usar en las siguientes llamadas API.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/{Application Object ID}
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["[https://www.samlapp.com"],
    "requiredResourceAccess": [
    {
        "resourceAppId": "00000003-0000-0000-c000-000000000000",
        "resourceAccess": [
        {
            "id": "7427e0e9-2fba-42fe-b0c0-848c9e6a8182",
            "type": "Scope"
        },
        {
            "id": "e1fe6dd8-ba31-4d61-89e7-88639da4683d",
            "type": "Scope"
        },
        {
            "id": "37f7f235-527c-4136-accd-4a02d197296e",
            "type": "Scope"
        }]
    }]
}
```

> [!NOTE]
> Los permisos de API indicados en el nodo `resourceAccess` conceden a la aplicación los permisos *openid*, *User.Read* y *offline_access*, que deberían bastar para que el usuario inicie sesión en la solución. Para obtener más información sobre los permisos, vea la [referencia sobre permisos de Microsoft Graph](/graph/permissions-reference/).

### <a name="apply-conditional-access-policies"></a>Aplicación de directivas de acceso condicional

Los clientes y asociados también pueden usar Microsoft Graph API para crear o aplicar directivas de acceso condicional en aplicaciones de clientes. En el caso de los asociados, esto puede proporcionar valor adicional, porque los clientes pueden aplicar estas directivas directamente desde la solución sin tener que ir al portal de Azure AD. 

Al aplicar directivas de acceso condicional de Azure AD, tiene dos opciones:

- Asignar la aplicación a una directiva de acceso condicional existente.
- Crear una nueva directiva de acceso condicional y asignar la aplicación a esa nueva directiva.

#### <a name="use-an-existing-conditional-access-policy"></a>Uso de una directiva de acceso condicional existente

En primer lugar, ejecute la consulta siguiente para obtener una lista de todas las directivas de acceso condicional. Obtenga el identificador de objeto de la directiva que quiere modificar.

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies
```

Luego revise la directiva; para ello, incluya el identificador de objeto de la aplicación en el ámbito de `includeApplications` en el cuerpo de JSON:

```https
Authorization: Required with a valid Bearer token
Method: PATCH

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/{policyid}
{
    "displayName":"Existing CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": 
        {
            "includeApplications":[
                "00000003-0000-0ff1-ce00-000000000000", 
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": 
    {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

#### <a name="create-a-new-conditional-access-policy"></a>Creación de una directiva de acceso condicional

Agregue el identificador de objeto de la aplicación en el ámbito de `includeApplications` en el cuerpo de JSON:

```https
Authorization: Required with a valid Bearer token
Method: POST

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/
{
    "displayName":"New CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

Si está interesado en crear nuevas directivas de acceso condicional de Azure AD, estas son algunas plantillas adicionales que pueden ayudarle a empezar a usar [Conditional Access API](/azure/active-directory/conditional-access/howto-conditional-access-apis/):

```https
#Policy Template for Requiring Compliant Device

{
    "displayName":"Enforce Compliant Device",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "compliantDevice",
            "domainJoinedDevice"
        ]
    }
}

#Policy Template for Block

{
    "displayName":"Block",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "block"
        ]
    }
}
```

### <a name="automate-admin-consent"></a>Automatización del consentimiento del administrador

Si el cliente está incorporando muchas aplicaciones de la plataforma a Azure AD, puede automatizar el consentimiento del administrador para no tener que hacerlo de forma manual para multitud de aplicaciones. También puede realizar esta automatización mediante Microsoft Graph. Necesita tanto el identificador de objeto de entidad de servicio de la aplicación que ha creado en las llamadas API anteriores como el identificador de objeto de entidad de servicio de Microsoft Graph del inquilino del cliente.

Para obtener el identificador de objeto de entidad de servicio de Microsoft Graph, realice esta llamada API:

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/serviceprincipals/?$filter=appid eq '00000003-0000-0000-c000-000000000000'&$select=id,appDisplayName
```

Cuando esté listo para automatizar el consentimiento del administrador, realice esta llamada API:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/oauth2PermissionGrants
{
    "clientId":"{Service Principal Object ID of Application}",
    "consentType":"AllPrincipals",
    "principalId":null,
    "resourceId":"{Service Principal Object ID Of Microsoft Graph}",
    "scope":"openid user.read offline_access}"
}
```

### <a name="get-the-token-signing-certificate"></a>Obtención del certificado de firma de tokens

Para obtener la parte pública del certificado de firma de tokens de todas estas aplicaciones, use `GET` desde el punto de conexión de metadatos de Azure AD de la aplicación:

```https
Method:GET

https://login.microsoftonline.com/{Tenant_ID}/federationmetadata/2007-06/federationmetadata.xml?appid={Application_ID}
```

### <a name="assign-users-and-groups"></a>Asignación de usuarios y grupos

Una vez que haya publicado la aplicación en Azure AD, puede asignarla opcionalmente a usuarios y grupos para asegurarse de que se muestre en el portal [MyApplications](/azure/active-directory/user-help/my-applications-portal-workspaces/). Esta asignación se almacena en el objeto de entidad de servicio generado al crear la aplicación.

En primer lugar, obtenga cualquier instancia de `AppRole` que la aplicación pueda tener asociada. Es habitual que las aplicaciones SaaS tengan varias instancias de `AppRole` asociadas. En el caso de las aplicaciones personalizadas, normalmente solo hay una instancia de `AppRole` predeterminada. Obtenga el identificador de la instancia de `AppRole` que quiere asignar:

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
```

Luego obtenga el identificador de objeto del usuario o grupo de Azure AD que quiere asignar a la aplicación. Además, tome el identificador de rol de aplicación de la llamada API anterior y envíelo como parte del cuerpo de PATCH en la entidad de servicio:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "principalId":"{Principal Object ID of User -or- Group}",
    "resourceId":"{Service Principal Object ID}",
    "appRoleId":"{App Role ID}"
}
```

## <a name="partnerships"></a>Colaboraciones

Microsoft tiene asociaciones con estos proveedores de controladores de entrega de aplicaciones (ADC) para ayudar a proteger las aplicaciones heredadas mientras se usan controladores de red y entrega existentes.

| **Proveedor de ADC** | **Vínculo** |
| --- | --- |
| Akamai Enterprise Application Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial](/azure/active-directory/saas-apps/akamai-tutorial) |
| Citrix ADC | [https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial](/azure/active-directory/saas-apps/citrix-netscaler-tutorial) |
| F5 Big-IP Access Policy Manager | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration](/azure/active-directory/manage-apps/f5-aad-integration) |
| Kemp LoadMaster | [https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial](/azure/active-directory/saas-apps/kemp-tutorial) |
| Pulse Secure Virtual Traffic Manager | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial](/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial) |

Los siguientes proveedores de soluciones VPN se conectan con Azure AD para habilitar métodos de autenticación y autorización modernos, como SSO y la autenticación multifactor.

| **Proveedor de VPN** | **Vínculo** |
| --- | --- |
| Cisco AnyConnect | [https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect](/azure/active-directory/saas-apps/cisco-anyconnect) |
| Fortinet FortiGate | [https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial](/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial) |
| F5 Big-IP Access Policy Manager | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn](/azure/active-directory/manage-apps/f5-aad-password-less-vpn) |
| Palo Alto Networks GlobalProtect | [https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial](/azure/active-directory/saas-apps/paloaltoadmin-tutorial) |
| Pulse Connect Secure | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial](/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial) |

Los siguientes proveedores de soluciones de perímetro definido por software (SDP) se conectan con Azure AD para habilitar métodos de autenticación y autorización modernos, como SSO y la autenticación multifactor.

| **Proveedor de SDP** | **Vínculo** |
| --- | --- |
| Datawiza Access Broker | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/datawiza-with-azure-ad](/azure/active-directory/manage-apps/datawiza-with-azure-ad) |
| Perimeter 81 | [https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial](/azure/active-directory/saas-apps/perimeter-81-tutorial) |
| Silverfort Authentication Platform | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/silverfort-azure-ad-integration](/azure/active-directory/manage-apps/silverfort-azure-ad-integration) |
| Strata Maverics Identity Orchestrator | [https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial](/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial) |
| Zscaler Private Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial](/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial) |
