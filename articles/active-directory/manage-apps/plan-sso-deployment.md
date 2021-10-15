---
title: Planeación de una implementación de inicio de sesión único
description: Planee la implementación del inicio de sesión único en Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: d6daa398c560bf7112539291f76fe4188abb76a1
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231622"
---
# <a name="plan-a-single-sign-on-deployment-in-azure-active-directory"></a>Planificación de una implementación de inicio de sesión único en Azure Active Directory

En este artículo se proporciona información que puede usar para planear la implementación del [inicio de sesión único (SSO)](what-is-single-sign-on.md) en Azure Active Directory (Azure AD). Al planear la implementación del SSO con sus aplicaciones de Azure AD, debe tener en cuenta estas preguntas:

- ¿Cuáles son los roles administrativos necesarios para administrar la aplicación?
- ¿Es necesario renovar el certificado?
- ¿Quién debe recibir una notificación de los cambios relacionados con la implementación del inicio de sesión único?
- ¿Qué licencias son necesarias para garantizar una administración eficaz de la aplicación?
- ¿Se usan cuentas de usuario compartidas para acceder a la aplicación?
- ¿Conozco las opciones de implementación del SSO?

## <a name="administrative-roles"></a>Roles administrativos

Use siempre el rol con los permisos mínimos disponibles para realizar la tarea requerida en Azure AD. Revise los distintos roles disponibles y elija el correcto para resolver las necesidades para cada rol de la aplicación. Es posible que algunos roles se tengan que aplicar temporalmente y quitar una vez completada la implementación.

| Persona | Roles | Rol de Azure AD (si es necesario) |
| ------- | ----- | --------------------------- |
| Administrador del departamento de soporte técnico | Soporte técnico de nivel 1 | None |
| Administración de identidades | Configuración y depuración cuando hay problemas que afectan a Azure AD | Administrador global |
| Administrador de aplicaciones | Atestación de usuarios en la aplicación, configuración en usuarios con permisos | None |
| Administradores de infraestructura | Propietario de sustitución del certificado | Administrador global |
| Propietario de la empresa o parte interesada | Atestación de usuarios en la aplicación, configuración en usuarios con permisos | None |

