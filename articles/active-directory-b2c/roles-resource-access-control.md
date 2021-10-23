---
title: Control de acceso a roles y recursos
titleSuffix: Azure AD B2C
description: Aprenda a usar roles para controlar el acceso a los recursos.
services: active-directory-b2c
author: kengaderdus
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: b9a4e9cf2fa8c8cd91fed738ec14e88d18063044
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620910"
---
# <a name="roles-and-resource-access-control"></a>Control de acceso a roles y recursos

Al planear la estrategia de control de acceso, es mejor asignar a los usuarios el rol con menos privilegios necesario para acceder a los recursos. En la tabla siguiente se describen los recursos principales del inquilino de Azure AD B2C y los roles administrativos más adecuados para los usuarios que los administran.

|Recurso  |Descripción  |Role  |
|---------|---------|---------|
|[Registros de aplicación](tutorial-register-applications.md) | Crear y administrar todos los aspectos de los registros de aplicaciones web, móviles y nativas dentro de Azure AD B2C.|[Administrador de aplicaciones](../active-directory/roles/permissions-reference.md#application-administrator)|
|[Proveedores de identidades](add-identity-provider.md)| Configurar el [proveedor de identidades local](identity-provider-local.md) y los proveedores de identidades sociales o empresariales externos. | [Administrador de proveedor de identidades externo](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[Conectores de API](add-api-connector.md)| Integrar los flujos de usuario en las API web para personalizar la experiencia del usuario e integrarla con sistemas externos.|[Administrador de flujos de usuarios con identificador externo](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Personalización de marca de empresa](customize-ui.md#configure-company-branding)| Personalizar las páginas de flujos de usuarios.| [Administrador global](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Atributos de usuario](user-flow-custom-attributes.md)| Agregar o eliminar los atributos personalizados disponibles en todos los flujos de usuarios.| [Administrador de atributos de flujos de usuarios con identificador externo](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Administrar usuarios| Administrar las [cuentas de consumidor](manage-users-portal.md) y las cuentas administrativas como se describe en este artículo.| [Administrador de usuarios](../active-directory/roles/permissions-reference.md#user-administrator)|
|Roles y administradores| Administrar las asignaciones de roles en el directorio de Azure AD B2C. Crear y administrar grupos a los que se pueden asignar roles de Azure AD B2C. |[Administrador global](../active-directory/roles/permissions-reference.md#global-administrator), [Administrador de roles con privilegios](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Flujos de usuario](user-flow-overview.md)|Para una configuración rápida y la habilitación de tareas comunes de identidad, como registro, inicio de sesión y edición de perfiles.| [Administrador de flujos de usuarios con identificador externo](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Directivas personalizadas](user-flow-overview.md)| Crear, leer, actualizar y eliminar todas las directivas personalizadas en Azure AD B2C.| [Administrador de directivas B2C con IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Claves de directiva](policy-keys-overview.md)|Agregar y administrar claves de cifrado para firmar y validar tokens, secretos de cliente, certificados y contraseñas utilizados en las directivas personalizadas.|[Administrador de conjuntos de claves B2C con IEF](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|