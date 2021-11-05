---
title: Conceder y solicitar permisos para todo el inquilino en Microsoft Defender for Cloud
description: Obtener información sobre cómo administrar permisos para todo el inquilino en Microsoft Defender for Cloud
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6f263de696ea23d0502a98b0ca85f4413a7020cd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004505"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Concesión y solicitud de visibilidad para todos los inquilinos

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Un usuario con el rol de **administrador global** de Azure Active Directory (AD) puede tener responsabilidades en todo el inquilino, pero carecer de los permisos de Azure para ver la información de toda la organización en Microsoft Defender for Cloud. La elevación de permisos es necesaria porque las asignaciones de roles de Azure AD no conceden acceso a los recursos de Azure. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Concesión de permisos de todo el inquilino a uno mismo

Para asignarse a uno mismo permisos de nivel de inquilino:

1. Si su organización administra el acceso a los recursos con [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md) o cualquier otra herramienta de PIM, el rol de administrador global debe estar activo para el usuario mediante el procedimiento que se describe a continuación.

1. Como usuario administrador global sin una asignación en el grupo de administración raíz del inquilino, abra la página **Información general** de Defender for Cloud y seleccione el vínculo de **visibilidad de todo el inquilino** en el mensaje emergente. 

    :::image type="content" source="media/management-groups-roles/enable-tenant-level-permissions-banner.png" alt-text="Habilite los permisos de nivel de inquilino en Microsoft Defender for Cloud.":::

1. Selección del nuevo rol de Azure que se va a asignar. 

    :::image type="content" source="media/management-groups-roles/enable-tenant-level-permissions-form.png" alt-text="Formulario para definir los permisos de nivel de inquilino que se asignarán al usuario.":::

    > [!TIP]
    > Por lo general, el rol de Administrador de seguridad es necesario para aplicar directivas en el nivel raíz, mientras que el de Lector de seguridad será suficiente para proporcionar visibilidad en el nivel de inquilino. Para más información acerca de los permisos concedidos por estos roles, consulte la [descripción del rol integrado de Administrador de seguridad](../role-based-access-control/built-in-roles.md#security-admin) o la [descripción del rol integrado de Lector de seguridad](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Para conocer las diferencias entre estos roles específicos de Defender for Cloud, consulte la tabla de [Roles y acciones permitidas](permissions.md#roles-and-allowed-actions).

    Para conseguir la vista de toda la organización, se conceden roles en el nivel de grupo de administración raíz del inquilino.  

1. Cierre sesión en Azure Portal y vuelva a iniciarla.

1. Cuando tenga privilegios de acceso elevados, abra o actualice Microsoft Defender for Cloud para comprobar que tiene visibilidad en todas las suscripciones del inquilino de Azure AD. 

El proceso sencillo anterior realiza una serie de operaciones automáticamente:

1. Los permisos del usuario se elevan temporalmente.
1. Con los nuevos permisos, se asigna al usuario el rol de Azure RBAC deseado en el grupo de administración raíz.
1. Se quitan los permisos elevados.

Para más información sobre el proceso de elevación de Azure AD, consulte [Elevación de los privilegios de acceso para administrar todas las suscripciones y los grupos de administración de Azure](../role-based-access-control/elevate-access-global-admin.md).


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Solicitud de permisos para todo el inquilino cuando el suyo sea insuficiente

Si inicia sesión en Defender for Cloud y ve un banner que le indica que la vista está limitada, puede hacer clic para enviar una solicitud al administrador global de su organización. En la solicitud, puede incluir el rol que le gustaría que se le asignara y el administrador global tomará una decisión sobre qué rol otorgar. 

La decisión del administrador global es aceptar o rechazar estas solicitudes. 

> [!IMPORTANT]
> Solamente puede enviar una solicitud cada siete días.

Para solicitar permisos elevados al administrador global:

1. En Azure Portal, abra Microsoft Defender for Cloud.

1. Si ve el banner "Está viendo información limitada"., Selecciónelo.

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions.png" alt-text="Banner que informa a un usuario de que puede solicitar permisos para todo el inquilino.":::

1. En el formulario de solicitud detallado, seleccione el rol deseado y la justificación del motivo por el que necesita estos permisos.

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions-details.png" alt-text="Página de detalles para solicitar permisos para todo el inquilino al administrador global de Azure.":::

1. Seleccione **Solicitar acceso**.

    Se envía un correo electrónico al administrador global. El correo electrónico contiene un vínculo a Defender for Cloud donde pueden aprobar o rechazar la solicitud.

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions-email.png" alt-text="Correo electrónico al administrador global para los nuevos permisos.":::

    Una vez que el administrador global selecciona **Revisar la solicitud** y completa el proceso, la decisión se envía por correo electrónico al usuario que realiza la solicitud. 

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los permisos de Defender for Cloud en la siguiente página relacionada:

- [Permisos de Microsoft Defender for Cloud](permissions.md)
