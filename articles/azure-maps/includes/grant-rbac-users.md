---
title: Concesión del acceso basado en roles a los usuarios en Microsoft Azure Maps
titleSuffix: Azure Maps
description: Uso del control de acceso basado en roles para conceder a los usuarios autorización para Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: 22b3eaaff0989718a781b29549b4fab102673273
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803366"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Concesión de acceso basado en roles para usuarios de Azure Maps

Para conceder el *control de acceso basado en rol de Azure (RBAC de Azure)* , se asignan una o varias definiciones de roles de Azure Maps a un grupo de Azure AD o una entidad de seguridad.

Para ver las definiciones de roles de Azure disponibles para Azure Maps, consulte [Visualización de definiciones de roles integrados de Azure Maps](../how-to-manage-authentication.md#view-built-in-azure-maps-role-definitions).

Para obtener pasos detallados sobre cómo asignar un rol de Azure Maps disponible a la identidad administrada o a la entidad de servicio creadas, consulte [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

Para administrar de forma eficaz la aplicación de Azure Maps y el acceso a los recursos por parte de una gran cantidad de usuarios, consulte [Administración del acceso a recursos y aplicaciones con grupos en Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md).

>[!IMPORTANT]
>Para que los usuarios puedan autenticarse en la aplicación, se deben crear primero en Azure AD. Para más información, consulte [Incorporación o eliminación de usuarios mediante Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Para obtener información sobre cómo administrar eficazmente un directorio grande para los usuarios, consulte [Azure AD](../../active-directory/fundamentals/index.yml).

> [!WARNING]
> Las definiciones de roles de Azure Maps integradas proporcionan un acceso de autorización muy amplio a muchas API REST de Azure Maps. Para restringir al mínimo el acceso a las API, consulte cómo [crear una definición de roles personalizada y asignar la identidad asignada por el sistema](../../role-based-access-control/custom-roles.md) a la definición de roles personalizada. Esto proporciona el privilegio mínimo necesario para que la aplicación acceda a Azure Maps.
