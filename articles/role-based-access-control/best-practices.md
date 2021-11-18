---
title: Procedimientos recomendados para Azure RBAC
description: Procedimientos recomendados para usar el control de acceso basado en roles de Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 8f42a384c0ef8605de42243fcbb232d3ff615583
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301350"
---
# <a name="best-practices-for-azure-rbac"></a>Procedimientos recomendados para Azure RBAC

En este artículo se describen algunos procedimientos recomendados para usar el control de acceso basado en roles de Azure (Azure RBAC). Estos procedimientos recomendados proceden de nuestra experiencia con Azure RBAC y las experiencias de clientes como usted.

## <a name="only-grant-the-access-users-need"></a>Solo se debe conceder el acceso que los usuarios necesitan

Con Azure RBAC, puede repartir las tareas entre el equipo y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo. En lugar de proporcionar a todos los empleados permisos no restringidos en los recursos o la suscripción de Azure, puede permitir solo determinadas acciones en un ámbito concreto.

Al planear la estrategia de control de acceso, es recomendable conceder a los usuarios el privilegio mínimo para que realicen su trabajo. Evite la asignación de roles más amplios en ámbitos más amplios, aunque inicialmente parezca más práctico hacerlo. Al crear roles personalizados, incluya solo los permisos que necesitan los usuarios. Al limitar los roles y los ámbitos, se limitan los recursos en peligro si la entidad de seguridad llegara a verse comprometida.

El siguiente diagrama muestra un patrón sugerido para la asignación de RBAC de Azure.

![RBAC de Azure y privilegios mínimos](./media/best-practices/rbac-least-privilege.png)

Para más información sobre la asignación de roles, consulte [Asignaciones de roles de Azure mediante Azure Portal](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Limitación del número de propietarios de suscripciones

Debe tener un máximo de 3 propietarios de suscripción para reducir el riesgo de vulneración por parte de un propietario en peligro. Esta recomendación se puede supervisar en Microsoft Defender for Cloud. En el caso de otras recomendaciones de identidad y acceso en Defender for Cloud, consulte [Guía de referencia sobre las recomendaciones de seguridad](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Utilice Azure AD Privileged Identity Management

Para proteger las cuentas con privilegios de ciberataques malintencionados, puede usar Azure Active Directory Privileged Identity Management (PIM) para reducir el tiempo de exposición de los privilegios y aumentar la visibilidad de su uso a través de alertas e informes. PIM ayuda a proteger las cuentas con privilegios, ya que proporciona acceso con privilegios Just-in-Time a Azure AD y recursos de Azure. El acceso puede estar limitado por el tiempo, después del cual los privilegios se revocan automáticamente. 

Para más información, vea [¿Qué es Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="assign-roles-to-groups-not-users"></a>Asignación de roles a grupos, no a usuarios

Para que las asignaciones de roles sean más fáciles de administrar, evite la asignación directa de roles a los usuarios. En su lugar, asigne roles a grupos. La asignación de roles a grupos en lugar de a usuarios también ayuda a minimizar el número de asignaciones de roles, que tiene un [límite de 2000 asignaciones de roles por suscripción](troubleshooting.md#azure-role-assignments-limit). 

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de Azure RBAC](troubleshooting.md)
