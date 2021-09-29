---
title: Visualización de las actividades de actualización e inicio de sesión para identidades administradas
description: Instrucciones paso a paso para ver las actividades realizadas en identidades administradas y las autenticaciones realizadas por identidades administradas
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03ab5ed10dcb9127c5b62850228d22222b4225be
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814422"
---
# <a name="view-update-and-sign-in-activities-for-managed-identities"></a>Visualización de las actividades de actualización e inicio de sesión para identidades administradas

En este artículo se explica cómo ver las actualizaciones realizadas en identidades administradas y los intentos de inicio de sesión realizados por identidades administradas.

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con las identidades administradas de los recursos de Azure, consulte la [sección de introducción](overview.md).
- Si aún no tiene una cuenta de Azure, [regístrese para obtener una cuenta gratuita](https://azure.microsoft.com/free/).

## <a name="view-updates-made-to-user-assigned-managed-identities"></a>Visualización de las actualizaciones realizadas en identidades administradas asignadas por el usuario

En este procedimiento se muestra cómo ver las actualizaciones realizadas en identidades administradas asignadas por el usuario

1. En Azure Portal, vaya a **Registro de actividad**.

 ![Navegación al registro de actividad en Azure Portal](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

2. Seleccione **Add Filter** (Agregar filtro) y luego **Operación** (Operación) en la lista.

![Inicio de la creación del filtro de búsqueda](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

3. En la lista desplegable **Operación** (Operación), escriba estos nombres de operación: "Eliminar identidad asignada por el usuario" y "Escribir userAssignedIdentities".

![Agregar operaciones al filtro de búsqueda](./media/how-to-view-managed-identity-activity/add-operations-to-search-filter.png)

4. Cuando se muestren las operaciones de búsqueda de coincidencias, seleccione una para ver el resumen.

![Vista de resumen de la operación](./media/how-to-view-managed-identity-activity/view-summary-of-operation.png)

5. Seleccione la pestaña **JSON** para ver información más detallada sobre la operación y desplácese hasta el nodo de **propiedades** para ver información sobre la identidad que se modificó.

![Visualización de los detalles de la operación](./media/how-to-view-managed-identity-activity/view-json-of-operation.png)

## <a name="view-role-assignments-added-and-removed-for-managed-identities"></a>Visualización de las asignaciones de roles agregadas y eliminadas para identidades administradas

 > [!NOTE] 
 > Deberá buscar por el identificador de objeto (entidad de seguridad) de la identidad administrada para la que quiere ver los cambios de asignación de roles.

1. Busque la identidad administrada para la que quiere ver los cambios de asignación de roles. Si busca una identidad administrada asignada por el sistema, el identificador de objeto se mostrará en la pantalla **Identidad** en el recurso. Si busca una identidad asignada por el usuario, el identificador de objeto se mostrará en la página **Información general** de la identidad administrada.

Identidad asignada por el usuario:

![Obtención del identificador de objeto de la identidad asignada por el usuario](./media/how-to-view-managed-identity-activity/get-object-id-of-user-assigned-identity.png)

Identidad asignada por el sistema:

![Obtención del identificador de objeto para la identidad asignada por el sistema](./media/how-to-view-managed-identity-activity/get-object-id-of-system-assigned-identity.png)

2. Copie el identificador de objeto.
3. Vaya al **Registro de actividad**.

 ![Navegación al registro de actividad en Azure Portal](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

4. Seleccione **Add Filter** (Agregar filtro) y luego **Operación** (Operación) en la lista.

![Inicio de la creación del filtro de búsqueda](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

5. En la lista desplegable **Operación** (Operación), escriba estos nombres de operación: "Crear asignación de roles" y "Eliminar asignación de roles".

![Adición de operaciones de asignación de roles al filtro de búsqueda](./media/how-to-view-managed-identity-activity/add-role-assignment-operations-to-search-filter.png)

6. Pegue el identificador de objeto en el cuadro de búsqueda; los resultados se filtrarán automáticamente.

![Búsqueda por identificador de objeto](./media/how-to-view-managed-identity-activity/search-by-object-id.png)
 
7. Cuando se muestren las operaciones de búsqueda de coincidencias, seleccione una para ver el resumen.
 
![Resumen de la asignación de roles para la identidad administrada](./media/how-to-view-managed-identity-activity/summary-of-role-assignment-for-msi.png)

## <a name="view-authentication-attempts-by-managed-identities"></a>Visualización de los intentos de autenticación por identidades administradas

1. Vaya a **Azure Active Directory**.

![Navegación hasta Active Directory](./media/how-to-view-managed-identity-activity/browse-to-active-directory.png)

2.  Seleccione **Sign-in logs** (Registros de inicio de sesión) en la sección **Monitoring** (Supervisión).

![Selección de los registros de inicio de sesión](./media/how-to-view-managed-identity-activity/sign-in-logs-menu-item.png)

3. Seleccione la pestaña **Managed identity sign-ins** (Inicios de sesión de identidades administradas).

![inicio de sesión de identidad administrada](./media/how-to-view-managed-identity-activity/msi-sign-ins.png)

4. Ahora, las identidades administradas filtrarán los eventos de inicio de sesión.

![eventos de inicio de sesión de identidad administrada](./media/how-to-view-managed-identity-activity/msi-sign-in-events.png) 

5.  Para ver la aplicación de identidad Enterprise en Azure Active Directory, seleccione la columna "Id. de identidad administrada".
6.  Para ver el recurso de Azure o la identidad administrada asignada por el usuario, busque por nombre en la barra de búsqueda de Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

* [Identidades administradas para recursos de Azure](./overview.md)
* [Registro de actividad de Azure](../../azure-monitor/essentials/activity-log.md)
* [Registro de inicios de sesión de Azure Active Directory](../reports-monitoring/concept-sign-ins.md)