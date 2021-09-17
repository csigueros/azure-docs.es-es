---
title: Permisos de aplicación para roles personalizados en Azure Active Directory | Microsoft Docs
description: Obtenga una vista previa de los permisos de aplicaciones empresariales para los roles de Azure AD personalizados en Azure Portal, PowerShell o Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 08/06/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: b0a8b3abf4e119096959b640be4b44051a198916
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736154"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Permisos de aplicaciones empresariales para roles personalizados de Azure Active Directory

Este artículo contiene los permisos de aplicaciones empresariales disponibles actualmente para las definiciones de roles personalizados de Azure Active Directory. En este artículo, encontrará listas de permisos para algunos escenarios habituales y la lista completa de permisos de aplicaciones empresariales.

## <a name="license-requirements"></a>Requisitos de licencia

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="enterprise-application-permissions"></a>Permisos de aplicaciones empresariales

Para más información sobre cómo usar estos permisos, consulte [Asignación de roles personalizados para administrar aplicaciones empresariales](custom-enterprise-apps.md).

#### <a name="assigning-users-or-groups-to-an-application"></a>Asignación de usuarios o grupos a una aplicación

Para delegar la asignación de usuarios y grupos que pueden acceder a aplicaciones de inicio de sesión único basadas en SAML. Permisos necesarios

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

#### <a name="creating-gallery-applications"></a>Creación de aplicaciones de la galería

Para delegar la creación de aplicaciones de la galería de Azure AD como ServiceNow, F5, Salesforce, entre otros. Permisos necesarios:

- microsoft.directory/applicationTemplates/instantiate

#### <a name="configuring-basic-saml-urls"></a>Configuración de direcciones URL de SAML básicas

Para delegar la actualización y lectura de configuraciones básicas de SAML para aplicaciones de inicio de sesión único basadas en SAML. Permisos necesarios:

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

#### <a name="rolling-over-or-creating-signing-certs"></a>Sustitución o creación de certificados de firma

Para delegar la administración de certificados de firma para aplicaciones de inicio de sesión único basadas en SAML. Permisos necesarios.

microsoft.directory/applications/credentials/update

#### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Actualización de la dirección de correo electrónico de la notificación de expiración del certificado de inicio de sesión

Para delegar la actualización de las direcciones de correo electrónico de las notificaciones de expiración de los certificados de inicio de sesión para aplicaciones de inicio de sesión único basadas en SAML. Permisos necesarios:

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

#### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>Administración de la firma del token SAML y del algoritmo de inicio de sesión

Para delegar la actualización de la firma del token SAML y del algoritmo de inicio de sesión para aplicaciones de inicio de sesión único basadas en SAML. Permisos necesarios:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

#### <a name="manage-user-attributes-and-claims"></a>Administración de atributos y notificaciones de usuario

Para delegar la creación, eliminación y actualización de atributos y notificaciones de usuario de las aplicaciones de inicio de sesión único basadas en SAML. Permisos necesarios:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>Permisos de aprovisionamiento de aplicaciones

La realización de cualquier operación de escritura como, por ejemplo, la administración de trabajos, esquemas o credenciales mediante la interfaz de usuario, también requerirá permisos de lectura para ver la página de aprovisionamiento.

Para establecer el ámbito en todos los usuarios y grupos, o en los usuarios y grupos asignados actualmente, se necesitan los permisos synchronizationJob y synchronizationCredentials.

#### <a name="turn-on-or-restart-provisioning-jobs"></a>Activación o reinicio de los trabajos de aprovisionamiento

Para delegar la capacidad de activar, desactivar y reiniciar los trabajos de aprovisionamiento. Permisos necesarios:

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

#### <a name="configure-the-provisioning-schema"></a>Configuración del esquema de aprovisionamiento  

Para delegar las actualizaciones en la asignación de atributos. Permisos necesarios:

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

#### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Lectura de la configuración de aprovisionamiento asociada al objeto de aplicación

Para delegar la capacidad de leer la configuración de aprovisionamiento asociada al objeto. Permisos necesarios:

- microsoft.directory/applications/synchronization/standard/read

#### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Lectura de la configuración de aprovisionamiento asociada a la entidad de servicio

Para delegar la capacidad de leer la configuración de aprovisionamiento asociada a la entidad de servicio. Permisos necesarios:

- microsoft.directory/servicePrincipals/synchronization/standard/read

#### <a name="authorize-application-access-for-provisioning"></a>Autorización del acceso a la aplicación para el aprovisionamiento  

