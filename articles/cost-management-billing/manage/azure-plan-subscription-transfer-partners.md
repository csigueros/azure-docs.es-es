---
title: Transferencia de suscripciones de un plan de Azure de un asociado a otro (versión preliminar)
description: En este artículo se describe lo que necesita saber antes y después de transferir la propiedad de facturación de su suscripción de Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 6ce3a4b19bd1ba32a466bede87e111744e1a8a52
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658476"
---
# <a name="transfer-subscriptions-under-an-azure-plan-from-one-partner-to-another-preview"></a>Transferencia de suscripciones de un plan de Azure de un asociado a otro (versión preliminar)

En este artículo se describe lo que necesita saber antes y después de transferir la propiedad de facturación de su suscripción de Azure. Para iniciar la transferencia de una suscripción de Azure que se encuentra en un plan de Azure de un asociado de Microsoft a otro, debe ponerse en contacto con su asociado. El asociado le enviará instrucciones sobre cómo comenzar. Una vez completado el proceso de transferencia, se cambia la propiedad de facturación de la suscripción.

## <a name="user-access"></a>Acceso de usuarios

El acceso a los usuarios, grupos o entidades de servicio existentes que se asignaron mediante el control de acceso basado en rol de Azure (Azure RBAC) no se ve afectado durante la transición. [Azure RBAC](../../role-based-access-control/overview.md) ayuda a administrar quién tiene acceso a los recursos de Azure, qué se puede hacer con esos recursos y a qué áreas se tiene acceso. La transferencia de la suscripción no proporciona al nuevo asociado acceso Azure RBAC a los recursos. El asociado anterior conserva su acceso Azure RBAC.

Por lo tanto, es importante retirar el acceso mediante Azure RBAC al asociado anterior y dar acceso al asociado nuevo. Para más información sobre cómo dar acceso a los asociados nuevos, vea [¿Qué es el control de acceso basado en rol de Azure (RBAC)?](../../role-based-access-control/overview.md) Para más información sobre cómo quitar el acceso a Azure RBAC al asociado anterior, consulte el artículo sobre la [eliminación de asignación de roles de Azure](../../role-based-access-control/role-assignments-remove.md).

Además, el asociado nuevo no obtiene automáticamente acceso de [administrador con derechos delegados (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) a las suscripciones. AOBO es necesario para que su asociado administre las suscripciones de Azure en su nombre. Para más información sobre los privilegios de Azure, consulte [Obtención de permisos para administrar el servicio o la suscripción de un cliente](/partner-center/customers-revoke-admin-privileges).

## <a name="stop-a-transfer"></a>Detener una transferencia

Tras recibir la confirmación de que se ha enviado una solicitud de transferencia, puede usar una de las siguientes opciones **si no desea que la transferencia continúe**.

- Si el asociado actual no ha aceptado aún la solicitud, puede pedir al nuevo asociado que cancele la solicitud de transferencia que inició (cuando el estado sea pendiente).
- Pida al asociado actual que no realice ninguna acción cuando reciba la solicitud de transferencia. Sin el consentimiento del asociado actual, la transferencia no puede continuar. La solicitud expirará.
- Puede _eliminar la relación de revendedor_ con el asociado nuevo. Esta acción elimina la posibilidad de que se mueva la suscripción. Cancela la solicitud de forma eficaz.

También puede buscar ayuda, informar de una conducta inadecuada o una actividad sospechosa mediante cualquiera de las opciones del sitio web [Microsoft Legal](https://www.microsoft.com/legal/). La opción de notificar un problema está en la opción de menú Compliance & Ethics (Cumplimiento y ética).

## <a name="next-steps"></a>Pasos siguientes

- Para dar al nuevo asociado el acceso Azure RBAC, consulte [¿Qué es el control de acceso basado en rol de Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Obtención de permisos para administrar un servicio de clientes o una suscripción](/partner-center/customers-revoke-admin-privileges).