---
title: ¿Qué es la administración de aplicaciones?
description: Introducción a la administración del ciclo de vida de una aplicación en Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/22/2021
ms.author: davidmu
ms.reviewer: sureshja, napuri
ms.openlocfilehash: 3b1e814def005283fb08ab7eeb3c23a8a38e3ee2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257409"
---
# <a name="what-is-application-management-in-azure-active-directory"></a>¿Qué es la administración de aplicaciones en Azure Active Directory?

La administración de aplicaciones en Azure Active Directory (Azure AD) es el proceso de creación, configuración, administración y supervisión de aplicaciones en la nube. Cuando se registra una [aplicación](../develop/app-objects-and-service-principals.md) en un inquilino Azure AD, los usuarios que se le han asignado pueden acceder a ella de forma segura. Se pueden registrar muchos tipos de aplicaciones en Azure AD. Para más información, consulte [Tipos de aplicaciones para la Plataforma de identidad de Microsoft](../develop/v2-app-types.md).

En este artículo, aprenderá estos aspectos importantes de la administración del ciclo de vida de una aplicación:

- **Desarrollar, agregar o conectar**: se toman rutas diferentes en función de si está desarrollando su propia aplicación, utilizando una aplicación integrada previamente o conectándose a una aplicación local.
- **Administrar el acceso**: el acceso se puede administrar mediante el inicio de sesión único (SSO), la asignación de recursos, la definición de la forma en que se concede y da su consentimiento al acceso y el uso del aprovisionamiento automatizado.
- **Configurar propiedades**: configure los requisitos para iniciar sesión en la aplicación y cómo se representa la aplicación en los portales de usuario.
- **Proteger la aplicación**: administre la configuración de permisos, la autenticación multifactor (MFA), el acceso condicional, los tokens y los certificados.
- **Gobernar y supervisar**: administre la interacción y revise la actividad mediante la administración de derechos y los recursos de informes y supervisión.
- **Limpiar**: cuando la aplicación ya no sea necesaria, elimine el acceso a la misma y elimínela para limpiar el inquilino.

## <a name="develop-add-or-connect"></a>Desarrollar, agregar o conectar

Hay varias maneras de administrar aplicaciones en Azure AD. La manera más fácil de empezar a administrar una aplicación es usar una aplicación integrada previamente desde la galería de Azure AD. Una opción es desarrollar su propia aplicación y registrarla en Azure AD, o bien puede seguir usando una aplicación local.

En la imagen siguiente, se muestra cómo interactúan estas aplicaciones con Azure AD.

:::image type="content" source="media/what-is-application-management/app-management-overview.png" alt-text="Diagrama que muestra cómo se pueden usar como aplicaciones empresariales sus propios desarrollos de aplicaciones, las aplicaciones integradas previamente y las aplicaciones locales.":::

### <a name="pre-integrated-applications"></a>Aplicaciones preintegradas

Muchas aplicaciones ya están integradas previamente (se muestran como "aplicaciones en la nube" en la imagen anterior) y se pueden configurar con un mínimo esfuerzo. Cada aplicación de la galería de Azure AD tiene disponible un artículo que muestra los pasos necesarios para [configurar la aplicación](../saas-apps/tutorial-list.md). Para obtener un ejemplo sencillo de cómo se puede agregar una aplicación al inquilino de Azure AD desde la galería, consulte [Inicio rápido: Adición de una aplicación empresarial en Azure Active Directory](add-application-portal.md).

### <a name="your-own-applications"></a>Sus propias aplicaciones

Si desarrolla su propia aplicación empresarial, puede registrarla con Azure AD para aprovechar las ventajas de las características de seguridad que proporciona el inquilino. Puede registrar la aplicación en **Registros de aplicaciones** o puede registrarla mediante el vínculo **Cree su propia aplicación** al agregar una nueva aplicación en **Aplicaciones empresariales**. Tenga en cuenta cómo se ha implementado la [autenticación](../develop/authentication-vs-authorization.md) en la aplicación para la integración con Azure AD. 

Si desea que la aplicación esté disponible en la galería, puede [enviar una solicitud para agregarla](../develop/v2-howto-app-gallery-listing.md).


### <a name="on-premises-applications"></a>Aplicaciones locales

Si desea seguir usando una aplicación local, pero aprovechar las ventajas que ofrece Azure AD, conéctela con Azure AD mediante [Application Proxy de Azure AD](../app-proxy/application-proxy.md). Application Proxy se puede implementar cuando desea publicar aplicaciones locales externamente. Los usuarios remotos que necesitan acceso a las aplicaciones internas pueden acceder a ellas de forma segura.

## <a name="manage-access"></a>Administración del acceso

Para [administrar el acceso](what-is-access-management.md) de una aplicación, será necesario responder a las siguientes preguntas:

- ¿Cómo se concede y se da el consentimiento al acceso para la aplicación?
- ¿La aplicación admite el inicio de sesión único?
- ¿Qué usuarios, grupos y propietarios se deben asignar a la aplicación? 
- ¿Hay otros proveedores de identidades que admitan la aplicación?
- ¿Será útil automatizar el aprovisionamiento de identidades y roles de usuario?

