---
title: 'Roles que no se pueden administrar en Privileged Identity Management: Azure Active Directory | Microsoft Docs'
description: Describe los roles que no se pueden administrar en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: adb5668f2a71c8fa8437275795308b66c44e737f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668777"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Roles que no se pueden administrar en Privileged Identity Management

Azure AD Privileged Identity Management (PIM) le permite administrar todos los [roles de Azure AD](../roles/permissions-reference.md) y todos los [roles de Azure](../../role-based-access-control/built-in-roles.md). Los roles de Azure también pueden incluir sus roles personalizados asociados a los grupos de administración, suscripciones, grupos de recursos y recursos. Sin embargo, hay algunos roles que no puede administrar. En este artículo se describen los roles que no se pueden administrar en Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Roles de administrador de suscripciones clásicas

No puede administrar los siguientes roles de administrador de suscripciones clásico en Privileged Identity Management:

- Administrador de cuenta
- Administrador de servicios
- Coadministrador

Para más información sobre los roles de administrador de suscripciones clásico, consulte [Roles de administrador de suscripciones clásico, roles de Azure y roles de administrador de Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>¿Qué sucede con los roles de administrador de Microsoft 365?

Se admiten todos los roles de Microsoft 365 en la experiencia de roles de Azure AD y el portal de administradores, como Administrador de Exchange y Administrador de SharePoint, pero no se admiten roles específicos en RBAC de Exchange ni en RBAC de SharePoint. Para más información sobre estos servicios de Microsoft 365, vea [Roles de administrador de Microsoft 365](/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> - Los usuarios que cumplan los requisitos para el rol Administrador de SharePoint, el rol Administrador de dispositivos, así como los roles que intenten tener acceso al centro de seguridad y cumplimiento de Microsoft, pueden experimentar retrasos de hasta unas horas después de activar su rol. Estamos trabajando con esos equipos para corregir los problemas.
> - Para obtener información sobre los retrasos en la activación del rol de administrador local del dispositivo unido a Azure AD, consulte [Administración del grupo de administradores locales en dispositivos unidos a Azure AD](/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role).

## <a name="next-steps"></a>Pasos siguientes

- [Asignación de roles de Azure AD en Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Asignación de roles de recursos de Azure en Privileged Identity Management](pim-resource-roles-assign-roles.md)