Para más información sobre los roles administrativos de Azure AD, consulte [Roles integrados de Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

## <a name="certificates"></a>Certificados

Al habilitar el inicio de sesión único federado para la aplicación, Azure AD crea un certificado con una validez predeterminada de tres años. Si es necesario, puede personalizar la fecha de expiración de ese certificado. Asegúrese de que tiene procesos implantados para renovar los certificados antes de que caduquen. 

Puede cambiar esa duración del certificado en Azure Portal. Asegúrese de documentar la expiración y saber cómo administrará la renovación de certificados. Es importante identificar los roles adecuados y las listas de distribución de correo electrónico encargadas de administrar el ciclo de vida del certificado de firma. Se recomiendan los siguientes roles:

- Propietario para actualizar las propiedades de usuario en la aplicación
- Propietario de guardia para proporcionar asistencia para la solución de problemas de la aplicación
- Lista de distribución de correo electrónico supervisada con detalle para notificaciones de cambios relacionados con el certificado

Puede usar los siguientes métodos para administrar sus certificados:

- **Sustitución automática de certificados**: Azure AD admite la sustitución de claves de firma. Aunque la sustitución de claves de firma es el método preferido para administrar certificados, no todas las aplicaciones admiten este escenario.

- **Actualización manual**: cada aplicación tiene su propio certificado que expira en función de cómo se haya definido. Antes de que expire el certificado de la aplicación, cree uno nuevo y envíelo al proveedor de la aplicación. Esta información se puede extraer de los metadatos de federación. Para más información, consulte [Metadatos de federación](../azuread-dev/azure-ad-federation-metadata.md). 

Configure un proceso para controlar un cambio de certificado entre Azure AD y la aplicación. Este proceso puede ayudar a evitar o minimizar una interrupción debido a la expiración de un certificado o a una sustitución forzada de certificados. Para más información, consulte [Manage certificates for federated single sign-on in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) (Administración de certificados para inicio de sesión único federado en Azure Active Directory).

## <a name="communications"></a>Comunicaciones

La comunicación es fundamental para el éxito de cualquier servicio nuevo. Comuníquese de forma proactiva con los usuarios sobre cómo cambiará su experiencia. Comuníqueles cuándo cambiará y cómo obtener soporte técnico si tienen problemas. Revise las opciones sobre cómo accederán los usuarios a sus aplicaciones habilitadas para el inicio de sesión único y elabore las comunicaciones de forma que coincidan con la selección.

Implemente el plan de comunicación. Asegúrese de que permite a los usuarios saber que se va a producir un cambio, cuándo se ha producido y qué hacer a continuación. Además, asegúrese de proporcionar información sobre cómo obtener ayuda.

## <a name="licensing"></a>Licencias

Asegúrese de que la aplicación está cubierta por los siguientes requisitos de licencia:

- **Licencias de Azure AD**: el inicio de sesión único para las aplicaciones empresariales preintegradas es gratuito. Pero es posible que el número de objetos en el directorio y las características que quiere implementar requieran licencias adicionales. Para obtener una lista completa de los requisitos de licencia, vea [Precios de Azure Active Directory](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

- **Licencias de aplicaciones**: necesitará las licencias adecuadas para las aplicaciones con el fin de satisfacer las necesidades empresariales. Trabaje con el propietario de la aplicación para determinar si los usuarios asignados a la aplicación tienen las licencias adecuadas para sus roles dentro de ella. Si Azure AD administra el aprovisionamiento automático en función de los roles, los roles asignados en Azure AD se deben alinear con el número de licencias que posee dentro de la aplicación. Un número incorrecto de las licencias que se poseen en la aplicación puede dar lugar a errores durante el aprovisionamiento o la actualización de una cuenta de usuario.

## <a name="shared-accounts"></a>Cuentas compartidas

Desde la perspectiva del inicio de sesión, las aplicaciones con cuentas compartidas no son diferentes de las aplicaciones empresariales en las que se usa el inicio de sesión único con contraseña para usuarios individuales. Pero es necesario realizar más pasos al planear y configurar una aplicación diseñada para usar cuentas compartidas.
- Trabaje con los usuarios para documentar la siguiente información:
    - El conjunto de usuarios de la organización que van a usar la aplicación.
    - El conjunto existente de credenciales en la aplicación asociadas con el conjunto de usuarios.
- Para cada combinación de conjunto de usuarios y credenciales, cree un grupo de seguridad local en la nube según los requisitos.
- Restablezca las credenciales compartidas. Una vez implementada la aplicación en Azure AD, los usuarios no necesitan la contraseña de la cuenta compartida. Azure AD almacena la contraseña, por lo que debería considerar la posibilidad de establecer una larga y compleja.
- Si la aplicación lo admite, configure la sustitución automática de la contraseña. De ese modo, ni siquiera el administrador que ha realizado la instalación inicial conoce la contraseña de la cuenta compartida.

## <a name="single-sign-on-options"></a>Opciones de inicio de sesión único

Hay varias maneras de configurar una aplicación para el inicio de sesión único. La elección de un método de SSO depende de cómo esté configurada la aplicación para la autenticación.
- Las aplicaciones en la nube pueden usar los métodos OpenID Connect, OAuth, SAML, basado en contraseña o vinculado para el inicio de sesión único. El inicio de sesión único también se puede deshabilitar.
- Las aplicaciones locales pueden usar métodos de inicio de sesión único basado en contraseña, de autenticación integrada de Windows, basado en encabezados o vinculado para el inicio de sesión único. Las opciones locales funcionan si las aplicaciones están configuradas para el [proxy de aplicación](../app-proxy/what-is-application-proxy.md).

Este diagrama de flujo puede ayudarle a decidir qué método de inicio de sesión único es el mejor en su caso.

![Diagrama de flujo de decisiones sobre el método de inicio de sesión único](./media/plan-sso-deployment/single-sign-on-options.png)
 
Están disponibles para su uso los siguientes protocolos de inicio de sesión único:

- **OpenID Connect y OAuth**: elija OpenID Connect y OAuth 2.0 si la aplicación a la que se va a conectar lo admite. Para más información, consulte [Protocolos OAuth 2.0 y OpenID Connect en la Plataforma de identidad de Microsoft](../develop/active-directory-v2-protocols.md). Para ver los pasos para implementar el inicio de sesión único de OpenID Connect, consulte el artículo [Configuración del inicio de sesión único basado en OIDC de una aplicación en Azure Active Directory](add-application-portal-setup-oidc-sso.md).

- **SAML**: elija SAML siempre que sea posible para las aplicaciones existentes que no usan OpenID Connect o OAuth. Para más información, consulte [Protocolo SAML de inicio de sesión único](../develop/single-sign-on-saml-protocol.md). Para obtener una introducción rápida a la implementación del inicio de sesión único de SAML, vea [Inicio rápido: Configuración del inicio de sesión único basado en SAML de una aplicación en Azure Active Directory](add-application-portal-setup-sso.md). 

- **Basado en contraseña**: elija el inicio de sesión basado en contraseña cuando la aplicación tenga una página de inicio de sesión HTML. El SSO basado en contraseña también se conoce como almacenamiento de contraseñas. El SSO basado en contraseña permite administrar el acceso y las contraseñas de los usuarios en aplicaciones web que no admiten la federación de identidades. También es útil cuando varios usuarios necesitan compartir una sola cuenta, como las cuentas de aplicaciones de redes sociales de la organización. 

    El SSO basado en contraseña admite aplicaciones que requieren varios campos de inicio de sesión para aplicaciones que no solo requieren los campos de nombre de usuario y contraseña para iniciar sesión. Puede personalizar las etiquetas de los campos de nombre de usuario y contraseña que los usuarios ven en la página Aplicaciones cuando escriben sus credenciales. Para ver los pasos para implementar el SSO basado en contraseña, consulte el artículo sobre el [inicio de sesión único basado en contraseña](configure-password-single-sign-on-non-gallery-applications.md).

- **Vinculado**: elija el inicio de sesión vinculado si la aplicación está configurada para el SSO en otro servicio de proveedor de identidades. La opción Vinculado permite configurar la ubicación de destino cuando un usuario selecciona la aplicación de los portales de la organización. Puede agregar un vínculo a una aplicación web personalizada que actualmente use la federación, como Servicios de federación de Active Directory (AD FS). 

    También puede agregar vínculos a páginas web específicas que quiera que aparezcan en los paneles de acceso de su usuario y a una aplicación que no requiera autenticación. La opción Vinculado no proporciona la funcionalidad de inicio de sesión a través de las credenciales de Azure AD. Para ver los pasos para implementar el SSO vinculado, vea el artículo sobre el [inicio de sesión único vinculado](configure-linked-sign-on.md).

- **Deshabilitado**: elija el SSO deshabilitado cuando la aplicación no esté lista para configurarse para el inicio de sesión único.

- **Autenticación integrada de Windows (IWA)** : elija el inicio de sesión único de IWA para aplicaciones que usen IWA o aplicaciones compatibles con notificaciones. Para obtener más información, consulte [Delegación restringida de Kerberos para el inicio de sesión único (SSO) para las aplicaciones con Application Proxy](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md).

- **Basado en encabezados**: use el inicio de sesión único basado en encabezados si la aplicación usa encabezados para la autenticación. Para obtener más información, consulte el artículo sobre el [inicio de sesión único basado en encabezados](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md).

## <a name="next-steps"></a>Pasos siguientes
- [Administración del acceso a aplicaciones](what-is-access-management.md)
