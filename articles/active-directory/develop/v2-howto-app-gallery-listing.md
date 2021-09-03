---
title: Publicación de la aplicación en la galería de aplicaciones de Azure Active Directory
description: Obtenga información sobre cómo mostrar una aplicación compatible con el inicio de sesión único en la galería de aplicaciones de Azure Active Directory. La publicación en la galería de aplicaciones facilita a los clientes la búsqueda y adición de la aplicación a su inquilino.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/23/2021
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev, contperf-fy21q4
ms.openlocfilehash: 7938e8ffbaca3f069016a445775d9c669f1ad144
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228074"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Publicación de la aplicación en la galería de aplicaciones de Azure AD

Puede publicar la aplicación en la galería de aplicaciones de Azure Active Directory (Azure AD). Una vez que se haya publicado la aplicación, se mostrará como una opción para los clientes cuando [agreguen aplicaciones a su inquilino](../manage-apps/add-application-portal.md). 

Los pasos para publicar la aplicación en la galería de aplicaciones de Azure AD son:
1. Requisitos previos
1. Elija el estándar de inicio de sesión único adecuado para su aplicación.
1. Implemente el inicio de sesión único en la aplicación.
1. Implementación del aprovisionamiento de usuarios de SCIM en la aplicación (opcional)
1. Cree el inquilino de Azure y pruebe la aplicación.
1. Cree y publique documentación.
1. Envíe la aplicación.
1. Únase a Microsoft Partner Network.

## <a name="what-is-the-azure-ad-application-gallery"></a>¿Qué es la galería de aplicaciones de Azure AD?

La [galería de aplicaciones de Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) es un catálogo de miles de aplicaciones que facilita la implementación y configuración del inicio de sesión único (SSO) y el aprovisionamiento automatizado de usuarios.

Algunas de las ventajas de agregar la aplicación a la galería de Azure AD incluyen:

- Los clientes pueden disfrutar de la mejor experiencia posible de inicio de sesión único en la aplicación.
- La configuración de la aplicación resulta sencilla y apenas requiere esfuerzo.
- Basta realizar una búsqueda rápida para encontrar la aplicación en la galería.
- Todos los clientes de Azure AD de los niveles Gratis, Básico y Premium pueden disfrutar de esta integración.
- Los clientes mutuos pueden utilizar un tutorial de configuración paso a paso.
- Los clientes que usan el sistema para la administración de identidades entre dominios ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) pueden usar el aprovisionamiento para la misma aplicación.

Además, hay muchas ventajas cuando los clientes usan Azure AD como proveedor de identidades para la aplicación. Algunas son:

- Proporcionar inicio de sesión único para los usuarios. Con el inicio de sesión único, puede reducir los costos de soporte técnico poniéndoselo más fácil a los clientes. Si está habilitado el inicio de sesión único con un solo clic, los administradores de TI de los clientes no tendrán que aprender a configurar la aplicación para su uso en su organización. Para más información sobre el inicio de sesión único, vea [¿Qué es el inicio de sesión único?](../manage-apps/what-is-single-sign-on.md)
- Se puede detectar la aplicación en la galería de aplicaciones de Microsoft 365, el iniciador de aplicaciones de Microsoft 365 y en Microsoft Search en Office.com. 
- Administración integrada de aplicaciones. Para más información sobre la administración de aplicaciones en Azure AD, vea [¿Qué es la administración de aplicaciones?](../manage-apps/what-is-application-management.md)
- La aplicación puede usar [Graph API](/graph/) para acceder a los datos que impulsan la productividad de los usuarios en el ecosistema de Microsoft.
- La documentación específica de la aplicación coproducida con el equipo de Azure AD para los clientes mutuos facilita la adopción.
- Puede proporcionar a los clientes la posibilidad de administrar por completo la autenticación y autorización de las identidades de sus empleados e invitados.
- Colocando toda la responsabilidad de administración de la cuenta y del cumplimiento normativo en el cliente propietario de esas identidades.
- Proporcionando la capacidad de habilitar o deshabilitar el inicio de sesión único para proveedores de identidades específicos, grupos o usuarios satisfagan sus necesidades empresariales.
- Puede aumentar la capacidad de comercialización y adopción. Muchas organizaciones grandes requieren (o aspiran a ello) que sus empleados tengan experiencias de inicio de sesión único sin problemas en todas las aplicaciones. Facilitar el inicio de sesión único es muy importante.
- Puede reducir los problemas del usuario final a la hora de utilizarla, lo cual podría aumentar el uso por parte del usuario final e incrementar los ingresos.
- Los clientes que usan el sistema para la administración de identidades entre dominios ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) pueden usar el aprovisionamiento para la misma aplicación.
- Agregue seguridad y comodidad cuando los usuarios inicien sesión en las aplicaciones con el inicio de sesión único de Azure AD sin necesidad de usar credenciales independientes.