Para delegar la capacidad de autorización del acceso a la aplicación para el aprovisionamiento. Token de portador de OAuth de entrada de ejemplo. Permisos necesarios:

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="application-proxy-permissions"></a>Permisos de Application Proxy

Realizar cualquier operación de escritura en las propiedades de Application Proxy de la aplicación también requiere los permisos para actualizar las propiedades básicas y la autenticación de la aplicación.

Para leer y realizar cualquier operación de escritura en las propiedades de Application Proxy de la aplicación, también se requieren los permisos de lectura para ver los grupos de conectores, ya que esto forma parte de la lista de propiedades que se muestra en la página.

#### <a name="delegate-application-proxy--connector-management"></a>Delegar la administración de conectores de Application Proxy

Para delegar acciones de creación, lectura, actualización y eliminación para la administración de conectores. Permisos necesarios:

- microsoft.directory/connectorGroups/allProperties/read
- microsoft.directory/connectorGroups/allProperties/update
- microsoft.directory/connectorGroups/create
- microsoft.directory/connectorGroups/delete
- microsoft.directory/connectors/allProperties/read
- microsoft.directory/connectors/create


#### <a name="delegate-application-proxy-settings-management"></a>Delegar la administración de la configuración de Application Proxy

Para delegar acciones de creación, lectura, actualización y eliminación para las propiedades de Application Proxy en una aplicación. Permisos necesarios:

- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/applications/applicationProxy/update 
- microsoft.directory/applications/applicationProxyAuthentication/update 
- microsoft.directory/applications/applicationProxySslCertificate/update 
- microsoft.directory/applications/applicationProxyUrlSettings/update 
- microsoft.directory/applications/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/connectorGroups/allProperties/read

#### <a name="read-application-proxy-settings-for-an-app"></a>Lectura de la configuración de Application Proxy para una aplicación

Para delegar los permisos de lectura para las propiedades de Application Proxy en una aplicación. Permisos necesarios:
 
- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/connectorGroups/allProperties/read 

#### <a name="update-url-configuration-application-proxy-settings-for-an-app"></a>Actualización de la configuración de URL de Application Proxy para una aplicación 

Para delegar los permisos de creación, lectura, actualización y eliminación (CRUD) para actualizar la dirección URL externa, la dirección URL interna y las propiedades de certificado SSL de Application Proxy. Permisos necesarios: 

- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/connectorGroups/allProperties/read 
- microsoft.directory/applications/basic/update 
- microsoft.directory/applications/authentication/update
- microsoft.directory/applications/applicationProxyAuthentication/update 
- microsoft.directory/applications/applicationProxySslCertificate/update 
- microsoft.directory/applications/applicationProxyUrlSettings/update

## <a name="full-list-of-permissions"></a>Lista completa de permisos