### <a name="access-and-consent"></a>Acceso y consentimiento

Puede administrar la [configuración del consentimiento del usuario](configure-user-consent.md) para elegir si los usuarios pueden permitir que una aplicación o un servicio accedan a los perfiles de usuario y a los datos de la organización. Cuando se concede acceso a las aplicaciones, los usuarios pueden iniciar sesión en las aplicaciones integradas con Azure AD y la aplicación puede acceder a los datos de la organización para ofrecer experiencias enriquecidas controladas por datos.

A menudo, los usuarios no pueden dar su consentimiento a los permisos que solicita una aplicación. Configure el [flujo de trabajo de consentimiento del administrador](configure-admin-consent-workflow.md) para permitir a los usuarios proporcionar una justificación y solicitar la revisión y aprobación de una aplicación por parte de un administrador.

Como administrador, puede [conceder el consentimiento del administrador para todo el inquilino](grant-admin-consent.md) a una aplicación. El consentimiento del administrador para todo el inquilino es necesario cuando una aplicación requiere permisos que los usuarios normales no pueden conceder y permite a las organizaciones implementar sus propios procesos de revisión. Antes de conceder el consentimiento, revise siempre con atención los permisos que solicita la aplicación. Cuando a una aplicación se le concede el consentimiento del administrador para todo el inquilino, todos los usuarios pueden iniciar sesión en la aplicación a menos que se haya configurado para requerir la asignación de usuarios.

### <a name="single-sign-on"></a>Inicio de sesión único

Considere la posibilidad de implementar el inicio de sesión único en la aplicación. Puede configurar manualmente la mayoría de las aplicaciones para el inicio de sesión único. Las opciones más populares en Azure AD son [SSO basado en SAML y SSO basado en OpenID Connect](../develop/active-directory-v2-protocols.md). Antes de empezar, asegúrese de que comprende los requisitos del inicio de sesión único y cómo [planear la implementación](plan-sso-deployment.md). Para obtener un ejemplo sencillo de cómo configurar el inicio de sesión único basado en SAML para una aplicación empresarial en el inquilino de Azure AD, consulte [Inicio rápido: Habilitación del inicio de sesión único para una aplicación empresarial en Azure Active Directory](add-application-portal-setup-sso.md).

### <a name="user-group-and-owner-assignment"></a>Asignación de usuarios, grupos y propietarios

De manera predeterminada, los usuarios pueden acceder a las aplicaciones empresariales sin tener que asignarlas. Sin embargo, si desea asignar la aplicación a un conjunto de usuarios, la aplicación requiere la asignación de usuarios. Para obtener un ejemplo sencillo de cómo crear y asignar una cuenta de usuario a una aplicación, consulte [Inicio rápido: Creación y asignación de una cuenta de usuario en Azure Active Directory](add-application-portal-assign-users.md). 

Si se incluye en la suscripción, [asigne grupos a una aplicación](assign-user-or-group-access-portal.md) para que pueda delegar la administración del acceso continuo al propietario del grupo. 

[Asignar propietarios](assign-app-owners.md) es una manera sencilla de conceder la capacidad de administrar todos los aspectos de la configuración de Azure AD para una aplicación. Como propietario, un usuario puede administrar la configuración específica de la organización de la aplicación.

### <a name="automate-provisioning"></a>Aprovisionamiento automatizado

El [aprovisionamiento de aplicaciones](../app-provisioning/user-provisioning.md) hace referencia a la creación automática de identidades y roles de usuario en las aplicaciones a las que los usuarios necesitan acceder. Además de crear identidades de usuario, el aprovisionamiento automático incluye el mantenimiento y la eliminación de identidades de usuario a medida que el estado o los roles cambian.

### <a name="identity-providers"></a>Proveedores de identidades

¿Tiene un proveedor de identidades con el que quiere que interactúe Azure AD? La [detección del dominio de inicio](home-realm-discovery-policy.md) proporciona una configuración que permite a Azure AD determinar con qué proveedor de identidades se debe autenticar un usuario en el momento del inicio de sesión.

### <a name="user-portals"></a>Portales de usuario

Azure AD proporciona maneras personalizables para implementar aplicaciones para los usuarios de la organización. Por ejemplo, el [portal Aplicaciones o el iniciador de aplicaciones de Microsoft 365](end-user-experiences.md). El portal Aplicaciones proporciona a los usuarios un lugar único desde el que pueden empezar a trabajar y buscar todas las aplicaciones a las que tienen acceso. Como administrador de una aplicación, debe [planear cómo usarán los usuarios de la organización el portal Aplicaciones](my-apps-deployment-plan.md).

## <a name="configure-properties"></a>Configuración de propiedades

Al agregar una aplicación al inquilino de Azure AD, tiene la oportunidad de [configurar propiedades](add-application-portal-configure.md) que afectan a la forma en la que los usuarios pueden iniciar sesión. Puede habilitar o deshabilitar la capacidad de iniciar sesión y puede ser necesaria la asignación de usuarios. También puede determinar la visibilidad de la aplicación, qué logotipo representa a la aplicación y cualquier nota sobre la aplicación.

