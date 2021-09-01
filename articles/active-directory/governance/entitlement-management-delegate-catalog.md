---
title: 'Delegación de la gobernanza del acceso en los creadores de catálogos desde la administración de derechos de Azure AD: Azure Active Directory'
description: Aprenda a delegar la gobernanza del acceso de los administradores de TI en los creadores de catálogos y los jefes de proyecto para que ellos mismos puedan encargarse del acceso.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/6/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5afa05b8a529d8a9e9fceeb4a113f0b743acfc05
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286580"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegación de la gobernanza del acceso en los creadores de catálogos desde la administración de derechos de Azure AD

Un catálogo es un contenedor de recursos y paquetes de acceso. Creará un catálogo cuando quiera agrupar recursos relacionados y paquetes de acceso. De manera predeterminada, un administrador global o un administrador de gobernanza de identidades pueden [crear un catálogo](entitlement-management-catalog-create.md) y agregar usuarios adicionales como propietarios de catálogo.

Para delegar en usuarios que no son administradores de modo que puedan crear sus propios catálogos, puede agregarlos al rol de creador de catálogos definido en la administración de derechos de Azure AD. Puede agregar usuarios individuales o bien agregar un grupo, cuyos miembros pueden luego crear catálogos.  Después de crear un catálogo, pueden agregar recursos de su propiedad a ese catálogo.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Como administrador de TI, delegación en un creador de catálogos

Siga estos pasos para asignar un usuario al rol de creador de catálogos.

**Requisitos previos de rol:** administrador global, administrador de Identity Governance o administrador de usuarios

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, en la sección **Administración de derechos**, haga clic en **Configuración**.

1. Haga clic en **Editar**.

    ![Configuración para agregar creadores de catálogos](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. En la sección **Delegate entitlement management** (Delegar la administración de derechos), haga clic en **Agregar creador de catálogos** para seleccionar los usuarios o grupos en los que desea delegar este rol de administración de derechos.

1. Haga clic en **Seleccionar**.

1. Haga clic en **Save**(Guardar).

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Permitir a los roles delegados tener acceso al Azure Portal

Para permitir que los roles delegados, como creadores de catálogos y administradores de paquetes de acceso, tengan acceso al Azure Portal para administrar los paquetes de acceso, debe comprobar la configuración del portal de administración.

**Rol necesario:** administrador global o administrador de usuarios.

1. En Azure Portal, haga clic en **Azure Active Directory** y luego en **Usuarios**.

1. Seleccione **Configuración de usuario** en el menú a la izquierda.

1. Cerciórese de que **Restringir el acceso al portal de administración de Azure AD** está establecido en **No**.

    ![Configuración de usuario de Azure AD: portal de administración](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="manage-role-assignments-programmatically-preview"></a>Administración de asignaciones de roles mediante programación (versión preliminar)

También puede ver y actualizar las asignaciones de roles específicas del catálogo de administración de derechos y los creadores de catálogo mediante Microsoft Graph.  Un usuario que tenga un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All` delegado puede llamar a Graph API para [enumerar las definiciones de roles](/graph/api/rbacapplication-list-roledefinitions?view=graph-rest-beta&preserve-view=true) de la administración de derechos y [enumerar las asignaciones de roles](/graph/api/rbacapplication-list-roleassignments?view=graph-rest-beta&preserve-view=true) para esas definiciones de roles.

Para recuperar una lista de los usuarios y grupos asignados al rol de creadores del catálogo, el rol con el identificador de definición `ba92d953-d8e0-4e39-a797-0cbedb0a89e8`, use la consulta de Graph.

```http
GET https://graph.microsoft.com/beta/roleManagement/entitlementManagement/roleAssignments?$filter=roleDefinitionId eq 'ba92d953-d8e0-4e39-a797-0cbedb0a89e8'&$expand=principal
```


## <a name="next-steps"></a>Pasos siguientes

- [Creación y administración de un catálogo de recursos](entitlement-management-catalog-create.md)
- [Delegación de la gobernanza del acceso en administradores de paquetes de acceso](entitlement-management-delegate-managers.md)
- [Delegación de la gobernanza del acceso a los propietarios de recursos](entitlement-management-delegate.md)