> [!div class="mx-tableFixed"]
> | Permiso | Descripción |
> | ---------- | ----------- |
> | microsoft.directory/applicationPolicies/allProperties/read | Leer todas las propiedades de las directivas de aplicación |
> | microsoft.directory/applicationPolicies/allProperties/update | Actualizar todas las propiedades de las directivas de aplicación |
> | microsoft.directory/applicationPolicies/basic/update | Actualizar las propiedades estándar de las directivas de aplicación |
> | microsoft.directory/applicationPolicies/create | Crear directivas de aplicación |
> | microsoft.directory/applicationPolicies/createAsOwner | Permite crear directivas de aplicación. El creador se agrega como primer propietario. |
> | microsoft.directory/applicationPolicies/delete | Eliminar directivas de aplicación |
> | microsoft.directory/applicationPolicies/owners/read | Leer los propietarios en directivas de aplicación |
> | microsoft.directory/applicationPolicies/owners/update | Actualizar la propiedad de propietario de las directivas de aplicación |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Leer las directivas de aplicación aplicadas a la lista de objetos |
> | microsoft.directory/applicationPolicies/standard/read | Leer las propiedades estándar de las directivas de aplicación |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Crear y eliminar servicePrincipals, y leer y actualizar todas las propiedades en Azure Active Directory |
> | microsoft.directory/servicePrincipals/allProperties/read | Leer todas las propiedades de servicePrincipals |
> | microsoft.directory/servicePrincipals/allProperties/update | Actualizar todas las propiedades de servicePrincipals |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Leer las asignaciones de roles de la entidad de servicio |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Actualiza las asignaciones de rol de la entidad de servicio. |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Leer las asignaciones de roles asignadas a las entidades de servicio |
> | microsoft.directory/servicePrincipals/audience/update | Actualizar las propiedades de público en las entidades de servicio |
> | microsoft.directory/servicePrincipals/authentication/update | Actualizar las propiedades de autenticación en las entidades de servicio |
> | microsoft.directory/servicePrincipals/basic/update | Actualizar las propiedades básicas de las entidades de servicio |
> | microsoft.directory/servicePrincipals/create | Creación de entidades de servicio |
> | microsoft.directory/servicePrincipals/createAsOwner | Permite crear entidades de servicio. El creador se agrega como primer propietario. |
> | microsoft.directory/servicePrincipals/credentials/update | Actualizar las propiedades de credenciales en entidades de servicio |
> | microsoft.directory/servicePrincipals/delete | Eliminar entidades de servicio |
> | microsoft.directory/servicePrincipals/disable | Deshabilitar entidades de servicio |
> | microsoft.directory/servicePrincipals/enable | Habilitación de entidades de servicio |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Leer las credenciales de inicio de sesión único con contraseña en las entidades de servicio |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Administrar las credenciales de inicio de sesión único con contraseña en las entidades de servicio |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Leer las concesiones de permisos delegados de las entidades de servicio |
> | microsoft.directory/servicePrincipals/owners/read | Leer propietarios de las entidades de servicio |
> | microsoft.directory/servicePrincipals/owners/update | Actualizar propietarios de las entidades de servicio |
> | microsoft.directory/servicePrincipals/permissions/update | Actualizar los permisos de las entidades de servicio |
> | microsoft.directory/servicePrincipals/policies/read | Leer directivas de las entidades de servicio |
> | microsoft.directory/servicePrincipals/policies/update | Actualizar directivas de las entidades de servicio |
> | microsoft.directory/servicePrincipals/standard/read | Leer las propiedades estándar de las entidades de servicio |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lectura de la configuración de aprovisionamiento asociada a la entidad de servicio |
> | microsoft.directory/servicePrincipals/tag/update | Actualizar la propiedad de etiquetas de las entidades de servicio |
> | microsoft.directory/applicationTemplates/instantiate | Crear instancias de las aplicaciones de la galería a partir de plantillas de aplicación. |
> | microsoft.directory/auditLogs/allProperties/read | Leer registros de auditoría |
> | microsoft.directory/signInReports/allProperties/read | Leer registros de inicio de sesión |
> | microsoft.directory/applications/applicationProxy/read | Leer todas las propiedades del proxy de aplicación de todos los tipos de aplicaciones |
> | microsoft.directory/applications/applicationProxy/update | Actualizar todas las propiedades del proxy de aplicación de todos los tipos de aplicaciones |
> | microsoft.directory/applications/applicationProxyAuthentication/update | Actualizar las propiedades de autenticación del proxy de aplicación de todos los tipos de aplicaciones |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | Actualizar las direcciones URL internas y externas del proxy de aplicación de todos los tipos de aplicaciones |
> | microsoft.directory/applications/applicationProxySslCertificate/update | Actualizar los dominios personalizados del proxy de aplicación de todos los tipos de aplicaciones. |
> | microsoft.directory/applications/synchronization/standard/read | Lectura de la configuración de aprovisionamiento asociada al objeto de aplicación |
> | microsoft.directory/connectorGroups/create | Crear grupos de conectores del proxy de aplicación |
> | microsoft.directory/connectorGroups/delete | Eliminar grupos de conectores del proxy de aplicación |
> | microsoft.directory/connectorGroups/allProperties/read | Leer todas las propiedades de los grupos de conectores del proxy de aplicación |
> | microsoft.directory/connectorGroups/allProperties/update | Actualizar todas las propiedades de los grupos de conectores del proxy de aplicación |
> | microsoft.directory/connectors/create | Crear conectores del proxy de aplicación |
> | microsoft.directory/connectors/allProperties/read | Leer todas las propiedades de los conectores del proxy de aplicación |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Permite administrar todos los aspectos de la sincronización de trabajos para los recursos de la entidad de servicio. |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Permite leer la configuración de aprovisionamiento asociada a las entidades de servicio. |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Permite administrar todos los aspectos de la sincronización de esquemas de los recursos de la entidad de servicio. |
> | microsoft.directory/provisioningLogs/allProperties/read | Permite leer todas las propiedades de los registros de aprovisionamiento. |

## <a name="next-steps"></a>Pasos siguientes

- [Creación de roles personalizados mediante Azure Portal, PowerShell de Azure AD y Graph API](custom-create.md)
- [Lista de asignaciones de roles](view-assignments.md)