## <a name="secure-the-application"></a>Protección de la aplicación

Hay varios métodos disponibles para ayudarle a proteger las aplicaciones empresariales. Por ejemplo, puede [restringir el acceso al inquilino](tenant-restrictions.md), [administrar la visibilidad, los datos y el análisis](cloud-app-security.md), y posiblemente proporcionar [acceso híbrido](secure-hybrid-access.md). Mantener las aplicaciones empresariales seguras también implica administrar la configuración de permisos, MFA, acceso condicional, tokens y certificados.

### <a name="permissions"></a>Permisos

Es importante revisar periódicamente y, si es necesario, [administrar los permisos concedidos a una aplicación o un servicio](manage-application-permissions.md). Asegúrese de permitir solo el acceso adecuado a las aplicaciones evaluando periódicamente si existe actividad sospechosa.

Las [clasificaciones de permisos](configure-permission-classifications.md) permiten identificar el efecto de los distintos permisos según las evaluaciones de riesgo y las directivas de la organización. Por ejemplo, puede usar las clasificaciones de permisos en las directivas de consentimiento para identificar el conjunto de permisos a los que los usuarios pueden dar su consentimiento.

### <a name="multifactor-authentication-and-conditional-access"></a>Autenticación multifactor y acceso condicional

Azure Active Directory Multifactor Authentication ayuda a proteger el acceso a datos y aplicaciones, ya que proporciona otra capa de seguridad mediante una segunda forma de autenticación. Hay muchos métodos que se pueden usar para una autenticación de segundo factor. Antes de empezar, [planee la implementación de MFA para la aplicación](../authentication/howto-mfa-getstarted.md) en la organización.

Las organizaciones pueden habilitar MFA con [acceso condicional](../conditional-access/overview.md) para que la solución se ajuste a sus necesidades específicas. Las directivas de acceso condicional permiten que los administradores asignen controles a determinadas [aplicaciones, acciones o contextos de autenticación](../conditional-access/concept-conditional-access-cloud-apps.md).

### <a name="tokens-and-certificates"></a>Tokens y certificados

En un flujo de autenticación de Azure AD, se usan distintos tipos de tokens de seguridad en función del protocolo utilizado. Por ejemplo, los [tokens SAML](../develop/reference-saml-tokens.md) se usan para el protocolo SAML y los [tokens de identificador](../develop/id-tokens.md) y los [tokens de acceso](../develop/access-tokens.md) se usan para el protocolo OpenID Connect. Los tokens se firman con el certificado único que se genera en Azure AD y mediante algoritmos estándar específicos. 

Puede proporcionar más seguridad mediante el [cifrado del token](howto-saml-token-encryption.md). También puede administrar la información de un token, incluidos los [roles permitidos](../develop/howto-add-app-roles-in-azure-ad-apps.md) para la aplicación.

Azure AD utiliza el [algoritmo SHA-256](certificate-signing-options.md) de manera predeterminada para firmar la respuesta SAML. Utilice SHA-256 a menos que la aplicación requiera SHA-1. Establezca un proceso para [administrar la duración del certificado](manage-certificates-for-federated-single-sign-on.md). La duración máxima de un certificado de firma es de tres años. Para evitar o minimizar la interrupción debido a la expiración de un certificado, use las listas de distribución de correo electrónico y roles para asegurarse de que las notificaciones de cambios relacionadas con los certificados se supervisan con atención. 

## <a name="govern-and-monitor"></a>Gobierno y supervisión

La [administración de derechos](../governance/entitlement-management-scenarios.md) de Azure AD permite administrar la interacción entre aplicaciones y administradores, propietarios de catálogos, administradores de paquetes de acceso, aprobadores y solicitantes.

La solución de supervisión e informes de Azure AD depende de sus requisitos legales, de seguridad y operativos, así como del entorno y los procesos existentes. Se mantienen varios registros en Azure AD y debe [planear la implementación de informes y supervisión](../reports-monitoring/plan-monitoring-and-reporting.md) para mantener la mejor experiencia posible para la aplicación.

## <a name="clean-up"></a>Limpieza

Puede limpiar el acceso a las aplicaciones. Por ejemplo, [quitar el acceso de un usuario](methods-for-removing-user-access.md). También puede [deshabilitar el modo en que un usuario inicia sesión](disable-user-sign-in-portal.md). Por último, puede eliminar la aplicación si ya no es necesaria para la organización. Para obtener un ejemplo sencillo de cómo eliminar una aplicación empresarial del inquilino de Azure AD, consulte [Inicio rápido: Eliminación de una aplicación empresarial en Azure Active Directory](delete-application-portal.md).

## <a name="next-steps"></a>Pasos siguientes

- Para empezar, agregue su primera aplicación empresarial con [Inicio rápido: Adición de una aplicación empresarial en Azure Active Directory](add-application-portal.md).