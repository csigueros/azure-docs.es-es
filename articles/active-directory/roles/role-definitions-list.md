---
title: 'Enumeración de las definiciones de roles de Azure AD: Azure AD'
description: Aprenda a enumerar roles integrados y personalizados de Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00fc768357d80fa22305b3b85e084dc636bd5a7c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796416"
---
# <a name="list-azure-ad-role-definitions"></a>Enumeración de las definiciones de roles de Azure AD

Una definición de roles es una colección de permisos que se pueden realizar, por ejemplo, de lectura, escritura y eliminación. Suele denominarse un rol. Azure Active Directory tiene más de 60 roles integrados, aunque también puede crear sus propios roles personalizados. Si alguna vez se ha preguntado "¿qué es lo que realmente hacen estos roles?", puede ver una lista detallada de los permisos para cada uno de los roles.

En este artículo se describe cómo enumerar los roles integrados y personalizados de Azure AD junto con sus permisos.

## <a name="list-all-roles"></a>Lista de todos los roles

1. Inicie sesión en el [centro de administración de Azure AD](https://aad.portal.azure.com) y seleccione **Azure Active Directory**.

1. Seleccione **Roles y administradores** para ver la lista de todos los roles disponibles.

    ![lista de roles en Azure Portal](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. A la derecha, seleccione el botón de puntos suspensivos y, a continuación, **Descripción** para ver la lista completa de permisos de un rol.

    En la página se incluyen vínculos a documentación relevante que le guiarán a través de la administración de los roles.

    ![Captura de pantalla que muestra la página "Administrador global - Descripción".](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Pasos siguientes

* No dude en compartir con nosotros en el [foro de roles administrativos de Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para más información sobre los permisos de roles, consulte [Roles integrados en Azure AD](permissions-reference.md).
* Para conocer los permisos predeterminados de usuario, vea una [comparación de los permisos predeterminados de usuario miembro e invitado](../fundamentals/users-default-permissions.md).
