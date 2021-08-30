---
title: Agregar o cambiar los administradores de la suscripción de Azure
description: Describe cómo agregar o cambiar un administrador de suscripción de Azure mediante el control de acceso basado en roles (Azure RBAC).
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/27/2021
ms.author: banders
ms.openlocfilehash: b4bd2615dd99145bd75fd7c1095056961d3b2d80
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988362"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Agregar o cambiar los administradores de la suscripción de Azure


Para administrar el acceso a recursos de Azure, debe tener el rol de administrador adecuado. Azure tiene un sistema de autorización denominado [control de acceso basado en rol de Azure (Azure RBAC)](../../role-based-access-control/overview.md) con varios roles integrados entre los que elegir. Puede asignar estos roles en distintos ámbitos, como grupo de administración, suscripción o grupo de recursos. De forma predeterminada, la persona que crea una nueva suscripción de Azure puede asignar a otros usuarios acceso administrativo a una suscripción.

En este artículo, se describe cómo agregar o cambiar el rol de administrador para un usuario que usa Azure RBAC en el ámbito de la suscripción.

Microsoft recomienda que administre el acceso a los recursos mediante Azure RBAC. No obstante, si todavía usa el modelo de implementación clásico y administra los recursos clásicos mediante el [módulo de PowerShell de Azure Service Management](/powershell/module/servicemanagement/azure.service), necesitará usar un administrador clásico.

> [!TIP]
> Si solo usa Azure Portal para administrar los recursos clásicos, no será preciso que utilice el administrador clásico.

Para más información, consulte [Implementación mediante Azure Resource Manager frente al modelo clásico](../../azure-resource-manager/management/deployment-models.md) y [Azure classic subscription administrators](../../role-based-access-control/classic-administrators.md) (Administradores de la suscripción clásica de Azure).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Asignación de un administrador de suscripción

Para que un usuario sea administrador de una suscripción a Azure, un administrador existente le asigna el rol [Propietario](../../role-based-access-control/built-in-roles.md#owner) (un rol de Azure) en el ámbito de la suscripción. El rol Propietario da al usuario acceso completo a todos los recursos de la suscripción, incluido el derecho a delegar este acceso a otros. Estos pasos son los mismos que para la asignación de cualquier otro rol.

Si no está seguro de quién es el administrador de cuenta de la suscripción, use los pasos siguientes para averiguarlo.

1. Abra la [página Suscripciones en Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione la suscripción que desee comprobar y, luego, consulte **Configuración**.
1. Seleccione **Propiedades**. El administrador de cuentas de la suscripción se muestra en el cuadro **Administrador de cuentas** .

### <a name="to-assign-a-user-as-an-administrator"></a>Para asignar un usuario como administrador

- Asigne el rol Propietario al usuario en el ámbito de la suscripción.  
     Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es el control de acceso basado en rol de Azure (RBAC)?](../../role-based-access-control/overview.md)
* [Descripción de los distintos roles en Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Asociación o incorporación de una suscripción de Azure al inquilino de Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Permisos de roles de administrador en Azure Active Directory](../../active-directory/roles/permissions-reference.md)