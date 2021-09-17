---
title: Cómo agregar otros propietarios a un laboratorio en Azure Lab Services
description: En este artículo se muestra cómo un administrador puede agregar un usuario como propietario de un laboratorio en Azure Lab Services.
ms.topic: article
ms.date: 08/03/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: e8dd21a140e1c519f09d661c776e5c84c47da925
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635016"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Cómo agregar otros propietarios a un laboratorio existente en Azure Lab Services
En este artículo se muestra cómo, como administrador, puede agregar otros propietarios a un laboratorio existente.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Adición de un usuario al rol de lector de la cuenta de laboratorio
1. En la página **Cuenta de laboratorio**, seleccione **Todos los laboratorios** en el menú de la izquierda.
2. Seleccione el **laboratorio** al que quiere agregar el usuario como propietario. 

    ![Selección del laboratorio ](./media/how-to-add-user-lab-owner/select-lab.png)  
1. En el menú de navegación, seleccione **Control de acceso (IAM)** .

1. Seleccione **Agregar** > **Agregar asignación de roles (versión preliminar)** .

    ![Página Control de acceso (IAM) con el menú Agregar asignación de roles abierto.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. En la pestaña **Rol**, seleccione el rol **Lector**.

    ![Página Agregar asignación de roles con la pestaña Rol seleccionada.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. En la pestaña **Miembros**, seleccione el usuario que quiere agregar al rol Lector.

1. En la pestaña **Revisión y asignación**, seleccione **Revisión y asignación** para asignar el rol.
## <a name="add-user-to-the-owner-role-for-the-lab"></a>Adición de un usuario al rol de propietario del laboratorio

> [!NOTE]
> Si el usuario solo tiene acceso de lector en el laboratorio, el laboratorio no se muestra en labs.azure.com. Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).


1. En la página **Cuenta de laboratorio**, seleccione **Control de acceso (IAM)** .

1. Seleccione **Agregar** > **Agregar asignación de roles (versión preliminar)** .

    ![Página Control de acceso (IAM) con el menú Agregar asignación de roles abierto.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. En la pestaña **Rol**, seleccione el rol **Propietario**.

    ![Página Agregar asignación de roles con la pestaña Rol seleccionada.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. En la pestaña **Miembros**, seleccione el usuario que quiere agregar al rol Propietario.

1. En la pestaña **Revisión y asignación**, seleccione **Revisión y asignación** para asignar el rol.


## <a name="next-steps"></a>Pasos siguientes
Confirme que el usuario ve el laboratorio al iniciar sesión en el [portal de Lab Services](https://labs.azure.com).
