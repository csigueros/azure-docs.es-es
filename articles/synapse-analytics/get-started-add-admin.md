---
title: 'Inicio rápido: Introducción a la incorporación de administradores'
description: En este tutorial, aprenderá a agregar un usuario administrativo a un área de trabajo.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7a03715a89b5319e341d1704719b020e1b61ef1d
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113219427"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Incorporación de un administrador a un área de trabajo de Synapse

En este tutorial, aprenderá a agregar un usuario administrativo a un área de trabajo de Synapse. Este usuario tendrá control total sobre el área de trabajo.

## <a name="overview"></a>Información general

Hasta ahora, en la guía de introducción nos hemos centrado en las actividades que *se* realizan en el área de trabajo. Como ha creado el área de trabajo en el paso 1, es administrador del área de trabajo de Synapse. Ahora, vamos a hacer que otro usuario, Ryan (`ryan@contoso.com`), sea administrador. Cuando hayamos terminado, Ryan podrá hacer en el área de trabajo exactamente todo lo que pueda hacer usted.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: rol de Propietario para el área de trabajo

1. Abra Azure Portal y abra el área de trabajo de Synapse.
1. A la izquierda, seleccione **Control de acceso (IAM)** .
1. Seleccione **Agregar** > **Agregar asignación de roles** para abrir la página Agregar asignación de roles.
1. Asigne el siguiente rol. Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).
    
    | Configuración | Valor |
    | --- | --- |
    | Role | Propietario |
    | Asignar acceso a | USER |
    | Miembro | ryan@contoso.com |

    ![Página Agregar asignación de roles en Azure Portal.](../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. Seleccione **Guardar**. 
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC: rol de Administrador de Synapse para el área de trabajo

Asigne `ryan@contoso.com` al rol de **Administrador de Synapse** de Synapse RBAC en el área de trabajo.

1. Abra el área de trabajo en Synapse Studio.
1. A la izquierda, seleccione **Administrar** para abrir el centro de administración.
1. En **Seguridad**, seleccione **Control de acceso**.
1. Seleccione **Agregar**.
1. En **Ámbito**, deje el valor **Área de trabajo**.
1. Agregue `ryan@contoso.com` al rol **Administrador de Synapse**. 
1. Luego, seleccione **Aplicar**.
 
## <a name="azure-rbac-role-assignments-on-the-workspaces-primary-storage-account"></a>Azure RBAC: asignaciones de roles en la cuenta de almacenamiento principal del área de trabajo

1. Abra la cuenta de almacenamiento principal del área de trabajo en Azure Portal.
1. A la izquierda, seleccione **Control de acceso (IAM)** .
1. Seleccione **Agregar** > **Agregar asignación de roles** para abrir la página Agregar asignación de roles.
1. Asigne el siguiente rol. Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).
    
    | Configuración | Value |
    | --- | --- |
    | Rol 1 | Propietario |
    | Rol 2| Colaborador de datos de Azure Storage Blob|
    | Asignar acceso a | USER |
    | Miembro | ryan@contoso.com |

    ![Página Agregar asignación de roles en Azure Portal.](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="dedicated-sql-pools-db_owner-role"></a>Grupos de SQL dedicados: rol de db_owner

Asigne `ryan@contoso.com` al rol de **db_owner** en todos los grupos de SQL dedicados del área de trabajo.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Azure Synapse Analytics](get-started.md)
* [Creación de un área de trabajo](quickstart-create-workspace.md)
* [Uso de grupos de SQL sin servidor](quickstart-sql-on-demand.md)
