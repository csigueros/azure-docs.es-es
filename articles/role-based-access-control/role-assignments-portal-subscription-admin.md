---
title: Asignación de un usuario como administrador de una suscripción de Azure - Azure RBAC
description: Aprenda a convertir a un usuario en administrador de una suscripción de Azure mediante Azure Portal y el control de acceso basado en rol (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/25/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: af88edbb788afa3576d3317d4e1cd54dea6f4deb
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988074"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Asignación de un usuario como administrador de una suscripción de Azure

Para que un usuario sea administrador de una suscripción de Azure, asígnele el rol [Propietario](built-in-roles.md#owner) en el ámbito de la suscripción. El rol Propietario da al usuario acceso total a todos los recursos de la suscripción, incluido el derecho a conceder acceso a otros. Estos pasos son los mismos que para la asignación de cualquier otro rol.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Paso 1: Abrir la suscripción

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Busque las suscripciones en el cuadro de búsqueda.

1. Haga clic en la suscripción que desee usar.

    A continuación se muestra una suscripción de ejemplo.

    ![Captura de pantalla de información general de Suscripciones](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-page"></a>Paso 2: Abrir la página Agregar asignación de roles

**Control de acceso (IAM)** es la página que se usa normalmente para asignar roles y conceder acceso a los recursos de Azure. También se conoce como administración de identidad y acceso (IAM) y aparece en varias ubicaciones de Azure Portal.

1. Haga clic en **Control de acceso (IAM)**.

    A continuación se muestra un ejemplo de la página Control de acceso (IAM) de una suscripción.

    ![Captura de pantalla de la página Control de acceso (IAM) para una suscripción.](./media/shared/sub-access-control.png)

1. Haga clic en la pestaña **Asignaciones de roles** para ver todas las asignaciones de roles en este ámbito.

1. Haga clic en **Agregar** > **Agregar asignación de roles (versión preliminar)** .

   Si no tiene permisos para asignar roles, la opción Agregar asignación de roles se deshabilitará.

    ![Captura de pantalla del menú Agregar > Agregar asignación de roles en la experiencia con la versión preliminar.](./media/shared/add-role-assignment-menu-preview.png)

    Se abre la página Agregar asignación de roles.

## <a name="step-3-select-the-owner-role"></a>Paso 3: Seleccionar el rol Propietario

El rol [Propietario](built-in-roles.md#owner) permite conceder acceso total para administrar todos los recursos, incluida la posibilidad de asignar roles en Azure RBAC. Debe tener un máximo de 3 propietarios de suscripción para reducir el riesgo de vulneración por parte de un propietario en peligro.

1. En la pestaña **Roles**, seleccione el rol **Propietario**.

    Puede buscar un rol por nombre o por descripción. También puede filtrar los roles por tipo y categoría.

   ![Captura de pantalla de la página Agregar asignación de roles con la pestaña Roles de la experiencia con la versión preliminar.](./media/shared/roles.png)

1. Haga clic en **Next**.

## <a name="step-4-select-who-needs-access"></a>Paso 4: elegir quién necesita acceso

1. En la pestaña **Miembros**, seleccione **Usuario, grupo o entidad de servicio**.

   ![Captura de pantalla de la página Agregar asignación de roles con la pestaña Agregar miembros de la experiencia con la versión preliminar.](./media/shared/members.png)

1. Haga clic en **Seleccionar miembros**.

1. Busque y seleccione el usuario.

    Puede escribir en el cuadro **Seleccionar** para buscar en el directorio por nombre para mostrar o dirección de correo electrónico.

   ![Captura de pantalla del panel Seleccionar miembros de la experiencia con la versión preliminar.](./media/shared/select-members.png)

1. Haga clic en **Guardar** para agregar el usuario a la lista Miembros.

1. En el cuadro de texto **Descripción**, escriba una descripción opcional para esta asignación de roles.

    Más adelante puede mostrar esta descripción en la lista de asignaciones de roles.

1. Haga clic en **Next**.

## <a name="step-5-assign-role"></a>Paso 5: Asignación de un rol

1. En la pestaña **Revisión y asignación**, revise la configuración de la asignación de roles.

1. Haga clic en **Revisión y asignación** para asignar el rol.

   Transcurridos unos instantes, al usuario se le asigna el rol Propietario para la suscripción.

    ![Captura de pantalla de la lista de asignaciones de roles después de asignar el rol en la experiencia con la versión preliminar.](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Pasos siguientes

- [Asignación de roles de Azure mediante Azure Portal](role-assignments-portal.md)
- [Enumeración de asignaciones de roles de Azure mediante Azure Portal](role-assignments-list-portal.md)
- [Organización de los recursos con grupos de administración de Azure](../governance/management-groups/overview.md)