> [!TIP]
> Cuando ofrece la aplicación para que la utilicen otras empresas mediante su compra o suscripción, hace que la aplicación esté disponible para los clientes dentro de sus propios inquilinos de Azure. Esto se conoce como creación de una aplicación multiinquilino. Para obtener información general sobre este concepto, vea [Inquilinos in Azure Active Directory](single-and-multi-tenant-apps.md).

## <a name="prerequisites"></a>Requisitos previos
Para publicar la aplicación en la galería de Azure AD, primero debe leer y aceptar [términos y condiciones](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/) concretos.

Necesita una cuenta permanente para las pruebas con al menos dos usuarios registrados.

- Para las aplicaciones federadas (Open ID y SAML/WS-Fed), la aplicación debe admitir el modelo de software como servicio (SaaS) para que se incluya en la galería de Azure AD. Las aplicaciones de la galería empresarial tienen que admitir varias configuraciones de cliente y no la de un cliente específico.
- Para OpenID Connect, la aplicación debe ser de varios inquilinos y para ella se debe implementar correctamente el [marco de consentimiento de Azure AD](../develop/consent-framework.md). El usuario puede enviar la solicitud de inicio de sesión a un punto de conexión común para que los clientes puedas proporcionar su consentimiento a la aplicación. Puede controlar el acceso de usuario en función del identificador del inquilino y el UPN del usuario que se recibieron en el token.
- Para SAML 2.0 o WS-Fed, la aplicación debe ser capaz de realizar la integración de SSO de SAML o WS-Fed en modo SP o IDP. Asegúrese de que esta funcionalidad funciona correctamente antes de enviar la solicitud.
- Para el SSO con contraseña, asegúrese de que la aplicación admita la autenticación por formulario para que se pueda realizar el almacenamiento de contraseña y hacer que el inicio de sesión único funcione del modo previsto.
- Necesita una cuenta permanente para las pruebas con al menos dos usuarios registrados.

Puede obtener una cuenta de prueba gratuita con todas las características de Azure AD premium gratis durante 90 días. Este período puede ampliarse siempre que realice labores de desarrollo con dicha cuenta: [Únase al programa de desarrolladores de Microsoft 365](/office/developer-program/microsoft-365-developer-program).

## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>Paso 1: Elija el estándar de inicio de sesión único adecuado para su aplicación

Para mostrar una aplicación en la galería de aplicaciones de Azure AD, implemente al menos una de las opciones admitidas de inicio de sesión único. Para comprender las opciones de inicio de sesión único y cómo las configurarán los clientes en Azure AD, vea [Opciones de inicio de sesión único](../manage-apps/sso-options.md).

En la tabla siguiente se comparan los estándares principales: Open Authentication 2.0 (OAuth 2.0) con OpenID Connect (OIDC), Lenguaje de marcado de aserción de seguridad (SAML) y Web Services Federation (WS-Fed).

| Capacidad| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| Inicio de sesión único basado en Web| √| √ |
| Cierre de sesión único basado en Web| √| √ |
| Inicio de sesión único basado en dispositivos móviles| √| √* |
| Cierre de sesión único basado en dispositivos móviles| √| √* |
| Directivas de acceso condicional para aplicaciones para dispositivos móviles| √| √* |
| Experiencia MFA completa para aplicaciones para dispositivos móviles| √| √* |
| Aprovisionamiento de SCIM| √| √ |
| Acceso a Microsoft Graph| √| X |

