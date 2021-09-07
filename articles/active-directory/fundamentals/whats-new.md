---
title: Novedades Notas de la versión de Azure Active Directory | Microsoft Docs
description: Obtenga información acerca de las novedades de Azure Active Directory, como son las notas de la versión más recientes, los problemas conocidos, las correcciones de errores, las funcionalidades en desuso y los próximos cambios.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/30/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c9b954776c33fedc3a770bc0aab0185ad595aff
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219742"
---
# <a name="whats-new-in-azure-active-directory"></a>¿Cuáles son las novedades de Azure Active Directory?

>Reciba notificaciones para volver a visitar esta página y obtener actualizaciones; para ello, copie y pegue la dirección URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` en el ![icono del lector de fuentes icono RSS](./media/whats-new/feed-icon-16x16.png) lector de fuentes.

En Azure AD se realizan mejoras de forma continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores
- Funciones obsoletas
- Planes de cambios

Esta página se actualiza mensualmente, por lo que se recomienda visitarla con frecuencia. Si busca elementos que tengan más de seis meses, puede encontrarlos en el [Archivo de novedades de Azure Active Directory](whats-new-archive.md).

---
## <a name="july-2021"></a>Julio de 2021

### <a name="new-google-sign-in-integration-for-azure-ad-b2c-and-b2b-self-service-sign-up-and-invited-external-users-will-stop-working-starting-july-12-2021"></a>La nueva integración de inicio de sesión de Google para Azure AD B2C y el registro de autoservicio y los usuarios externos invitados de B2B dejarán de funcionar a partir del 12 de julio de 2021

**Tipo:** Plan de cambio  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 

Anteriormente anunciamos que [la excepción aplicable a las vistas web insertadas para la autenticación de Gmail expirará en la segunda mitad de 2021](https://www.yammer.com/cepartners/threads/1188371962232832).

El 7 de julio de 2021, Google informó que algunas de estas restricciones se empezarán a aplicar el **12 de julio de 2021**. A los clientes de Azure AD B2B y B2C que configuren un nuevo inicio de sesión con el Id. de Google en sus aplicaciones personalizadas o de línea de negocio para invitar a usuarios externos o habilitar el registro de autoservicio se les aplicarán las restricciones de inmediato. Como resultado, los usuarios finales se encontrarán con una pantalla de error que bloquea su acceso a Gmail si la autenticación no se traslada a una vista web del sistema. Consulta la documentación vinculada más adelante para obtener más información. 

La mayoría de las aplicaciones usan una vista web del sistema de manera predeterminada, por lo que no se verán afectadas por este cambio. Esta restricción solo se aplica a los clientes que usan vistas web insertadas (configuración no predeterminada). Recomendamos a los clientes que trasladen la autenticación de su aplicación a exploradores del sistema, antes de crear otras integraciones de Google. Para obtener información sobre cómo pasar a exploradores del sistema las autenticaciones de Gmail, consulte la sección "Interfaz de usuario web integrada y del sistema" en la documentación [Uso de exploradores web (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Todos los SDK de MSAL usan la vista web del sistema de forma predeterminada. [Más información](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).

---

### <a name="google-sign-in-on-embedded-web-views-expiring-september-30-2021"></a>El inicio de sesión de Google en vistas web insertadas expira el 30 de septiembre de 2021

**Tipo:** Plan de cambio  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 

Hace alrededor de dos meses anunciamos que la excepción aplicable a las vistas web insertadas para la autenticación de Gmail expirará en la segunda mitad de 2021. 

Hace poco, Google ha concretado que la fecha será el **30 de septiembre de 2021**. 

A partir de esa fecha, en todo el mundo se pedirá a los invitados de Azure AD B2B que inicien sesión con su cuenta de Gmail que escriban un código en una ventana aparte del explorador, para terminar de iniciar sesión en clientes móviles y de escritorio de Microsoft Teams. Esta restricción se aplicará a los invitados que hayan recibido una invitación, así como los invitados que hayan usado el registro de autoservicio. 

Los clientes de Azure AD B2C que hayan configurado autenticaciones de Gmail de vistas web insertadas en sus aplicaciones personalizadas o de línea de negocio, o que ya tuvieran integraciones de Google, ya no podrán permitir que sus usuarios inicien sesión con cuentas de Gmail. Para mitigar este problema, asegúrese de modificar sus aplicaciones, de modo que usen el explorador del sistema para iniciar sesión. Para obtener más información, consulte la sección "Interfaz de usuario web integrada y del sistema" en la documentación [Uso de exploradores web (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Todos los SDK de MSAL usan la vista web del sistema de forma predeterminada. 

Como el flujo de inicio de sesión en dispositivos comenzará a implementarse el 30 de septiembre de 2021, es probable que aún no se haya implementado en su región (en cuyo caso, sus usuarios finales se encontrarán con la pantalla de error que se muestra en la documentación hasta que se implemente). 

Para obtener más información sobre los escenarios afectados conocidos y la experiencia que pueden esperar sus usuarios, consulte [Incorporación de Google como proveedor de identidades para los usuarios invitados de B2B](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).

---

### <a name="bug-fixes-in-my-apps"></a>Corrección de errores en Aplicaciones

**Tipo:** Corregido  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** Experiencias de usuario final
 
- Antes, la presencia del banner que recomienda el uso de colecciones provocaba que el contenido se desplazara detrás del encabezado. El problema se ha solucionado. 
- Antes, había otro problema al agregar aplicaciones a una colección, en virtud de cual las aplicaciones de la colección Todas las aplicaciones se reordenaban de manera aleatoria. Este problema también se ha solucionado. 

Para obtener más información sobre Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

---

### <a name="public-preview----application-authentication-method-policies"></a>Versión preliminar pública: directivas de métodos de autenticación de aplicaciones

**Tipo:** Nueva característica  
**Categoría del servicio:** MS Graph  
**Funcionalidad del producto:** Experiencia para el desarrollador
 
Las directivas de métodos de autenticación de aplicaciones de MS Graph permiten a los administradores de TI aplicar una vigencia a las credenciales secretas de contraseñas de aplicaciones o bloquear por completo el uso de secretos. Es posible aplicar directivas a todo un inquilino como una configuración predeterminada o limitar su ámbito a aplicaciones o entidades de servicio específicas. [Más información](/graph/api/resources/policy-overview?view=graph-rest-1.0).
 
---

### <a name="public-preview----authentication-methods-nudge-to-download-microsoft-authenticator"></a>Versión preliminar pública: desplazamiento de los métodos de autenticación para descargar Microsoft Authenticator

**Tipo:** Nueva característica  
**Categoría de servicio:** Aplicación Microsoft Authenticator  
**Funcionalidad del producto:** Autenticación de usuarios
 
La directiva de desplazamiento de Authenticator ayuda a los administradores a trasladar su organización a una posición más segura, al pedir a los usuarios que adopten la aplicación de Microsoft Authenticator. Antes de esta característica, los administradores no tenían forma de obligar a sus usuarios a configurar la aplicación. 

El desplazamiento ofrece a los administradores la posibilidad de controlar el ámbito de los usuarios y grupos, incluyéndolos o excluyéndolos de la directiva con el fin de garantizar una adopción fluida en toda la organización. [Más información](../authentication/how-to-nudge-authenticator-app.md)
 
---

### <a name="public-preview----separation-of-duties-check"></a>Versión preliminar pública: comprobación de la separación de obligaciones 

**Tipo:** Nueva característica  
**Categoría del servicio:** Administración de acceso de usuarios  
**Funcionalidad del producto:** Administración de derechos
 
En la administración de derechos de Azure AD, un administrador puede definir que un paquete de acceso sea incompatible con otro paquete de acceso o con un grupo.  Los usuarios con una afiliación incompatible no podrán solicitar acceso adicional. [Más información](../governance/entitlement-management-access-package-request-policy.md#prevent-requests-from-users-with-incompatible-access-preview).
 
---

### <a name="public-preview----identity-protection-logs-in-log-analytics-storage-accounts-and-event-hubs"></a>Versión preliminar pública: registros de Identity Protection en Log Analytics, cuentas de almacenamiento y Event Hubs

**Tipo:** Nueva característica  
**Categoría del servicio:** Protección de identidad  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
Ahora puede enviar los usuarios de riesgo y los registros de detecciones de riesgos a Azure Monitor, cuentas de almacenamiento o Log Analytics, usando la configuración de diagnóstico en la hoja de Azure AD. [Más información](../identity-protection/howto-export-risk-data.md).
 
---

### <a name="public-preview----application-proxy-api-addition-for-backend-ssl-certificate-validation"></a>Versión preliminar pública: nueva API de Application Proxy para la validación de certificados SSL de back-end

**Tipo:** Nueva característica  
**Categoría del servicio:** Proxy de aplicaciones  
**Funcionalidad del producto:** Control de acceso
 
El tipo de recurso onPremisesPublishing incluye ahora la propiedad "isBackendCertificateValidationEnabled", que indica si la validación de certificados SSL de back-end está habilitada para la aplicación. Para todas las nuevas aplicaciones de Application Proxy, la propiedad se establecerá en "true" de manera predeterminada. Para todas las aplicaciones ya existentes, la propiedad se establecerá en "false". Para obtener más información, consulte [Tipo de recurso onPremisesPublishing](/graph/api/resources/onpremisespublishing?view=graph-rest-beta).
 
---

### <a name="general-availability---improved-authenticator-setup-experience-for-add-azure-ad-account-in-microsoft-authenticator-app-by-directly-signing-into-the-app"></a>Disponibilidad general: se ha mejorado la experiencia de configuración de Authenticator al agregar una cuenta de Azure AD en la aplicación de Microsoft Authenticator iniciando sesión directamente en esta

**Tipo:** Nueva característica  
**Categoría de servicio:** Aplicación Microsoft Authenticator  
**Funcionalidad del producto:** Autenticación de usuarios
 
Los usuarios ahora pueden usar los métodos de autenticación de que ya disponen para iniciar sesión directamente en la aplicación de Microsoft Authenticator con el fin de configurar sus credenciales. Los usuarios ya no necesitan digitalizar un código QR y pueden usar un Pase de acceso temporal (TAP) o una contraseña + SMS (u otro método de autenticación) para configurar su cuenta en la aplicación de Authenticator.

De este modo, se mejora el proceso de aprovisionamiento de credenciales de usuario de la aplicación de Microsoft Authenticator y se proporciona al usuario final un método de autoservicio para aprovisionarla. [Más información](../user-help/user-help-auth-app-add-work-school-account.md#sign-in-with-your-credentials).
 
---

### <a name="general-availability---set-manager-as-reviewer-in-azure-ad-entitlement-management-access-packages"></a>Disponibilidad general: establecimiento del administrador como revisor en paquetes de acceso de la administración de derechos de Azure AD

**Tipo:** Nueva característica  
**Categoría del servicio:** Administración de acceso de usuarios  
**Funcionalidad del producto:** Administración de derechos
 
Los paquetes de acceso de la administración de derechos de Azure AD ahora permiten establecer al administrador del usuario como revisor para las revisiones de acceso que tienen lugar con regularidad. [Más información](../governance/entitlement-management-access-reviews-create.md).

---

### <a name="general-availability---enable-external-users-to-self-service-sign-up-in-aad-using-msa-accounts"></a>Disponibilidad general: habilitación de usuarios externos para el registro de autoservicio en AAD usando cuentas de MSA

**Tipo:** Nueva característica  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 
Los usuarios ahora pueden permitir que otros usuarios externos se registren en Azure Active Directory mediante una opción de autoservicio usando cuentas de Microsoft. [Más información](../external-identities/microsoft-account.md).
 
---
 
### <a name="general-availability---external-identities-self-service-sign-up-with-email-one-time-passcode"></a>Disponibilidad general: registro de autoservicio de External Identities con código de acceso de un solo uso enviado por correo electrónico

**Tipo:** Nueva característica  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 

Ahora los usuarios pueden permitir que otros usuarios externos se registren en Azure Active Directory mediante una opción de autoservicio, con su correo electrónico y un código de acceso de un solo uso. [Más información](../external-identities/one-time-passcode.md).
 
---

### <a name="general-availability---anomalous-token"></a>Disponibilidad general: token anómalo

**Tipo:** Nueva característica  
**Categoría del servicio:** Protección de identidad  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
La detección de tokens anómalos ya está disponible en Identity Protection. Esta opción puede detectar si hay características anómalas en el token, como tiempo activo o una autenticación desde una dirección IP desconocida. [Más información](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---

### <a name="general-availability---register-or-join-devices-in-conditional-access"></a>Disponibilidad general: registro o unión de dispositivos en el acceso condicional

**Tipo:** Nueva característica  
**Categoría del servicio:** Acceso condicional  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
La acción de usuario para registrar o unir dispositivos en el acceso condicional ya está disponible de forma general. Esta acción de usuario le permite controlar directivas de autenticación multifactor (MFA) para el registro de dispositivos de Azure AD. 

Actualmente, esta acción del usuario solo permite habilitar MFA como un control cuando los usuarios registran o unen dispositivos a Azure AD. Otros controles que dependan del registro de dispositivos de Azure AD o que no se apliquen a estos permanecerán deshabilitados con esta acción de usuario. [Más información](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2021"></a>Nuevos conectores de aprovisionamiento disponibles en la galería de aplicaciones de Azure AD, julio de 2021

**Tipo:** Nueva característica  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Integración de terceros
 
Ahora, puede automatizar la creación, actualización y eliminación de cuentas de usuario para estas aplicaciones recién integradas:

- [Clebex](../saas-apps/clebex-provisioning-tutorial.md)
- [Exium](../saas-apps/exium-provisioning-tutorial.md)
- [SoSafe](../saas-apps/sosafe-provisioning-tutorial.md)
- [Talentech](../saas-apps/talentech-provisioning-tutorial.md)
- [Thrive LXP](../saas-apps/thrive-lxp-provisioning-tutorial.md)
- [Vonage](../saas-apps/vonage-provisioning-tutorial.md)
- [Zip](../saas-apps/zip-provisioning-tutorial.md)
- [TimeClock 365](../saas-apps/timeclock-365-provisioning-tutorial.md)

Para obtener más información sobre cómo proteger mejor su organización mediante el aprovisionamiento automatizado de cuentas de usuario, consulte [Automatización del aprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="changes-to-security-and-microsoft-365-group-settings-in-azure-portal"></a>Cambios en la seguridad y la configuración de grupos de Microsoft 365 en Azure Portal

**Tipo:** Característica modificada  
**Categoría del servicio:** Administración de grupos  
**Funcionalidad del producto:** Directorio
 

En el pasado, los usuarios podían crear grupos de seguridad y de Microsoft 365 en Azure Portal. Ahora los usuarios tendrán la posibilidad de crear grupos en Azure Portal, PowerShell y API. Los clientes deben comprobar y actualizar los nuevos valores que se han configurado para su organización. [Más información](../enterprise-users/groups-self-service-management.md#group-settings).
 
---

### <a name="all-apps-collection-has-been-renamed-to-apps"></a>La colección "Todas las aplicaciones" ha pasado a denominarse "Aplicaciones".

**Tipo:** Característica modificada  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** Experiencias de usuario final
 
En el portal Aplicaciones, la colección "Todas las aplicaciones" ha pasado a denominarse "Aplicaciones". Dada la evolución del producto, "Aplicaciones" es un nombre más adecuado para esta colección predeterminada. [Más información](../manage-apps/my-apps-deployment-plan.md#plan-the-user-experience).
 
---
 
## <a name="june-2021"></a>Junio de 2021

### <a name="context-panes-to-display-risk-details-in-identity-protection-reports"></a>Paneles de contexto para mostrar detalles de riesgos en informes de Identity Protection

**Tipo:** Plan de cambio  
**Categoría del servicio:** Protección de identidad  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
En el caso de los informes de usuarios de riesgo, inicios de sesión de riesgo y detección de riesgos de Identity Protection, a partir de julio de 2021 los detalles de riesgos de una entrada seleccionada se mostrarán en un panel de contexto que aparecerá a la derecha de la página. El cambio no afectará a las funcionalidades existentes, solo a la interfaz de usuario. Para obtener más información sobre la funcionalidad de estas características, consulte [Instrucciones: Investigación de riesgos](../identity-protection/howto-identity-protection-investigate-risk.md).
 
---

### <a name="public-preview----create-azure-ad-access-reviews-of-service-principals-that-are-assigned-to-privileged-roles"></a>Versión preliminar pública: creación de revisiones de acceso de Azure AD de entidades de servicio asignadas a roles con privilegios

**Tipo:** Nueva característica  
**Categoría del servicio:** Revisiones de acceso  
**Funcionalidad del producto:** Identity Governance
 
 Puede usar las revisiones de acceso de Azure AD para revisar el acceso de entidades de servicio a los roles de recurso con privilegios de Azure AD y Azure. [Más información](../privileged-identity-management/pim-how-to-start-security-review.md#open-access-reviews).
 
---

### <a name="public-preview----group-owners-in-azure-ad-can-create-and-manage-azure-ad-access-reviews-for-their-groups"></a>Versión preliminar pública: los propietarios de grupos de Azure AD pueden crear y administrar revisiones de acceso de Azure AD para sus grupos

**Tipo:** Nueva característica  
**Categoría del servicio:** Revisiones de acceso  
**Funcionalidad del producto:** Identity Governance
 
Ahora los propietarios de grupos de Azure AD pueden crear y administrar revisiones de acceso de Azure AD en sus grupos. Esta capacidad la pueden habilitar los administradores de inquilinos mediante la configuración de revisiones de acceso de Azure AD y está deshabilitada de manera predeterminada. [Más información](../governance/create-access-review.md#allow--group-owners-to-create-and-manage-access-reviews-preview).
 
---

### <a name="public-preview----customers-can-scope-access-reviews-of-privileged-roles-to-just-users-with-eligible-or-active-access"></a>Versión preliminar pública: los clientes pueden establecer el ámbito de las revisiones de acceso de los roles con privilegios solo en los usuarios con acceso válido o activo

**Tipo:** Nueva característica  
**Categoría del servicio:** Revisiones de acceso  
**Funcionalidad del producto:** Identity Governance
 
Cuando los administradores crean revisiones de acceso de asignaciones a roles con privilegios, pueden establecer el ámbito de las revisiones solo en usuarios asignados de forma válida o solo en asignados de forma activa. [Más información](../privileged-identity-management/pim-how-to-start-security-review.md).
 
---

### <a name="public-preview----microsoft-graph-apis-for-mobility-mdmmam-management-policies"></a>Versión preliminar pública: Microsoft Graph API para directivas de administración de la movilidad (MDM/MAM)

**Tipo:** Nueva característica  
**Categoría del servicio:** Otros  
**Funcionalidad del producto:** Administración del ciclo de vida de dispositivos
 
La compatibilidad de Microsoft Graph con la configuración de movilidad (MDM/MAM) de Azure AD está en versión preliminar pública. Los administradores pueden configurar el ámbito de usuario y las direcciones URL de aplicaciones MDM como Intune mediante Microsoft Graph v1.0. Para obtener más información, consulte [Tipo de recurso mobilityManagementPolicy](/graph/api/resources/mobilitymanagementpolicy?view=graph-rest-beta).

---

### <a name="general-availability---custom-questions-in-access-package-request-flow-in-azure-active-directory-entitlement-management"></a>Disponibilidad general: preguntas personalizadas en el flujo de solicitudes de paquetes de acceso de la administración de derechos de Azure Active Directory

**Tipo:** Nueva característica  
**Categoría del servicio:** Administración de acceso de usuarios  
**Funcionalidad del producto:** Administración de derechos
 
La administración de derechos de Azure AD ahora admite la creación de preguntas personalizadas en el flujo de solicitud del paquete de acceso. Esta característica le permite configurar preguntas personalizadas en la directiva del paquete. Estas preguntas se muestran a los solicitantes, que pueden especificar sus respuestas como parte del proceso de solicitud de acceso. Estas respuestas se mostrarán a los aprobadores, que dispondrán de información útil para tomar mejores decisiones sobre la solicitud de acceso. [Más información](../governance/entitlement-management-access-package-create.md#add-requestor-information-to-an-access-package).

---

### <a name="general-availability---multi-geo-sharepoint-sites-as-resources-in-entitlement-management-access-packages"></a>Disponibilidad general: sitios de SharePoint con varias ubicaciones geográficas como recursos en paquetes de acceso de la administración de derechos

**Tipo:** Nueva característica  
**Categoría del servicio:** Administración de acceso de usuarios  
**Funcionalidad del producto:** Administración de derechos
 
Los paquetes de acceso de la administración de derechos ahora admiten sitios de SharePoint con varias ubicaciones geográficas en el caso de clientes que usen las funcionalidades multigeográficas en SharePoint Online. [Más información](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site).
 
---

### <a name="general-availability---knowledge-admin-and-knowledge-manager-built-in-roles"></a>Disponibilidad general: nuevos roles integrados "Administrador de conocimientos" y "Gestor de conocimientos"

**Tipo:** Nueva característica  
**Categoría del servicio:** RBAC  
**Funcionalidad del producto:** Control de acceso
 
Hay dos nuevos roles disponibles de forma general: "Administrador de conocimientos" y "Gestor de conocimientos".

- Los usuarios con el rol "Administrador de conocimientos" tienen acceso completo a toda la configuración de conocimientos de la organización en el centro de administración de Microsoft 365. Pueden crear y administrar contenido, como temas y acrónimos. Además, estos usuarios pueden crear centros de contenido, supervisar el estado del servicio y crear solicitudes de servicio. [Más información](../roles/permissions-reference.md#knowledge-administrator)
- Los usuarios con el rol "Gestor de conocimientos" pueden crear y administrar contenido y son los principales responsables de la calidad y la estructura de los conocimientos. Tienen derechos completos sobre acciones de administración de temas, como confirmar un tema, eliminarlo o aprobar modificaciones. Este rol también puede administrar taxonomías como parte de la herramienta de administración del almacén de términos y crear centros de contenido. [Más información](../roles/permissions-reference.md#knowledge-manager).

---

### <a name="general-availability---cloud-app-security-administrator-built-in-role"></a>Disponibilidad general: rol integrado "Administrador" de Cloud App Security

**Tipo:** Nueva característica  
**Categoría del servicio:** RBAC  
**Funcionalidad del producto:** Control de acceso
 
 Los usuarios con este rol tienen permisos totales en Cloud App Security. Pueden agregar administradores, agregar directivas y configuraciones de Microsoft Cloud App Security (MCAS), cargar registros y realizar acciones de gobernanza. [Más información](../roles/permissions-reference.md#cloud-app-security-administrator).
 
---

### <a name="general-availability---windows-update-deployment-administrator"></a>Disponibilidad general: rol "Administrador de implementación" de Windows Update

**Tipo:** Nueva característica  
**Categoría del servicio:** RBAC  
**Funcionalidad del producto:** Control de acceso
 

 Los usuarios con este rol pueden crear y administrar todos los aspectos de las implementaciones de Windows Update mediante el servicio de implementación Windows Update para empresas. El servicio de implementación permite a los usuarios configurar cuándo y cómo se implementan las actualizaciones. Además, los usuarios pueden especificar qué actualizaciones se ofrecen a los grupos de dispositivos de su inquilino. También permite a los usuarios supervisar el progreso de las actualizaciones. [Más información](../roles/permissions-reference.md#windows-update-deployment-administrator).
 
---

### <a name="general-availability---multi-camera-support-for-windows-hello"></a>Disponibilidad general: compatibilidad de Windows Hello con varias cámaras

**Tipo:** Nueva característica  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios
 
Con la actualización Windows 10 21H1, Windows Hello admite ahora varias cámaras. La actualización incluye valores predeterminados para usar la cámara externa cuando estén presentes esta y la integrada. [Más información](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/it-tools-to-support-windows-10-version-21h1/ba-p/2365103).

---
 
### <a name="general-availability---access-reviews-ms-graph-apis-now-in-v10"></a>Disponibilidad general: las revisiones de acceso de Microsoft Graph API están ahora en v1.0

**Tipo:** Nueva característica  
**Categoría del servicio:** Revisiones de acceso  
**Funcionalidad del producto:** Identity Governance
 
Microsoft Graph API está ahora en v1.0 y admite características de revisiones de acceso plenamente configurables. [Más información](/graph/api/resources/accessreviewsv2-root?view=graph-rest-1.0).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---june-2021"></a>Nuevos conectores de aprovisionamiento disponibles en la galería de aplicaciones de Azure AD, junio de 2021

**Tipo:** Nueva característica  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Integración de terceros
 
Ahora, puede automatizar la creación, actualización y eliminación de cuentas de usuario para estas aplicaciones recién integradas:

- [askSpoke](../saas-apps/askspoke-provisioning-tutorial.md)
- [Cloud Academy: SSO](../saas-apps/cloud-academy-sso-provisioning-tutorial.md)
- [CheckProof](../saas-apps/checkproof-provisioning-tutorial.md)
- [GoLinks](../saas-apps/golinks-provisioning-tutorial.md)
- [Holmes Cloud](../saas-apps/holmes-cloud-provisioning-tutorial.md)
- [H5mag](../saas-apps/h5mag-provisioning-tutorial.md)
- [LimbleCMMS](../saas-apps/limblecmms-provisioning-tutorial.md)
- [LogMeIn](../saas-apps/logmein-provisioning-tutorial.md)
- [SECURE DELIVER](../saas-apps/secure-deliver-provisioning-tutorial.md)
- [Sigma Computing](../saas-apps/sigma-computing-provisioning-tutorial.md)
- [Smallstep SSH](../saas-apps/smallstep-ssh-provisioning-tutorial.md)
- [Tribeloo](../saas-apps/tribeloo-provisioning-tutorial.md)
- [Twingate](../saas-apps/twingate-provisioning-tutorial.md)

Para más información, consulte [Qué es el aprovisionamiento automatizado de usuarios de aplicaciones SaaS en Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2021"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD, junio de 2021

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros
 
En junio de 2021 agregamos las siguientes 42 aplicaciones nuevas a nuestra galería de aplicaciones, con compatibilidad de federación:

[Taksel](https://app.taksel.it/admin/integrations), [IDrive360](../saas-apps/idrive360-tutorial.md), [VIDA](../saas-apps/vida-tutorial.md), [ProProfs Classroom](../saas-apps/proprofs-classroom-tutorial.md), [WAN-Sign](../saas-apps/wan-sign-tutorial.md), [Citrix Cloud SAML SSO](../saas-apps/citrix-cloud-saml-sso-tutorial.md), [Fabric](../saas-apps/fabric-tutorial.md), [DssAD](https://cloudlicensing.deepseedsolutions.com/), [RICOH Creative Collaboration RICC](https://www.ricoh-europe.com/products/software-apps/collaboration-board-software/ricc/), [Styleflow](../saas-apps/styleflow-tutorial.md), [Chaos](https://accounts.chaosgroup.com/corporate_login), [Traced Connector](https://control.traced.app/signup), [Squarespace](https://account.squarespace.com/org/azure), [MX3 Diagnostics Connector](https://mx3www.playground.dynuddns.com/signin-oidc), [Ten Spot](https://tenspot.co/api/v1/sso/azure/login/), [Finvari](../saas-apps/finvari-tutorial.md), [Mobile4ERP](https://play.google.com/store/apps/details?id=com.negevsoft.mobile4erp), [WalkMe US OpenID Connect](https://www.walkme.com/), [Neustar UltraDNS](../saas-apps/neustar-ultradns-tutorial.md), [cloudtamer.io](../saas-apps/cloudtamer-io-tutorial.md), [A Cloud Guru](../saas-apps/a-cloud-guru-tutorial.md), [PetroVue](../saas-apps/petrovue-tutorial.md), [Postman](../saas-apps/postman-tutorial.md), [ReadCube Papers](../saas-apps/readcube-papers-tutorial.md), [Peklostroj](https://app.peklostroj.cz/), [SynCloud](https://onboard.syncloud.io/), [Polymerhq.io](https://www.polymerhq.io/), [Bonos](../saas-apps/bonos-tutorial.md), [Astra Schedule](../saas-apps/astra-schedule-tutorial.md), [Draup](../saas-apps/draup-inc-tutorial.md), [Inc](../saas-apps/draup-inc-tutorial.md), [Applied Mental Health](../saas-apps/applied-mental-health-tutorial.md), [iHASCO Training](../saas-apps/ihasco-training-tutorial.md), [Nexsure](../saas-apps/nexsure-tutorial.md), [XEOX](https://login.xeox.com/), [Plandisc](https://create.plandisc.com/account/logon), [foundU](../saas-apps/foundu-tutorial.md), [Standard for Success Accreditation](../saas-apps/standard-for-success-accreditation-tutorial.md), [Penji Teams](https://web.penjiapp.com/), [CheckPoint Infinity Portal](../saas-apps/checkpoint-infinity-portal-tutorial.md), [Teamgo](../saas-apps/teamgo-tutorial.md), [Hopsworks.ai](../saas-apps/hopsworks-ai-tutorial.md), [HoloMeeting 2](https://backend2.holomeeting.io/)

También puede consultar la documentación de todas ellas aquí: https://aka.ms/AppsTutorial.

Para incluir su aplicación en la galería de aplicaciones de Azure AD, lea los detalles aquí: https://aka.ms/AzureADAppRequest.
 
---

### <a name="device-code-flow-now-includes-an-app-verification-prompt"></a>El flujo de código de dispositivo ahora incluye una solicitud de comprobación de aplicaciones

**Tipo:** Característica modificada  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios
 
El [flujo de código de dispositivo](../develop/v2-oauth2-device-code.md) se ha actualizado para incluir un mensaje de usuario extra. Al iniciar sesión, el usuario verá un mensaje en el que se le pide que valide la aplicación en la que está iniciando sesión.  Esta solicitud garantiza que no sea víctima de un ataque de suplantación de identidad (phishing). [Más información](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt).
 
---

### <a name="user-last-sign-in-date-and-time-is-now-available-on-azure-portal"></a>La última fecha y hora de inicio de sesión del usuario ya está disponible en Azure Portal

**Tipo:** Característica modificada  
**Categoría del servicio:** User Management  
**Funcionalidad del producto:** User Management
 
Ahora puede ver la última marca de fecha y hora de inicio de sesión de sus usuarios en Azure Portal. La información está disponible para cada usuario en la página de su perfil. Esta información le ayuda a identificar usuarios inactivos y a administrar eficazmente eventos de riesgo. [Más información](./active-directory-users-profile-azure-portal.md?context=%2fazure%2factive-directory%2fenterprise-users%2fcontext%2fugr-context).
 
---

### <a name="mim-bhold-suite-impact-of-end-of-support-for-microsoft-silverlight"></a>Repercusiones en MIM BHOLD Suite de la finalización del soporte para Microsoft Silverlight

**Tipo:** Característica modificada  
**Categoría del servicio:** Microsoft Identity Manager  
**Funcionalidad del producto:** Identity Governance
 
Microsoft Silverlight alcanzará la finalización de su soporte el 12 de octubre de 2021. Este cambio solo afecta a los clientes que usan Microsoft BHOLD Suite (no a otros escenarios de Microsoft Identity Manager). Para obtener más información, consulte [Finalización del soporte de Silverlight](https://support.microsoft.com/windows/silverlight-end-of-support-0a3be3c7-bead-e203-2dfd-74f0a64f1788).  

Los usuarios que no han instalado Microsoft Silverlight en su explorador no pueden usar los módulos de BHOLD Suite que requieren Silverlight, incluidos el generador de modelos de BHOLD, la integración de autoservicio de BHOLD FIM y BHOLD Analytics.  Los clientes con una implementación existente de BHOLD de uno o varios de esos módulos deben planificar la desinstalación de dichos módulos de sus equipos de servidor de BHOLD antes de octubre de 2021. Además, deben planificar la desinstalación de Silverlight de los equipos de usuario que interactuaran previamente con dicha implementación de BHOLD.
 
---

### <a name="my-experiences-end-of-support-for-internet-explorer-11"></a>Experiencias de Mi(s)*: fin de la compatibilidad con Internet Explorer 11

**Tipo:** Obsoleto  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** Experiencias de usuario final
 

El 21 de agosto de 2021 Microsoft 365 y otras aplicaciones dejarán de ser compatibles con Internet Explorer 11, incluidas las experiencias de Mi(s)*. Las experiencias de Mi(s)* a las que se accede a través de Internet Explorer no recibirán correcciones de errores ni actualizaciones, lo que puede provocar problemas. El equipo de Edge está trabajando para cumplir este plazo, que pueden estar sujeto a cambios. [Más información](https://blogs.windows.com/windowsexperience/2021/05/19/the-future-of-internet-explorer-on-windows-10-is-in-microsoft-edge/).
 
---

### <a name="planned-deprecation---malware-linked-ip-address-detection-in-identity-protection"></a>Desuso previsto: detección de direcciones IP vinculadas a malware en Identity Protection

**Tipo:** Obsoleto  
**Categoría del servicio:** Protección de identidad  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
A partir del 1 de octubre de 2021 Azure AD Identity Protection dejará de generar la detección "Dirección IP vinculada a malware". No es necesario realizar ninguna acción, y los clientes seguirán estando protegidos por las demás detecciones que proporciona Identity Protection. Para obtener más información sobre las directivas de protección, consulte [Directivas de Identity Protection](../identity-protection/concept-identity-protection-policies.md).
 
---
 
## <a name="may-2021"></a>Mayo de 2021

### <a name="public-preview----azure-ad-verifiable-credentials"></a>Versión preliminar pública: credenciales verificables de Azure AD

**Tipo:** Nueva característica  
**Categoría del servicio:** Otros  
**Funcionalidad del producto:** Autenticación de usuarios
 
Los clientes de Azure AD ahora pueden diseñar y emitir fácilmente credenciales verificables. Las credenciales verificables se pueden usar para representar pruebas de empleo, titulaciones académicas o cualquier otra notificación, respetando la privacidad. Valide digitalmente cualquier dato sobre cualquier persona y empresa. [Más información](../verifiable-credentials/index.yml).

---

### <a name="public-preview----device-code-flow-now-includes-an-app-verification-prompt"></a>Versión preliminar pública: el flujo de código de dispositivo ahora incluye una solicitud de comprobación de aplicaciones

**Tipo:** Nueva característica  
**Categoría del servicio:** Autenticación de usuarios  
**Funcionalidad del producto:** Autenticaciones (inicios de sesión)
 
Para mejorar la seguridad, el [flujo de código de dispositivo](../develop/v2-oauth2-device-code.md) se ha actualizado para incluir un aviso adicional, que valida que el usuario inicia sesión en la aplicación que espera. Está previsto que la implementación se inicie en junio y se complete antes de que finalice dicho mes.

Para ayudar a evitar ataques de suplantación de identidad (phishing) en los que un atacante engaña al usuario para que inicie sesión en una aplicación malintencionada, se ha agregado el siguiente aviso: "¿Está intentando iniciar sesión en [nombre para mostrar de la aplicación]?". Todos los usuarios verán este aviso al iniciar sesión mediante el flujo del código de dispositivo. Como medida de seguridad, no se puede quitar ni omitir. [Más información](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt).

---

### <a name="public-preview----build-and-test-expressions-for-user-provisioning"></a>Versión preliminar pública: expresiones de compilación y prueba para el aprovisionamiento de usuarios

**Tipo:** Nueva característica  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Administración del ciclo de vida de la identidad
 
El generador de expresiones permite crear y probar expresiones, sin tener que esperar a que se termine el ciclo de sincronización. [Más información](../app-provisioning/functions-for-customizing-application-data.md). 

---

### <a name="public-preview----enhanced-audit-logs-for-conditional-access-policy-changes"></a>Versión preliminar pública: registros de auditoría mejorados para los cambios en directivas de acceso condicional

**Tipo:** Nueva característica  
**Categoría del servicio:** Acceso condicional  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
Un aspecto importante de la administración del acceso condicional es comprender los cambios que se producen con el tiempo en las directivas. Estos cambios pueden provocar interrupciones para los usuarios finales, por lo que es fundamental mantener un registro de cambios y permitir que los administradores puedan revertir a versiones anteriores de las directivas. 

Además de mostrar quién ha realizado cambios en una directiva y cuándo lo ha hecho, los registros de auditoría también contendrán ahora un valor de propiedades modificadas. Esta novedad proporciona a los administradores una mayor visibilidad de las asignaciones, las condiciones o los controles que han cambiado. Si desea revertir a una versión anterior de una directiva, puede copiar la representación JSON de la versión anterior y usar las API de acceso condicional para restablecer rápidamente el estado anterior de la directiva. [Más información](../conditional-access/concept-conditional-access-policies.md).

---

### <a name="public-preview----sign-in-logs-include-authentication-methods-used-during-sign-in"></a>Versión preliminar pública: los registros de inicio de sesión incluyen los métodos de autenticación usados durante el inicio de sesión

**Tipo:** Nueva característica  
**Categoría del servicio:** MFA  
**Funcionalidad del producto:** Supervisión e informes
 

Los administradores ahora pueden ver los pasos secuenciales que los usuarios realizaron para iniciar sesión, incluidos los métodos de autenticación que se emplearon durante el inicio de sesión. 

Para acceder a estos detalles, vaya a los registros de inicio de sesión de Azure AD, seleccione un inicio de sesión y, a continuación, vaya a la pestaña "Detalles del método de autenticación". Aquí hemos incluido datos como el método usado, detalles sobre el método (por ejemplo, el número de teléfono o el nombre del teléfono), el requisito de autenticación cumplido y los detalles del resultado. [Más información](../reports-monitoring/concept-sign-ins.md).

---

### <a name="public-preview----pim-adds-support-for-abac-conditions-in-azure-storage-roles"></a>Versión preliminar pública: PIM admite ahora condiciones de ABAC en los roles de Azure Storage

**Tipo:** Nueva característica  
**Categoría del servicio:** Privileged Identity Management  
**Funcionalidad del producto:** Privileged Identity Management
 
Junto con la versión preliminar pública del control de acceso basado en atributos de un rol RBAC específico de Azure, también puede agregar condiciones de ABAC en Privileged Identity Management a sus asignaciones aptas. [Más información](../../role-based-access-control/conditions-overview.md#conditions-and-privileged-identity-management-pim).

---

### <a name="general-availability---conditional-access-and-identity-protection-reports-in-b2c"></a>Disponibilidad general: informes de acceso condicional y de protección de identidad en B2C

**Tipo:** Nueva característica  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C

B2C ahora admite el acceso condicional y la protección de identidad en los usuarios y las aplicaciones de negocio a consumidor (B2C). Esto permite que los clientes puedan proteger a sus usuarios con controles de acceso granulares basados en el riesgo y la ubicación. Con estas características, los clientes ahora pueden ver las señales y crear una directiva para proporcionar más seguridad y acceso a los clientes. [Más información](../../active-directory-b2c/conditional-access-identity-protection-overview.md).

---

### <a name="general-availability---kmsi-and-password-reset-now-in-next-generation-of-user-flows"></a>Disponibilidad general: KMSI y restablecimiento de contraseña ya disponibles en la próxima generación de flujos de usuario

**Tipo:** Nueva característica  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C

La próxima generación de flujos de usuario de B2C ahora permite [mantener la sesión iniciada (KMSI)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) y restablecer las contraseñas. La funcionalidad KMSI permite a los clientes ampliar la duración de la sesión de los usuarios de sus aplicaciones web y nativas mediante el uso de una cookie persistente. Esta característica mantiene la sesión activa incluso cuando el usuario cierra y vuelve a abrir el explorador. La sesión se revoca cuando el usuario cierra sesión. Con el restablecimiento de contraseñas, los usuarios pueden restablecer su contraseña mediante el vínculo "¿Olvidó la contraseña?". Esto también permite al administrador forzar el restablecimiento de la contraseña expirada del usuario en el directorio de Azure AD B2C. [Más información](../../active-directory-b2c/add-password-reset-policy.md?pivots=b2c-user-flow).
 
---

### <a name="general-availability---new-log-analytics-workbook-application-role-assignment-activity"></a>Disponibilidad general: nueva actividad de asignación de roles de aplicación del libro de Log Analytics

**Tipo:** Nueva característica  
**Categoría del servicio:** Administración de acceso de usuarios  
**Funcionalidad del producto:** Administración de derechos
 
Se ha agregado un nuevo libro para mostrar eventos de auditoría de los cambios de asignación de roles de aplicación. [Más información](../governance/entitlement-management-logs-and-reporting.md).

---

### <a name="general-availability---next-generation-azure-ad-b2c-user-flows"></a>Disponibilidad general: próxima generación de flujos de usuario de Azure AD B2C 

**Tipo:** Nueva característica  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C
 
La nueva experiencia simplificada de flujo de usuario ofrece paridad con las características en versión preliminar y es donde se encuentran todas las características nuevas. Los usuarios pueden habilitar nuevas características en el mismo flujo de usuario, lo que reduce la necesidad de crear varias versiones con cada nueva versión de actualización de características. La nueva experiencia de usuario fácil de usar también simplifica la selección y creación de flujos de usuario. Consulte [Creación de flujos de usuario en Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow) para obtener instrucciones sobre cómo usar esta característica. [Más información](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="general-availability---azure-active-directory-threat-intelligence-for-sign-in-risk"></a>Disponibilidad general: Inteligencia sobre amenazas de Azure Active Directory para riesgos de inicio de sesión

**Tipo:** Nueva característica  
**Categoría del servicio:** Protección de identidad  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
Esta nueva detección sirve de método ad-hoc para permitir que nuestros equipos de seguridad le envíen notificaciones y protejan a sus usuarios, elevando el riesgo de su sesión a alto cuando observamos que se está produciendo un ataque. La detección también marcará los inicios de sesión asociados como de riesgo. Esta detección sigue la inteligencia sobre amenazas actual de Azure Active Directory para la detección de riesgo de usuarios con el fin de proporcionar una cobertura completa de los distintos ataques observados por los equipos de seguridad de Microsoft. [Más información](../identity-protection/concept-identity-protection-risks.md#user-linked-detections).
 
---

### <a name="general-availability---conditional-access-named-locations-improvements"></a>Disponibilidad general: mejoras en las ubicaciones con nombre de acceso condicional

**Tipo:** Nueva característica  
**Categoría del servicio:** Acceso condicional  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
La compatibilidad con IPv6 en ubicaciones con nombre ahora está disponible con carácter general. Las actualizaciones incluyen:

- Se ha agregado la funcionalidad para definir intervalos de direcciones IPv6.
- Se ha aumentado el límite de ubicaciones con nombre de 90 a 195.
- Se ha incrementado el límite de intervalos IP por ubicación con nombre de 1200 a 2000.
- Se han agregado funcionalidades para buscar y ordenar ubicaciones con nombre y filtrar por tipo de ubicación y de confianza.
- Se han agregado ubicaciones con nombre a las que pertenece un inicio de sesión en los registros de inicio de sesión.
 
Además, con el fin de evitar que los administradores definan ubicaciones con nombres problemáticos, se han agregado comprobaciones extras para reducir las probabilidades de que haya errores de configuración. [Más información](../conditional-access/location-condition.md).

---

### <a name="general-availability---restricted-guest-access-permissions-in-azure-ad"></a>Disponibilidad general: permisos restringidos de acceso de invitado en Azure AD

**Tipo:** Nueva característica  
**Categoría del servicio:** User Management  
**Funcionalidad del producto:** Directorio
 
Hemos actualizado los permisos del nivel de directorio para los usuarios invitados. Estos permisos permiten a los administradores exigir restricciones y controles adicionales en el acceso de usuarios invitados externos.

Los administradores ahora pueden agregar más restricciones para el acceso de invitados externos a la información de perfil y pertenencia de los usuarios y grupos. Además, los clientes pueden administrar el acceso de los usuarios externos a escala ocultando las pertenencias a grupos, incluida la restricción de los usuarios invitados para ver la pertenencia de los grupos en los que se encuentran. Para obtener más información, consulte [Restricción de los permisos de acceso de invitados en Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD, mayo de 2021

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros
 
Ahora, puede automatizar la creación, actualización y eliminación de cuentas de usuario para estas aplicaciones recién integradas:

- [AuditBoard](../saas-apps/auditboard-provisioning-tutorial.md)
- [Cisco Umbrella User Management](../saas-apps/cisco-umbrella-user-management-provisioning-tutorial.md)
- [Insite LMS](../saas-apps/insite-lms-provisioning-tutorial.md)
- [kpifire](../saas-apps/kpifire-provisioning-tutorial.md)
- [UNIFI](../saas-apps/unifi-provisioning-tutorial.md)

Para obtener más información sobre cómo proteger mejor una organización mediante el aprovisionamiento automatizado de cuentas de usuario, consulte [Automatización del aprovisionamiento de usuarios para aplicaciones SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD, mayo de 2021

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros
 
En mayo de 2021 agregamos las siguientes 29 aplicaciones nuevas a nuestra galería de aplicaciones, con compatibilidad de federación:

[InviteDesk](https://app.invitedesk.com/login), [Webrecruit ATS](https://id-test.webrecruit.co.uk/), [Workshop](../saas-apps/workshop-tutorial.md), [Gravity Sketch](https://landingpad.me/), [JustLogin](../saas-apps/justlogin-tutorial.md), [Custellence](https://custellence.com/sso/), [WEVO](https://hello.wevoconversion.com/login), [AppTec360 MDM](https://www.apptec360.com/ms/autopilot.html), [Filemail](https://www.filemail.com/login),[Ardoq](../saas-apps/ardoq-tutorial.md), [Leadfamly](../saas-apps/leadfamly-tutorial.md), [Documo](../saas-apps/documo-tutorial.md), [Autodesk SSO](../saas-apps/autodesk-sso-tutorial.md), [Check Point Harmony Connect](../saas-apps/check-point-harmony-connect-tutorial.md), [BrightHire](https://app.brighthire.ai/), [Rescana](../saas-apps/rescana-tutorial.md), [Bluewhale](https://cloud.bluewhale.dk/), [AlacrityLaw](../saas-apps/alacritylaw-tutorial.md), [Equisolve](../saas-apps/equisolve-tutorial.md), [Zip](../saas-apps/zip-tutorial.md), [Cognician](../saas-apps/cognician-tutorial.md), [Acra](https://www.acrasuite.com/), [VaultMe](https://app.vaultme.com/#/signIn), [TAP App Security](../saas-apps/tap-app-security-tutorial.md), [Cavelo Office365 Cloud Connector](https://dashboard.prod.cavelodata.com/), [Clebex](../saas-apps/clebex-tutorial.md), [Banyan Command Center](../saas-apps/banyan-command-center-tutorial.md), [Check Point Remote Access VPN](../saas-apps/check-point-remote-access-vpn-tutorial.md) y [LogMeIn](../saas-apps/logmein-tutorial.md)

La documentación de todas las aplicaciones está disponible en https://aka.ms/AppsTutorial.

Para mostrar su aplicación en la galería de aplicaciones de Azure AD, lea los detalles aquí https://aka.ms/AzureADAppRequest.

---

### <a name="improved-conditional-access-messaging-for-android-and-ios"></a>Mensajería de acceso condicional mejorada para Android e iOS

**Tipo:** Característica modificada  
**Categoría del servicio:** Administración y registro de dispositivos  
**Funcionalidad del producto:** Experiencias de usuario final
 

Hemos actualizado el texto de la pantalla "Acceso condicional" que se muestra a los usuarios cuando se bloquea su acceso a los recursos corporativos. Este bloqueo se mantendrá hasta que inscriban su dispositivo en la administración de dispositivos móviles. Estas mejoras se aplican a las plataformas Android e iOS/iPadOS. Se ha cambiado lo siguiente:

- De "Ayúdenos a garantizar la seguridad de su dispositivo." a "Configuración del dispositivo para obtener acceso".
- De "Ha iniciado sesión correctamente, pero su administrador requiere que Microsoft administre su dispositivo para poder acceder a este recurso." a "[Nombre de la organización] requiere que proteja este dispositivo para poder acceder a los datos, los archivos y el correo electrónico de [nombre de la organización]". 
- De "Inscribirse ahora" a "Continuar".

La información que se incluye en [Inscriba su dispositivo empresarial Android](https://support.microsoft.com/topic/enroll-your-android-enterprise-device-d661c82d-fa28-5dfd-b711-6dff41ae83bb) no está actualizada.

---

### <a name="azure-information-protection-service-will-begin-asking-for-consent"></a>El servicio Azure Information Protection comenzará a pedir consentimiento

**Tipo:** Característica modificada  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios
 
El servicio Azure Information Protection inicia la sesión de los usuarios en el inquilino que cifra el documento como parte de proporcionar acceso al documento.  A partir de junio Azure AD comenzará a pedir al usuario su consentimiento cuando este acceso se realice entre organizaciones.  De este modo, se garantiza que el usuario entienda que la organización propietaria del documento recopilará información sobre su persona como parte del acceso al documento. [Más información](/azure/information-protection/known-issues#sharing-external-doc-types-across-tenants).
 
---

### <a name="provisioning-logs-schema-change-impacting-graph-api-and-azure-monitor-integration"></a>Cambio de esquema de registros de aprovisionamiento que afecta a Graph API y a las integraciones de Azure Monitor

**Tipo:** Característica modificada  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Supervisión e informes
 

Los atributos "Action" y "statusInfo" se cambiarán a "provisioningAction" y "provisoiningStatusInfo". Actualice los scripts que ha creado usando los [registros de aprovisionamiento de Graph API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) o [las integraciones de Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). 
 

---

### <a name="new-arm-api-to-manage-pim-for-azure-resources-and-azure-ad-roles"></a>Nueva API de ARM para administrar PIM para recursos de Azure y roles de Azure AD

**Tipo:** Característica modificada  
**Categoría del servicio:** Privileged Identity Management  
**Funcionalidad del producto:** Privileged Identity Management
 
Se ha publicado una versión actualizada de la API de PIM para el rol de recursos de Azure y el rol de Azure AD. La API de PIM para el rol de recursos de Azure está ahora disponible con el estándar de la API de ARM, que se alinea con la API de administración de roles para la asignación normal de roles de Azure. Por otro lado, la API de PIM para los roles de Azure AD también se ha publicado en Graph API, adaptada a las API de unifiedRoleManagement. Estas son algunas de las ventajas de este cambio:

- Alineación de la API de PIM con los objetos de ARM y Graph para la administración de roles, lo que reduce la necesidad de llamar a PIM para incorporar nuevos recursos de Azure. 
- Todos los recursos de Azure funcionan automáticamente con la nueva API de PIM.
- Se ha reducido la necesidad de llamar a PIM para la definición de roles o mantener un identificador de recurso de PIM.
- Se ha agregado compatibilidad con los permisos de API de solo aplicación en PIM para los roles de Azure AD y los roles de recursos de Azure.

La versión anterior de la API de PIM en /privilegedaccess seguirá funcionando, pero se recomienda migrar a esta nueva API en el futuro. [Más información](../privileged-identity-management/pim-apis.md).
 
---

### <a name="revision-of-roles-in-azure-ad-entitlement-management"></a>Revisión de roles en la administración de derechos de Azure AD

**Tipo:** Característica modificada  
**Categoría del servicio:** Roles  
**Funcionalidad del producto:** Administración de derechos
 
Hace poco se ha introducido un nuevo rol: "Administrador de gobernanza de identidades". Este rol sustituirá a "Administrador de usuarios" a la hora de administrar catálogos y paquetes de acceso en la administración de derechos de Azure AD. Si ha asignado administradores al rol "Administrador de usuarios" o les ha hecho activar este rol para administrar paquetes de acceso en la administración de derechos de Azure AD, cambie al rol "Administrador de gobernanza de identidades". El rol Administrador de usuarios ya no proporciona derechos administrativos ni a los catálogos ni a los paquetes de acceso. [Más información](../governance/identity-governance-overview.md#appendix---least-privileged-roles-for-managing-in-identity-governance-features).

---

## <a name="april-2021"></a>Abril de 2021

### <a name="bug-fixed---azure-ad-will-no-longer-double-encode-the-state-parameter-in-responses"></a>Error corregido: Azure AD ya no codificará dos veces el parámetro de estado de las respuestas

**Tipo:** Corregido  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios
 
Azure AD identificó, probó y publicó la corrección para un error en la respuesta `/authorize` a una aplicación cliente.  Azure AD codificaba de manera incorrecta la URL del parámetro `state` dos veces al enviar las respuestas de vuelta al cliente.  Esto puede generar que la aplicación cliente rechace la solicitud debido a una discrepancia en los parámetros de estado. [Más información](../develop/reference-breaking-changes.md#bug-fix-azure-ad-will-no-longer-url-encode-the-state-parameter-twice). 

---

### <a name="users-can-only-create-security-and-microsoft-365-groups-in-azure-portal-being-deprecated"></a>La restricción para que los usuarios solo puedan crear grupos de seguridad y de Microsoft 365 en Azure Portal está en desuso

**Tipo:** Plan de cambio  
**Categoría del servicio:** Administración de grupos  
**Funcionalidad del producto:** Directorio
 
Los usuarios ya no estarán limitados a crear grupos de seguridad y de Microsoft 365 solo en Azure Portal. La configuración nueva permitirá que los usuarios creen grupos de seguridad en Azure Portal, PowerShell y en la API. Los usuarios deberán comprobar y actualizar la configuración nueva. [Más información](../enterprise-users/groups-self-service-management.md).

---

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Versión preliminar pública: registro de autoservicio de External Identities en AAD mediante un código de acceso de un solo uso enviado por correo electrónico

**Tipo:** Nueva característica  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 
Los usuarios externos ahora pueden usar las cuentas de código de acceso de un solo uso de correo electrónico para registrarse o iniciar sesión en las aplicaciones de línea de negocio y de Azure AD de Microsoft. [Más información](../external-identities/one-time-passcode.md).

---

### <a name="general-availability---external-identities-self-service-sign-up"></a>Disponibilidad general: registro de autoservicio de External Identities

**Tipo:** Nueva característica  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 
El registro de autoservicio para usuarios externos ahora está en disponibilidad general. Con esta característica nueva, los usuarios externos ahora pueden autoregistrarse en una aplicación. 

Puede crear experiencias personalizadas para estos usuarios externos, incluida la recopilación de información sobre los usuarios durante el proceso de registro y la posibilidad de permitir proveedores de identidades externos como Facebook y Google. También puede integrarse con proveedores de servicios en la nube de terceros para diversas funcionalidades, como la verificación de identidad o la aprobación de usuarios. [Más información](../external-identities/self-service-sign-up-overview.md).
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Disponibilidad general: registro e inicio de sesión mediante teléfono en Azure AD B2C con una directiva integrada

**Tipo:** Nueva característica  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C
 
El registro e inicio de sesión mediante teléfono en B2C con una directiva integrada permiten a los administradores de TI y desarrolladores de organizaciones dejar que sus usuarios finales inicien sesión y se registren con un número de teléfono en los flujos de usuario. Con esta característica, los vínculos de declinación de responsabilidades, como la directiva de privacidad y las condiciones de uso, se pueden personalizar y mostrar en la página antes de que el usuario final siga recibiendo el código de acceso de un solo uso a través de un mensaje de texto. [Más información](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---april-2021"></a>Aplicaciones federadas nuevas disponibles en la galería de aplicaciones de Azure AD, abril de 2021

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros

En abril de 2021, agregamos 31 aplicaciones nuevas a la galería de aplicaciones con compatibilidad de federación

[Zii Travel Azure AD Connect](http://ziitravel.com/), [Cerby](../saas-apps/cerby-tutorial.md), [Selflessly](https://app.selflessly.io/sign-in), [Apollo CX](https://apollo.cxlabs.de/sso/aad), [Pedagoo](https://account.pedagoo.com/), [Measureup](https://account.measureup.com/), [Wistec Education](https://wisteceducation.fi/login/index.php), [ProcessUnity](../saas-apps/processunity-tutorial.md), [Cisco Intersight](../saas-apps/cisco-intersight-tutorial.md), [Codility](../saas-apps/codility-tutorial.md), [H5mag](https://account.h5mag.com/auth/request-access/ms365), [Check Point Identity Awareness](../saas-apps/check-point-identity-awareness-tutorial.md), [Jarvis](https://jarvis.live/login), [desknet's NEO](../saas-apps/desknets-neo-tutorial.md), [SDS & Chemical Information Management](../saas-apps/sds-chemical-information-management-tutorial.md), [Wúru App](../saas-apps/wuru-app-tutorial.md), [Holmes](../saas-apps/holmes-tutorial.md), [Tide Multi Tenant](https://gallery.tideapp.co.uk/), [Telenor](https://admin.smartansatt.telenor.no/), [Yooz US](https://us1.getyooz.com/?kc_idp_hint=microsoft), [Mooncamp](https://app.mooncamp.com/#/login), [inwise SSO](https://app.inwise.com/defaultsso.aspx), [Ecolab Digital Solutions](https://ecolabb2c.b2clogin.com/account.ecolab.com/oauth2/v2.0/authorize?p=B2C_1A_Connect_OIDC_SignIn&client_id=01281626-dbed-4405-a430-66457825d361&nonce=defaultNonce&redirect_uri=https://jwt.ms&scope=openid&response_type=id_token&prompt=login), [Taguchi Digital Marketing System](https://login.taguchi.com.au/), [XpressDox EU Cloud](https://test.xpressdox.com/Authentication/Login.aspx), [EZSSH](https://docs.keytos.io/getting-started/registering-a-new-tenant/registering_app_in_tenant/), [EZSSH Client](https://portal.ezssh.io/signup), [Verto 365](https://www.vertocloud.com/Login/), [KPN Grip](https://www.grip-on-it.com/), [AddressLook](https://portal.bbsonlineservices.net/Manage/AddressLook), [Cornerstone Single Sign-On](../saas-apps/cornerstone-ondemand-tutorial.md)

También puede consultar la documentación de todas ellas aquí: https://aka.ms/AppsTutorial.

Para incluir su aplicación en la galería de aplicaciones de Azure AD, lea los detalles aquí: https://aka.ms/AzureADAppRequest.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---april-2021"></a>Conectores de aprovisionamiento nuevos disponibles en la galería de aplicaciones de Azure AD, abril de 2021

**Tipo:** Nueva característica  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Integración de terceros
 
Ahora, puede automatizar la creación, actualización y eliminación de cuentas de usuario para estas aplicaciones recién integradas:

- [Bentley - Automatic User Provisioning](../saas-apps/bentley-automatic-user-provisioning-tutorial.md)
- [Boxcryptor](../saas-apps/boxcryptor-provisioning-tutorial.md)
- [Inicio de sesión único de BrowserStack](../saas-apps/browserstack-single-sign-on-provisioning-tutorial.md)
- [Eletive](../saas-apps/eletive-provisioning-tutorial.md)
- [Jostle](../saas-apps/jostle-provisioning-tutorial.md)
- [Olfeo SAAS](../saas-apps/olfeo-saas-provisioning-tutorial.md)
- [Proware](../saas-apps/proware-provisioning-tutorial.md)
- [Segmento](../saas-apps/segment-provisioning-tutorial.md)

Para más información sobre cómo proteger mejor la organización a través del aprovisionamiento automatizado de cuentas de usuario, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---

### <a name="introducing-new-versions-of-page-layouts-for-b2c"></a>Introducción de versiones nuevas de diseños de página para B2C

**Tipo:** Característica modificada  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C
 
Los [diseños de página](../../active-directory-b2c/page-layout.md) para escenarios de B2C en Azure AD B2C se actualizaron a fin de reducir los riesgos de seguridad al introducir las versiones nuevas de jQuery y Handlebars JS.
 
---

### <a name="updates-to-sign-in-diagnostic"></a>Actualizaciones del diagnóstico de inicio de sesión

**Tipo:** Característica modificada  
**Categoría del servicio:** Notificación  
**Funcionalidad del producto:** Supervisión e informes
 
La cobertura de escenarios de la herramienta de diagnóstico de inicio de sesión ha aumentado. 

Con esta actualización, los escenarios siguientes relacionados con eventos ahora se incluirán en los resultados del diagnóstico de inicio de sesión: 
- Eventos de problemas de configuración de aplicaciones empresariales.
- Eventos del proveedor de servicios de aplicaciones empresariales (lado de la aplicación).
- Eventos de credenciales incorrectas. 

Estos resultados mostrarán detalles contextuales y pertinentes sobre el evento y las acciones que se deben realizar para resolver estos problemas. Además, en escenarios en los que no tenemos diagnósticos contextuales profundos, el diagnóstico de inicio de sesión presentará contenido más descriptivo sobre el evento de error.

Para más información, consulte [¿Qué es el diagnóstico de inicio de sesión en Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md)

---
### <a name="azure-ad-connect-cloud-sync-general-availability-refresh"></a>Actualización de la disponibilidad general de Azure AD Connect Cloud Sync 
**Tipo:** Característica modificada  
**Categoría del servicio:** Azure AD Connect Cloud Sync **Funcionalidad del producto:** Directorio

Azure AD Connect Cloud Sync ahora tiene un agente actualizado (n.° de versión 1.1.359). Para más información sobre las actualizaciones del agente, incluidas las correcciones de errores, consulte el [historial de versiones](../cloud-sync/reference-version-history.md). Con el agente actualizado, los clientes de la sincronización en la nube pueden usar cmdlets de GMSA para establecer y restablecer su permiso de gMSA en un nivel detallado. Además, cambiamos el límite de sincronización de los miembros mediante el filtrado del ámbito de grupo de 1499 a 50 000 miembros. 

Consulte el [generador de expresiones](../cloud-sync/how-to-expression-builder.md#deploy-the-expression) recién disponible para la sincronización en la nube, que lo ayuda a crear expresiones complejas y expresiones sencillas cuando realiza transformaciones de valores de atributo de AD a Azure AD mediante la asignación de atributos.

---

## <a name="march-2021"></a>Marzo de 2021

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Instrucciones sobre cómo habilitar la compatibilidad con TLS 1.2 en su entorno, en preparación para el próximo desuso de Azure AD TLS 1.0/1.1.

**Tipo:** Plan de cambio  
**Categoría del servicio:** N/D  
**Funcionalidad del producto:** Estándares

Azure Active Directory dejará de usar los siguientes protocolos en las regiones de todo el mundo de Azure Active Directory a partir del 30 de junio de 2021:


- TLS 1.0
- TLS 1.1
- Conjunto de cifrado 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Los entornos afectados son:

- Nube comercial de Azure
- Office 365 GCC y WW

Para más información, consulte [Habilitar la compatibilidad con TLS 1.2 en el entorno para el desuso de TLS 1.1 y 1.0 de Azure AD](/troubleshoot/azure/active-directory/enable-support-tls-environment).

---

### <a name="public-preview----azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Versión preliminar pública: la administración de derechos de Azure AD ahora admite SharePoint Online con varias ubicaciones geográficas

**Tipo:** Nueva característica  
**Categoría del servicio:** Otros  
**Funcionalidad del producto:** Administración de derechos
 
En el caso de las organizaciones que usan SharePoint Online con múltiples geografías, ahora puede incluir sitios de varios entornos geográficos específicos en los paquetes de acceso de administración de derechos. [Más información](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site).

---

### <a name="public-preview----restore-deleted-apps-from-app-registrations"></a>Versión preliminar pública: restauración de aplicaciones eliminadas de Registros de aplicaciones

**Tipo:** Nueva característica  
**Categoría del servicio:** Otros  
**Funcionalidad del producto:** Experiencia para el desarrollador
 
Los clientes ahora pueden ver, restaurar y quitar permanentemente los registros de aplicaciones eliminados de Azure Portal. Esto solo se aplica a las aplicaciones asociadas a un directorio, no a las aplicaciones de un cuenta personal de Microsoft. [Más información](../develop/howto-restore-app.md).
 
---

### <a name="public-preview----new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Versión preliminar pública: nueva acción de usuario en acceso condicional para registrar o unir dispositivos

**Tipo:** Nueva característica  
**Categoría del servicio:** Acceso condicional  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
 Hay disponible una nueva acción de usuario denominada "Registrar o unir dispositivos" en el acceso condicional. Esta acción de usuario le permite controlar directivas de autenticación multifactor (MFA) para el registro de dispositivos de Azure AD. 

Actualmente, esta acción del usuario solo permite habilitar MFA como un control cuando los usuarios registran o unen dispositivos a Azure AD. Otros controles que dependen del registro de dispositivos de Azure AD, o que no se aplican, se deshabilitan con esta acción del usuario. [Más información](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 
 
---

### <a name="public-preview----optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Versión preliminar pública: optimización de los grupos de conectores para usar el servicio en la nube de Application Proxy más cercano

**Tipo:** Nueva característica  
**Categoría del servicio:** Proxy de aplicaciones  
**Funcionalidad del producto:** Control de acceso
 
Con esta nueva funcionalidad, los grupos de conectores se pueden asignar al servicio de Application Proxy regional más cercano en el que se hospeda una aplicación. Esto puede mejorar el rendimiento de la aplicación en escenarios en los que las aplicaciones se hospedan en regiones distintas de la región de inicio del inquilino. [Más información](../app-proxy/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). 
 
---

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Versión preliminar pública: registro de autoservicio de External Identities en AAD mediante un código de acceso de un solo uso enviado por correo electrónico

**Tipo:** Nueva característica  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C

Ahora, los usuarios externos podrán usar las cuentas de código de acceso de un solo uso de correo electrónico para registrarse en las aplicaciones de Azure AD de Microsoft y de LOB. [Más información](../external-identities/one-time-passcode.md).

---

### <a name="public-preview----availability-of-ad-fs-sign-ins-in-azure-ad"></a>Versión preliminar pública: disponibilidad de inicios de sesión de AD FS en Azure AD

**Tipo:** Nueva característica  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Supervisión e informes
 
La actividad de inicio de sesión de AD FS ahora se puede integrar con los informes de actividad de Azure AD, lo que proporciona una vista unificada de la infraestructura de identidades híbridas. Con el informe de inicio de sesión de Azure AD, Log Analytics y los libros de Azure Monitor, es posible realizar un análisis exhaustivo de los escenarios de inicio de sesión de AAD y AD FS como bloqueos de cuentas de AD FS, intentos incorrectos de contraseña y aumentos de los intentos de inicio de sesión inesperados.

Para más información, consulte [Inicios de sesión de AD FS en Azure AD con Connect Health (versión preliminar)](../hybrid/how-to-connect-health-ad-fs-sign-in.md).

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Disponibilidad general: lanzamiento preconfigurado para la autenticación en la nube

**Tipo:** Nueva característica  
**Categoría del servicio:** AD Connect  
**Funcionalidad del producto:** Autenticación de usuarios
 
El lanzamiento preconfigurado en la autenticación en la nube ya está disponible con carácter general. La característica del lanzamiento preconfigurado permite probar de forma selectiva grupos de usuarios con métodos de autenticación en la nube, como la autenticación de paso a través (PTA) o la sincronización de hash de contraseña (PHS). Mientras tanto, todos los demás usuarios de los dominios federados siguen usando los servicios de federación, como AD FS o cualquier otro servicio de federación para autenticar a los usuarios. [Más información](../hybrid/how-to-connect-staged-rollout.md).

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Disponibilidad general: el atributo de tipo de usuario ahora se puede actualizar en el portal de administración de Azure

**Tipo:** Nueva característica  
**Categoría de servicio:** Experiencia y administración de usuarios  
**Funcionalidad del producto:** User Management
 
Los clientes ahora pueden actualizar el tipo de usuario de Azure AD cuando actualicen su información de perfil de usuario desde el portal de administración de Azure. También se puede actualizar el tipo de usuario en Microsoft Graph. Para más información, consulte [Adición o actualización de la información de perfil de un usuario mediante Azure Active Directory](active-directory-users-profile-azure-portal.md).
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Disponibilidad general: conjuntos de réplicas para Azure Active Directory Domain Services

**Tipo:** Nueva característica  
**Categoría del servicio:** Azure AD Domain Services  
**Funcionalidad del producto:** Azure AD Domain Services
 
La funcionalidad de los conjuntos de réplicas en Azure AD DS ahora está disponible con carácter general. [Más información](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Disponibilidad general: colaboración con los asociados mediante el código de acceso de un solo uso de correo electrónico en la nube de Azure Government

**Tipo:** Nueva característica  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 
Ahora, las organizaciones de la nube de Microsoft Azure Government pueden permitir a sus invitados canjear las invitaciones por el código de acceso de un solo uso de correo electrónico. Esto garantiza que todos los usuarios invitados que no tengan cuentas de Azure AD, Microsoft o Gmail en la nube de Azure Government puedan colaborar con los asociados solicitando y escribiendo un código temporal para iniciar sesión en los recursos compartidos. [Más información](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD, marzo de 2021

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros
 
En marzo de 2021, agregamos 37 aplicaciones nuevas a la galería de aplicaciones con compatibilidad de federación:

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc](https://www.deepdyve.com/azure-sso), [Moqups](../saas-apps/moqups-tutorial.md), [RICOH Spaces Mobile](https://ricohspaces.app/welcome), [Flipgrid](https://auth.flipgrid.com/), [hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI](https://app.simplificaci.com.br/), [Thrive LXP](../saas-apps/thrive-lxp-tutorial.md), [Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md), [Exium](../saas-apps/exium-tutorial.md), [Sapient](../saas-apps/sapient-tutorial.md), [TrueChoice](../saas-apps/truechoice-tutorial.md), [RICOH Spaces](https://ricohspaces.app/welcome), [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), [Enterprise Vault.cloud para Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData](https://app.clicdata.com/), [Evergreen](../saas-apps/evergreen-tutorial.md), [Patchdeck](https://patchdeck.com/ad_auth/authenticate/), [FAX.PLUS](../saas-apps/fax-plus-tutorial.md), [ValidSign](../saas-apps/validsign-tutorial.md), [AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md), [Nura Space](https://dashboard.nuraspace.com/login), [Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

También puede consultar la documentación de todas ellas aquí: https://aka.ms/AppsTutorial.

Para incluir su aplicación en la galería de aplicaciones de Azure AD, lea los detalles aquí: https://aka.ms/AzureADAppRequest.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Nuevos conectores de aprovisionamiento disponibles en la galería de aplicaciones de Azure AD, marzo de 2021

**Tipo:** Nueva característica  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Integración de terceros

Ahora, puede automatizar la creación, actualización y eliminación de cuentas de usuario para estas aplicaciones recién integradas:

- [Inicio de sesión único de AWS](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

Para más información acerca de cómo proteger mejor una organización mediante el aprovisionamiento automatizado de cuentas de usuario, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Introducción a MS Graph API para la personalización de marca de empresa

**Tipo:** Característica modificada  
**Categoría del servicio:** MS Graph  
**Funcionalidad del producto:** B2B/B2C

[MS Graph API para la personalización de marca de empresa](/graph/api/resources/organizationalbrandingproperties) está disponible para la experiencia de inicio de sesión Azure AD o de Microsoft 365 para permitir la administración de los parámetros de personalización de marca mediante programación.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Disponibilidad general: inicio de sesión único de autenticación basada en encabezados con proxy de aplicación

**Tipo:** Característica modificada  
**Categoría del servicio:** Proxy de aplicaciones  
**Funcionalidad del producto:** Control de acceso
 
Ahora está disponible con carácter general la compatibilidad nativa del proxy de aplicaciones de Azure AD con la autenticación basada en encabezados. Con esta característica, puede configurar los atributos de usuario necesarios como encabezados HTTP para la aplicación sin necesidad de componentes adicionales para la implementación. [Más información](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md).

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>Ya no se admite el servidor de SMS bidireccional para MFA

**Tipo:** Obsoleto  
**Categoría del servicio:** MFA  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 

El servidor SMS bidireccional para el servidor de MFA se dejó de utilizar originalmente en 2018, y ya no tiene soporte técnico desde el 24 de febrero de 2021. Los administradores deben habilitar otro método para los usuarios que siguen usando el SMS bidireccional.

Se enviaron notificaciones por correo electrónico y de Service Health en Azure Portal a los administradores afectados el 8 de diciembre de 2020 y el 28 de enero de 2021. Las alertas se dirigieron a los roles RBAC de propietario, copropietario, administrador y administrador de servicios vinculados a las suscripciones. [Más información](../authentication/how-to-authentication-two-way-sms-unsupported.md).
 
---
 
## <a name="february-2021"></a>Febrero de 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>La autenticación con código de acceso de un solo uso por correo electrónico estará activada de manera predeterminada a partir de octubre de 2021

**Tipo:** Plan de cambio  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 

A partir del 31 de octubre de 2021, la [autenticación con código de acceso de un solo uso por correo electrónico](../external-identities/one-time-passcode.md) de Microsoft Azure Active Directory se convertirá en el método predeterminado para invitar a cuentas e inquilinos en escenarios de colaboración B2B. En ese momento, Microsoft ya no permitirá el canje de invitaciones mediante cuentas de Azure Active Directory no administradas. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Los permisos no solicitados pero consentidos ya no se agregarán a los tokens si esto supone desencadenar el acceso condicional

**Tipo:** Plan de cambio  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** plataforma
 
Actualmente, las aplicaciones que usan [permisos dinámicos](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) reciben todos los permisos a los que se les consiente el acceso. Esto incluye las aplicaciones que no se han solicitado, incluso si desencadenan el acceso condicional. Por ejemplo, esto puede dar lugar a que una aplicación que solicite únicamente el permiso `user.read` y tenga también el consentimiento para el permiso `files.read` deba pasar el acceso condicional asignado al permiso `files.read`. 

Para reducir el número de mensajes de acceso condicional innecesarios, Azure AD está cambiando la forma en que se proporcionan los ámbitos no solicitados a las aplicaciones. Las aplicaciones solo desencadenarán el acceso condicional correspondiente al permiso que solicitan explícitamente. Para obtener más información, consulte [Novedades en la autenticación](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes).
 
---
 
### <a name="public-preview----use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Versión preliminar pública: uso de un Pase de acceso temporal para registrar credenciales sin contraseña

**Tipo:** Nueva característica  
**Categoría del servicio:** MFA  
**Funcionalidad del producto:** Seguridad y protección de la identidad

El pase de acceso temporal es un código de acceso de tiempo limitado que sirve como credencial segura y permite la incorporación de credenciales sin contraseña y su recuperación cuando un usuario ha perdido u olvidado su factor de autenticación sólida (por ejemplo, una clave de seguridad FIDO2 o la aplicación Microsoft Authenticator) y debe iniciar sesión para registrar nuevos métodos de autenticación sólida. [Más información](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview----keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Versión preliminar pública: mantenimiento de la sesión iniciada (KMSI) en la nueva generación de flujos de usuario

**Tipo:** Nueva característica  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C

La próxima generación de flujos de usuario de B2C ahora es compatible con la funcionalidad de [mantenimiento de la sesión iniciada (KMSI)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) que permite a los clientes ampliar la duración de la sesión de los usuarios de sus aplicaciones web y nativas mediante el uso de una cookie persistente.  La característica mantiene la sesión activa incluso cuando el usuario cierra y vuelve a abrir el explorador y la revoca cuando el usuario cierra sesión.

---

### <a name="public-preview----reset-redemption-status-for-a-guest-user"></a>Versión preliminar pública: restablecimiento del estado de canje de un usuario invitado

**Tipo:** Nueva característica  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 
Ahora los clientes pueden volver a invitar a los usuarios externos invitados existentes a restablecer su estado de canje, lo que permite que la cuenta de usuario invitado se mantenga sin que se pierda ningún acceso. [Más información](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview----synchronization-provisioning-apis-now-support-application-permissions"></a>Versión preliminar pública: las API de sincronización (aprovisionamiento) ahora admiten permisos de aplicación

**Tipo:** Nueva característica  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Administración del ciclo de vida de la identidad
 
Ahora los clientes pueden usar application.readWrite.ownedby como permiso de aplicación para llamar a las API de sincronización. Tenga en cuenta que esta opción solo se admite para el aprovisionamiento de Azure AD a aplicaciones de terceros (por ejemplo, AWS, Data Bricks, etc.). Actualmente no se admite en el aprovisionamiento de recursos humanos (WorkDay/SuccessFactors) ni en la sincronización en la nube (AD a Azure AD). [Más información](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Disponibilidad general: rol integrado "Administrador de directivas de autenticación"

**Tipo:** Nueva característica  
**Categoría del servicio:** RBAC  
**Funcionalidad del producto:** Control de acceso
 
Los usuarios con este rol pueden configurar la directiva de métodos de autenticación, la configuración de MFA para todos los inquilinos y la directiva de protección de contraseñas. Este rol concede permiso para administrar la configuración de protección de contraseñas: configuraciones de bloqueo inteligente y actualización de la lista de contraseñas prohibidas personalizadas. [Más información](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Disponibilidad general: ahora las colecciones de los usuarios están disponibles en Mis aplicaciones

**Tipo:** Nueva característica  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** Experiencias de usuario final
 
Ahora los usuarios pueden crear sus propias agrupaciones de aplicaciones en el iniciador Mis aplicaciones. También pueden reordenar y ocultar colecciones compartidas con ellos por su administrador. [Más información](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Disponibilidad general: autorrellenado en Authenticator

**Tipo:** Nueva característica  
**Categoría de servicio:** Aplicación Microsoft Authenticator  
**Funcionalidad del producto:** Seguridad y protección de la identidad
 
Microsoft Authenticator proporciona funciones de autenticación multifactor (MFA) y de administración de cuentas. Además, ahora también rellenará automáticamente las contraseñas de los sitios y las aplicaciones que los usuarios visiten en sus dispositivos móviles (iOS y Android). 

Para usar el autorrellenado en Authenticator, los usuarios deben agregar sus cuenta de Microsoft personales en esta aplicación y usarla para sincronizar las contraseñas. Por el momento, no se pueden usar las cuentas profesionales ni educativas para sincronizar contraseñas. [Más información](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Disponibilidad general: invitación a usuarios internos para la colaboración B2B

**Tipo:** Nueva característica  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C
 
Ahora los clientes pueden invitar a los invitados internos a usar la colaboración B2B en lugar de enviar una invitación a una cuenta interna existente. Esto permite a los clientes mantener el identificador de objeto, el UPN, la pertenencia a grupos y las asignaciones de aplicaciones del usuario. [Más información](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Disponibilidad general: rol integrado "Administrador de nombres de dominio"

**Tipo:** Nueva característica  
**Categoría del servicio:** RBAC  
**Funcionalidad del producto:** Control de acceso
 
Los usuarios con este rol pueden administrar (leer, agregar, comprobar, actualizar y eliminar) los nombres de dominio. También pueden leer información de directorio sobre usuarios, grupos y aplicaciones, ya que estos objetos tienen dependencias de dominio. 

En entornos locales, los usuarios con este rol pueden configurar nombres de dominio para la federación, de modo que los usuarios asociados siempre se autentiquen de forma local. Estos usuarios pueden iniciar sesión en servicios basados en Azure AD con sus contraseñas locales a través del inicio de sesión único. La configuración de federación se debe sincronizar a través de Azure AD Connect, por lo que los usuarios también tienen permisos para administrar Azure AD Connect. [Más información](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD (febrero de 2021)

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros
 
En febrero de 2021, agregamos 37 aplicaciones nuevas a la galería de aplicaciones con compatibilidad de federación:

[Extensión de Loop Messenger](https://loopworks.com/loop-flow-messenger/), [Adaptador de Silverfort para Azure AD](http://www.silverfort.com/), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus Pre- and Onboarding Platform](http://app.introdus.dk/login), [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), [Fenwick Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS y TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT Bridge](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [Rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot - Car park management](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung Knox and Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [plataforma de administración de permisos de CloudKnox](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), [Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md), [Enablon](../saas-apps/enablon-tutorial.md)

También puede consultar la documentación de todas ellas aquí: https://aka.ms/AppsTutorial.

Para incluir su aplicación en la galería de aplicaciones de Azure AD, lea los detalles aquí: https://aka.ms/AzureADAppRequest.

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Nuevos conectores de aprovisionamiento en la galería de aplicaciones de Azure AD (febrero de 2021)

**Tipo:** Nueva característica  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Integración de terceros
 

Ahora, puede automatizar la creación, actualización y eliminación de cuentas de usuario para estas aplicaciones recién integradas:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Para más información, consulte [Qué es el aprovisionamiento automatizado de usuarios de aplicaciones SaaS en Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Disponibilidad general: cambio de nombre de diez roles de Azure Active Directory

**Tipo:** Característica modificada  
**Categoría del servicio:** RBAC  
**Funcionalidad del producto:** Control de acceso
 
Se ha cambiado el nombre de 10 roles integrados de Azure AD para que estén alineados en el [centro de administración de Microsoft 365](/microsoft-365/admin/microsoft-365-admin-center-preview), [el portal de Azure AD](https://portal.azure.com/) y [Microsoft Graph](https://developer.microsoft.com/graph/). Para más información sobre los nuevos roles, consulte [Permisos de roles de administrador en Azure Active Directory](../roles/permissions-reference.md#all-roles).

![Tabla que muestra los nombres de rol en MS Graph API y Azure Portal, y el nombre final propuesto en la API, Azure Portal y Mac.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Nueva personalización de marca de la empresa en el registro combinado de MFA/SSPR

**Tipo:** Característica modificada  
**Categoría de servicio:** Experiencia y administración de usuarios  
**Funcionalidad del producto:** Experiencias de usuario final
 
En el pasado, no se usaban los logotipos de empresa en las páginas de inicio de sesión de Azure Active Directory. Ahora, la personalización de marca de la empresa se encuentra en la parte superior izquierda del registro combinado de MFA/SSPR. La personalización de marca de la empresa también se incluye en Mis inicios de sesión y en la página de información de seguridad. [Más información](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Disponibilidad general: el administrador de segundo nivel se puede establecer como aprobador alternativo

**Tipo:** Característica modificada  
**Categoría del servicio:** Administración de acceso de usuarios  
**Funcionalidad del producto:** Administración de derechos
 
Ahora, en la administración de derechos, se encuentra disponible una opción adicional al seleccionar aprobadores. Si selecciona "Administrador como aprobador" para el primer aprobador, tendrá otra opción disponible para elegir en el campo de aprobador alternativo, "Administrador de segundo nivel como aprobador alternativo". Si selecciona esta opción, debe agregar un aprobador de reserva al que reenviar la solicitud en caso de que el sistema no encuentre el administrador de segundo nivel. [Más información](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Panel Actividad de los métodos de autenticación

**Tipo:** Característica modificada  
**Categoría del servicio:** Notificación  
**Funcionalidad del producto:** Supervisión e informes
 

El panel Actividad de los métodos de autenticación actualizado proporciona a los administradores una visión general de la actividad de registro y uso de los métodos de autenticación en su inquilino. El informe resume el número de usuarios registrados para cada método y los métodos que se usan durante el inicio de sesión y el restablecimiento de contraseña. [Más información](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>Se retira la capacidad de configuración de la vigencia de los tokens de sesión y actualización en la directiva de vigencia configurable del token

**Tipo:** Obsoleto  
**Categoría del servicio:** Otros  
**Funcionalidad del producto:** Autenticación de usuarios
 
Se retira la capacidad de configuración de la vigencia de los tokens de sesión y actualización en la directiva de vigencia configurable del token. Azure Active Directory ya no respeta la configuración de los tokens de actualización y de sesión en las directivas existentes. [Más información](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---