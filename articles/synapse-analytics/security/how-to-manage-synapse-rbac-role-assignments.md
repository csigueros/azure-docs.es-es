---
title: Cómo administrar asignaciones de Synapse RBAC en Synapse Studio
description: En este artículo se describe cómo asignar y revocar roles de Synapse RBAC a las entidades de seguridad de AAD
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: 8bbb6185368b438376529d69409d301c0cdd9c97
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732487"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Cómo administrar asignaciones de roles de Synapse RBAC en Synapse Studio

Synapse RBAC usa roles para asignar permisos a usuarios, grupos y otras entidades de seguridad para permitir el acceso y el uso de los recursos y artefactos de código de Synapse.  [Más información](./synapse-workspace-synapse-rbac.md)

En este artículo se muestra cómo agregar y eliminar asignaciones de roles de Synapse RBAC.

>[!Note]
>- Para administrar las asignaciones de roles de Synapse RBAC, debe tener el rol de administrador de Synapse en el área de trabajo o en un ámbito de nivel inferior que incluya los objetos que desea administrar. Si es administrador de Synapse en el área de trabajo, puede conceder acceso a todos los objetos en el área de trabajo. 
>- Los **usuarios invitados** de un inquilino de AD distinto también pueden ver y administrar las asignaciones de roles, después de que se les asigne el rol de administrador de Synapse.
>- Para ayudarle a recuperar el acceso a un área de trabajo en caso de que no haya ningún administrador de Synapse asignado o a su disposición, los usuarios con permisos para administrar las asignaciones de roles de **Azure RBAC** en el área de trabajo también pueden administrar las asignaciones de roles de **Synapse RBAC**, lo que permite agregar administradores de Synapse u otras asignaciones de roles de Synapse.
>- El acceso a los grupos de SQL se administra mediante permisos de SQL.  A excepción de los roles de administrador de Synapse y administrador de Synapse SQL, los roles de Synapse RBAC no conceden acceso a los grupos de SQL.

>[!important]
>- Los cambios realizados en las asignaciones de roles de Synapse RBAC pueden tardar de 2 a 5 minutos en surtir efecto. 
>- Si está administrando los permisos de Synapse RBAC modificando la pertenencia de los grupos de seguridad, los cambios en la pertenencia se administran mediante Azure Active Directory.  Los cambios en la pertenencia a grupos pueden tardar de 10 a 15 minutos o más en surtir efecto.

## <a name="open-synapse-studio"></a>Abrir Synapse Studio  

Para asignar un rol a un usuario, grupo, entidad de servicio o identidad administrada, primero [abra Synapse Studio](https://web.azuresynapse.net/) e inicie sesión en el área de trabajo. 

![Inicio de sesión en el área de trabajo](./media/common/login-workspace.png) 
 
 Una vez que haya abierto el área de trabajo, expanda la sección **Seguridad** a la izquierda y seleccione **Control de acceso**. 

 ![Seleccione Control de acceso en la sección Seguridad de la izquierda](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

La pantalla de control de acceso muestra las asignaciones de roles actuales.  Puede filtrar la lista por nombre de entidad de seguridad o correo electrónico, y filtrar de forma selectiva los tipos de objeto, los roles o los ámbitos incluidos. En esta pantalla, puede agregar o quitar asignaciones de roles.  

## <a name="add-a-synapse-role-assignment"></a>Agregar una asignación de rol de Synapse

En la pantalla de control de acceso, seleccione **+Agregar** para crear una nueva asignación de roles

![Haga clic en +Agregar para crear una nueva asignación de roles](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

En la pestaña Agregar asignación de roles, puede crear asignaciones de roles en el ámbito del área de trabajo o en el ámbito del elemento del área de trabajo. 

## <a name="add-workspace-scoped-role-assignment"></a>Agregar asignación de roles en el ámbito del área de trabajo

Primero, seleccione **Área de trabajo** como ámbito y, a continuación, seleccione el **rol de Synapse RBAC**.  Seleccione las **entidades de seguridad** a las que se asignará el rol y, después, cree las asignaciones de roles. 

![Agregue la asignación de roles al área de trabajo: seleccione el rol](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

El rol asignado se aplicará a todos los objetos aplicables en el área de trabajo.

## <a name="add-workspace-item-scoped-role-assignment"></a>Agregar asignación de roles en el ámbito del elemento del área de trabajo

Para asignar un rol en un ámbito más preciso, seleccione **Elemento de área de trabajo** como ámbito y, a continuación, seleccione el ámbito **Tipo de elemento**.       

![Agregue la asignación de roles al elemento del área de trabajo: seleccione el tipo de elemento](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Seleccione el **elemento** específico que se va a usar como ámbito y, a continuación, seleccione el **rol** que se va a asignar en la lista desplegable.  La lista desplegable muestra solo los roles que son válidos para el tipo de elemento seleccionado. [Más información](./synapse-workspace-synapse-rbac.md).  

![Agregue la asignación de roles al elemento del área de trabajo: seleccione el rol](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
A continuación, **seleccione las entidades de seguridad** a las que se va a asignar el rol.  Puede seleccionar varias entidades de seguridad de forma iterativa.  Seleccione **Aplicar** para crear la asignación de roles.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Eliminación de una asignación de roles de Synapse RBAC

Para revocar el acceso a Synapse RBAC, quite las asignaciones de roles adecuadas.  En la pantalla de control de acceso, use los filtros para buscar las asignaciones de roles que se van a quitar.  Compruebe las asignaciones de roles y, a continuación, seleccione **Quitar acceso**.   

![Elimine una asignación de roles para revocar el acceso](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Recuerde que los cambios en las asignaciones de roles tardarán de 2 a 5 minutos en surtir efecto.   

## <a name="next-steps"></a>Pasos siguientes

[Descripción de los roles de Synapse RBAC necesarios para realizar tareas comunes](./synapse-workspace-understand-what-role-you-need.md)