*Es posible, pero Microsoft no proporciona ejemplos ni guía.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 y OpenID Connect
OAuth 2.0 es un protocolo [estándar de la industria](https://oauth.net/2/) para la autorización. OpenID Connect (OIDC) es un nivel de autenticación de identidad [estándar del sector](https://openid.net/connect/) creado a partir del protocolo OAuth 2.0. 

**Motivos para elegir OAuth/OIDC**
- La autorización inherente a estos protocolos permite a cualquier aplicación no solo acceder a un gran número de datos de los usuarios y de la organización sino también integrarse en ellos mediante Microsoft Graph API.
- Simplifica la experiencia de usuario final de sus clientes al adoptar el inicio de sesión único para su aplicación. Puede definir fácilmente los conjuntos de permisos necesarios, que luego se representan automáticamente para que el administrador o el usuario final den su consentimiento.
- El uso de estos protocolos permite a los clientes usar directivas de Multi-Factor Authentication (MFA) y de acceso condicional para controlar el acceso a las aplicaciones. 
- Microsoft proporciona bibliotecas y [ejemplos de código en varias plataformas tecnológicas](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para facilitar el desarrollo.  

**Varios aspectos que se deben tener en cuenta**
- Si ya ha implementado el inicio de sesión único basado en SAML para la aplicación, es posible que no desee implementar un nuevo estándar para obtener la aplicación en la galería.

### <a name="saml-20-or-ws-fed"></a>SAML 2.0 o WS-Fed

SAML es un [estándar de inicio de sesión único](https://www.oasis-open.org/standards#samlv2.0), consolidado y ampliamente adoptado para aplicaciones web. Para más información sobre cómo Azure usa SAML, vea [Uso del protocolo SAML por parte de Azure](active-directory-saml-protocol-reference.md). 

Web Services Federation (WS-Fed) es un [estándar del sector](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) que normalmente se usa para las aplicaciones web que se desarrollan mediante la plataforma .NET.

**Motivos para elegir SAML**
- SAML 2.0 es un estándar maduro y la mayoría de las plataformas tecnológicas admiten las bibliotecas de código abierto para SAML 2.0. 
- Puede proporcionar a los clientes una interfaz de administración para configurar el inicio de sesión único de SAML. Puede configurar el inicio de sesión único de SAML para Microsoft Azure AD y cualquier otro proveedor de identidades que sea compatible con SAML.

**Varios aspectos que se deben tener en cuenta**
- Al usar los protocolos SAML 2.0 o WSFed para aplicaciones para dispositivos móviles, determinadas directivas de acceso condicional, incluida la autenticación multifactor (MFA), tendrán una experiencia que se verá limitada.
- Si desea acceder a Microsoft Graph, será preciso que implemente la autorización mediante OAuth 2.0 para generar los tokens necesarios. 

### <a name="password-based"></a>Basado en contraseñas
El SSO basado en contraseña, también conocido como almacenamiento de contraseñas, permite administrar el acceso y las contraseñas de los usuarios en aplicaciones web que no admiten la federación de identidades. También es útil para escenarios en los que varios usuarios deben compartir la misma cuenta, como las cuentas de las aplicaciones de redes sociales de la organización.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>Paso 2: Implemente el inicio de sesión único en la aplicación
Cada aplicación de la galería debe implementar una de las opciones de inicio de sesión único compatibles. Para más información sobre las opciones compatibles, vea [Opciones de inicio de sesión único](../manage-apps/sso-options.md).

Para OAuth y OIDC, vea [Guía sobre los patrones de autenticación](v2-app-types.md) y [Ejemplos de código de Azure Active Directory](sample-v2-code.md).

Para SAML y WS-Fed, la aplicación debe ser capaz de realizar la integración de SSO en modo SP o IDP. Asegúrese de que esta funcionalidad funciona correctamente antes de enviar la solicitud.

Para más información sobre la autenticación, vea [¿Qué es la autenticación?](authentication-vs-authorization.md)

> [!IMPORTANT]
> En el caso de las aplicaciones federadas (OpenID y SAML/WS-Fed), la aplicación debe admitir el modelo de software como servicio (SaaS). Las aplicaciones de la galería de Azure AD tienen que admitir varias configuraciones de cliente y no deben especificarse para un único cliente.

### <a name="implement-oauth-20-and-openid-connect"></a>Implementación de OAuth 2.0 y OpenID Connect

Para OpenID Connect, la aplicación debe ser multiinquilino y para ella se debe implementar correctamente el [marco de consentimiento de Azure AD](consent-framework.md). El usuario puede enviar la solicitud de inicio de sesión a un punto de conexión común para que los clientes puedas proporcionar su consentimiento a la aplicación. Puede controlar el acceso de usuario en función del identificador del inquilino y el UPN del usuario que se recibieron en el token.

Para revisar ejemplos específicos, vea [Ejemplos de código de la Plataforma de identidad de Microsoft](sample-v2-code.md). 

Para consultar ejemplos específicos de dispositivos móviles, vea: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Plataforma universal de Windows](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>Implementación de SAML 2.0

Si la aplicación es compatible con SAML 2.0, puede integrarlo directamente con un inquilino de Azure AD. Para más información sobre la configuración de SAML con Azure AD, vea [Configuración del inicio de sesión único basado en SAML](../manage-apps/configure-saml-single-sign-on.md).

Microsoft no proporciona ni recomienda bibliotecas para las implementaciones de SAML. Hay muchas bibliotecas de código abierto disponibles.

### <a name="implement-ws-fed"></a>Implementación de WS-Fed
Para más información sobre WS-Fed en ASP.NET Core, vea [Autenticación de usuarios con WS-Federation en ASP.NET Core](/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>Implementación del almacenamiento de contraseñas

Cree una aplicación web que tenga una página de inicio de sesión HTML. Asegúrese de que la aplicación admita la autenticación por formulario para que se pueda realizar el almacenamiento de contraseñas y hacer que el inicio de sesión único funcione según lo previsto.

## <a name="step-3---implement-scim-user-provisioning-in-your-app"></a>Paso 3: Implemente el aprovisionamiento de usuarios de SCIM en la aplicación
La compatibilidad con el aprovisionamiento usuarios de [SCIM](https://aka.ms/scimoverview) es un paso opcional, pero muy recomendable, para crear la aplicación. Proporcionar compatibilidad con el estándar SCIM es sencillo y permite a los clientes crear y actualizar automáticamente las cuentas de usuario en la aplicación, sin necesidad de procesos manuales como cargar archivos CSV. Además, los clientes pueden automatizar la eliminación de usuarios y mantener sincronizada la pertenencia a grupos, algo que no es posible con una solución como SAML JIT. 

### <a name="learn-about-scim"></a>Más información sobre SCIM
Para obtener más información sobre los estándares y las ventajas de SCIM para los clientes, consulte una [introducción al aprovisionamiento con SCIM](https://aka.ms/scimoverview).

### <a name="understand-the-azure-ad-scim-implementation"></a>Información sobre la implementación de SCIM de Azure AD
Para obtener más información acerca de la implementación de SCIM de Azure AD, consulte cómo [crear un punto de conexión de SCIM y configurar el aprovisionamiento de usuarios con Azure AD](../app-provisioning/use-scim-to-provision-users-and-groups.md).

### <a name="implement-scim"></a>Implementación de SCIM
Azure AD proporciona un [código de referencia](https://aka.ms/scimoverview) para ayudarle a crear un punto de conexión de SCIM. También hay muchas bibliotecas o referencias de terceros disponibles en GitHub.  

## <a name="step-4---create-your-azure-tenant-and-test-your-app"></a>Paso 4: Cree el inquilino de Azure y pruebe la aplicación

Necesitará un inquilino de Azure AD para probar la aplicación. Para configurar el entorno de desarrollo, vea [Inicio rápido: Configuración de un inquilino](quickstart-create-new-tenant.md).

Como alternativa, cada suscripción a Microsoft 365 incluye un inquilino de Azure AD. Para configurar un entorno de desarrollo de Microsoft 365 gratuito, vea [Unirse al programa de desarrolladores de Microsoft 365](/office/developer-program/microsoft-365-developer-program).

Una vez que tenga un inquilino, pruebe el inicio de sesión único y el [aprovisionamiento](../app-provisioning/use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client). 

**En aplicaciones de OIDC u Oath**, [registre la aplicación](quickstart-register-app.md) como una aplicación multiinquilino. ‎Seleccione la opción Cuentas en cualquier directorio organizativo y cuentas Microsoft personales en Tipos de cuenta admitidos.

**En aplicaciones basadas en SAML y WS-Fed**, [configure el inicio de sesión único basado en SAML](../manage-apps/configure-saml-single-sign-on.md) mediante una plantilla genérica de SAML en Azure AD.

También puede [convertir una aplicación de un solo inquilino en multiinquilino](howto-convert-app-to-be-multi-tenant.md) si fuera necesario.


## <a name="step-5---create-and-publish-documentation"></a>Paso 5: Cree y publique la documentación

### <a name="documentation-on-your-site"></a>Documentación sobre el sitio

La facilidad de adopción es un factor importante para las decisiones relacionadas con el software empresarial. Una documentación clara y fácil de seguir ayuda a los clientes en su proceso de adopción y reduce los costos de soporte técnico. El trabajo con miles de proveedores de software ha permitido a Microsoft observar lo que funciona.

Se recomienda que la documentación sobre el sitio como mínimo incluya los siguientes elementos.

* Introducción a la funcionalidad de inicio de sesión único
  * Protocolos admitidos
  * SKU y versión
  * Lista de proveedores de identidades admitidos con vínculos de documentación
* Información de licencia de la aplicación
* Control de acceso basado en rol para configurar el inicio de sesión único
* Pasos de configuración de inicio de sesión único
  * Elementos de configuración de la interfaz de usuario para SAML con los valores esperados del proveedor
  * Información del proveedor de servicio que se va a pasar a los proveedores de identidades
* Si OIDC/OAuth
  * Lista de permisos necesarios para el consentimiento con justificaciones comerciales
* Pasos de prueba para usuarios piloto
* Información de solución de problemas, incluidos mensajes y códigos de error
* Mecanismos de soporte técnico para clientes
* Detalles sobre el punto de conexión de SCIM, incluidos los recursos y atributos admitidos

### <a name="documentation-on-the-microsoft-site"></a>Documentación sobre el sitio de Microsoft

Al publicar la aplicación con la galería de aplicaciones de Azure Active Directory, que también publica la aplicación en Azure Marketplace, Microsoft genera documentación que explica el proceso paso a paso para los clientes mutuos. Puede ver un ejemplo [aquí](../saas-apps/tutorial-list.md). Esta documentación se crea en función del envío a la galería y se puede actualizar fácilmente si se realizan cambios en la aplicación con la cuenta de GitHub.


## <a name="step-6---submit-your-app"></a>Paso 6: Envíe la aplicación

Cuando haya comprobado que la integración de aplicaciones funciona con Azure AD, envíe la solicitud de aplicación en el [portal de redes de aplicaciones de Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

La primera vez que intente iniciar sesión en el portal, se mostrará una de las dos pantallas. 

Si recibe el mensaje "Eso no funcionó", deberá ponerse en contacto con el [equipo de integración del SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Proporcione la cuenta de correo electrónico que desea usar para enviar la solicitud. Se prefiere una dirección de correo electrónico empresarial, como `name@yourbusiness.com`. A continuación, el equipo de Azure AD agregará la cuenta en el portal de redes de aplicaciones de Microsoft.

Si aparece una página "Solicitar acceso", rellene la justificación comercial y seleccione **Solicitar acceso**.

Una vez agregada la cuenta, puede iniciar sesión en el portal de redes de aplicaciones de Microsoft y enviar la solicitud; para ello, seleccione el icono **Enviar solicitud (ISV)** en la página principal.

![Icono Enviar solicitud (ISV) en la página principal](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Problemas al iniciar sesión en el portal

Si ve este error al iniciar sesión, estos son los detalles sobre el problema y así es como puede corregirlo.

* Si el inicio de sesión se ha bloqueado como se muestra a continuación:

  ![aplicación de resolución de problemas de la galería](./media/howto-app-gallery-listing/blocked.png)

**Qué ocurre:**

El usuario invitado está federado en un inquilino inicial que también es de Azure AD. El riesgo que afronta el usuario invitado es alto. Microsoft no permite que los usuarios de alto riesgo tengan acceso a sus recursos. Todos los usuarios de alto riesgo (empleados o invitados/proveedores) deben poner solución a su riesgo o acabar con este para tener acceso a los recursos de Microsoft. Para los usuarios invitados, este riesgo de usuario procede del inquilino inicial y la directiva procede del inquilino de los recursos (Microsoft en este caso).
 
**Soluciones seguras:**

* Los usuarios invitados registrados para MFA ponen solución a su propio riesgo de usuario. Esto lo puede hacer el usuario invitado cambiando o restableciendo la contraseña segura (https://aka.ms/sspr) en su inquilino inicial (esto requiere MFA y SSPR en el inquilino inicial). El cambio o restablecimiento de la contraseña segura debe iniciarse en Azure AD y no en el entorno local.

* Los usuarios invitados hacen que sus administradores pongan solución a su riesgo. En este caso, el administrador restablecerá la contraseña (generación de contraseñas temporal). Esto no requiere Identity Protection. El administrador del usuario invitado puede ir a https://aka.ms/RiskyUsers y hacer clic en "Restablecer contraseña".

* Los usuarios invitados hacen que sus administradores acaben con su riesgo o lo descarten. Una vez más, esto no requiere Identity Protection. El administrador puede ir a https://aka.ms/RiskyUsers y hacer clic en "Descartar el riesgo del usuario". Sin embargo, el administrador debe llevar a cabo la debida diligencia para asegurarse de que se trataba de una evaluación de riesgos de falsos positivos antes de acabar con el riesgo de usuario. De lo contrario, estarán poniendo en riesgo sus recursos y los de Microsoft mediante la supresión de una evaluación de riesgos sin investigación.

> [!NOTE]
> Si tiene algún problema con el acceso, póngase en contacto con el [equipo de integración del SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="implementation-specific-options"></a>Opciones específicas de la implementación
Si desea agregar la aplicación a la lista en la galería con OpenID Connect, seleccione **OpenID Connect & OAuth 2.0** (OAuth 2.0 y OpenID Connect) como se muestra.

![Adición de una aplicación de OpenID Connect a la lista de la galería](./media/howto-app-gallery-listing/openid.png)

Si desea agregar la aplicación a la lista en la galería mediante **SAML 2.0** o **WS-Fed**, seleccione **SAML 2.0/WS-Fed** como se muestra.

![Adición de una aplicación de SAML 2.0 o WS-Fed a la lista de la galería](./media/howto-app-gallery-listing/saml.png)

Si quiere agregar la aplicación a la lista de la galería mediante SSO con contraseña, seleccione **Password SSO** (SSO con contraseña) (nombre de usuario y contraseña) como se muestra.

![Escala de tiempo para agregar la aplicación de SSO con contraseña a la lista de galería](./media/howto-app-gallery-listing/passwordsso.png)

Si va a implementar un punto de conexión de [SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 2.0 para el aprovisionamiento de usuarios, seleccione la opción como se muestra. Al proporcionar el esquema en la solicitud de incorporación, siga las instrucciones facilitadas [en esta página](../app-provisioning/export-import-provisioning-configuration.md) para descargar el esquema. Usaremos el esquema que configuró al probar la aplicación que no pertenece a la galería para crear la aplicación de galería. 

   ![Solicitud de aprovisionamiento de usuarios](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Actualización o eliminación de un listado existente

Puede actualizar o quitar una aplicación existente en la galería en el [portal de redes de aplicaciones de Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

![Listado de una aplicación SAML en la galería](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Si tiene algún problema con el acceso, revise la sección anterior sobre cómo crear una cuenta. Si esto no funciona, póngase en contacto con el [equipo de integración del SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="timelines"></a>Escalas de tiempo

La escala de tiempo para el proceso del listado de una aplicación de SAML 2.0 o WS-Fed en la galería es de entre 7 y 10 días laborables.

![Escala de tiempo para agregar una aplicación SAML a la lista de la galería](./media/howto-app-gallery-listing/timeline.png)

La escala de tiempo que dura el proceso para mostrar una aplicación de OpenID Connect en la lista de la galería es de entre 2 y 5 días laborables.

![Escala de tiempo para agregar la aplicación de OpenID Connect a la lista de la galería](./media/howto-app-gallery-listing/timeline2.png)

La escala de tiempo del proceso de enumeración de una aplicación de aprovisionamiento de SCIM en la galería es variable y depende de numerosos factores. 

### <a name="escalations"></a>Extensiones

Si necesita ponerse en contacto con una instancia superior, envíe un correo electrónico al [equipo de integración del SSO de Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) y le responderemos lo antes posible.


## <a name="step-7---join-the-microsoft-partner-network"></a>Paso 7: Únase a Microsoft Partner Network
Microsoft Partner Network proporciona acceso instantáneo a recursos, programas, herramientas y conexiones exclusivos. Para unirse a la red y crear el plan de comercialización, vea [Llegue a los clientes comerciales](https://partner.microsoft.com/explore/commercial#gtm).

## <a name="request-apps-by-sharing-isv-app-team-contact"></a>Solicitud de aplicaciones mediante el envío del contacto del equipo de aplicación de ISV

Los clientes pueden solicitar la aplicación compartiendo la información de contacto de la aplicación y de ISV [aquí](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/AppRequestsByCustomers.aspx).

![Muestra el icono de aplicaciones solicitadas por los clientes.](./media/howto-app-gallery-listing/customer-submit-request.png)

Este es el flujo de las aplicaciones solicitadas por el cliente.

![Muestra el flujo de aplicaciones solicitadas por los clientes.](./media/howto-app-gallery-listing/customer-request-2.png)

> [!Note]
> Si tiene algún [problema de acceso](#issues-on-logging-into-portal), envíe un correo electrónico al [equipo de integración de aplicaciones de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="next-steps"></a>Pasos siguientes

* [Aprovisionamiento de usuarios de SCIM con Azure Active Directory (Azure AD)](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Escenarios de autenticación de Azure AD](authentication-flows-app-scenarios.md)