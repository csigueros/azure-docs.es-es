---
title: Grupo de confianza del servidor
titleSuffix: Azure SQL Managed Instance
description: Obtenga información sobre cómo administrar la confianza entre instancias mediante un grupo de confianza del servidor en Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ad3d7c45fb621d3f12146548bdb9e5ac5693a622
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072029"
---
# <a name="set-up-trust-between-instances-with-server-trust-group-azure-sql-managed-instance"></a>Configuración de la confianza entre instancias con un grupo de confianza del servidor (Azure SQL Managed Instance)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

El concepto de grupo de confianza del servidor (también conocido como grupo de confianza de SQL) se usa para administrar la confianza entre instancias en Azure SQL Managed Instance. Al crear un grupo, se establece una confianza basada en certificados entre sus miembros. Esta confianza se puede usar para diferentes escenarios entre instancias. Al quitar servidores del grupo o eliminar el grupo, se quita la confianza entre los servidores. Para crear o eliminar un grupo de confianza del servidor, el usuario debe tener permisos de escritura en la instancia administrada.
Un [grupo de confianza del servidor](/azure/templates/microsoft.sql/allversions) es un objeto de Azure Resource Manager que se ha etiquetado como **grupo de confianza de SQL** en Azure Portal.


## <a name="set-up-group"></a>Configuración del grupo

El grupo de confianza del servidor se puede configurar mediante [Azure PowerShell](/powershell/module/az.sql/new-azsqlservertrustgroup) o la [CLI de Azure](/cli/azure/sql/stg). 

Para crear un grupo de confianza del servidor mediante Azure Portal, siga estos pasos: 

1. Vaya a [Azure Portal](https://portal.azure.com/).

2. Vaya a la instancia de Azure SQL Managed Instance que tiene previsto agregar a un grupo de confianza del servidor.

3. En la configuración de **Seguridad**, seleccione la pestaña **SQL trust groups** (Grupos de confianza SQL).

   :::image type="content" source="./media/server-trust-group-overview/sql-trust-groups.png" alt-text="Grupos de confianza de SQL":::

4. En la página de configuración de los **grupos de confianza de SQL**, seleccione el icono **Nuevo grupo**.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group-button.png" alt-text="Nuevo grupo":::

5. En la hoja de creación del **grupo de confianza de SQL**, defina el **Nombre del grupo**. Debe ser único en el grupo de recursos, la región y la suscripción del grupo. El **ámbito de confianza** define el tipo de escenario entre instancias que se habilita con el grupo de confianza del servidor. El ámbito de confianza es fijo: todas las funcionalidades disponibles están preseleccionadas y esto no se puede cambiar. Seleccione **Suscripción** y **Grupo de recursos** para elegir las instancias administradas que serán miembros del grupo.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group.png" alt-text="Hoja de creación del grupo de confianza de SQL":::

6. Una vez que se hayan completado todos los campos obligatorios, seleccione **Guardar**.

## <a name="edit-group"></a>Editar grupo 

Para editar un grupo de confianza del servidor, siga estos pasos: 

1. Vaya a Azure Portal.
1. Navegue a una instancia administrada que pertenezca al grupo de confianza.
1. En la configuración de **Seguridad**, seleccione la pestaña **SQL trust groups** (Grupos de confianza SQL).
1. Seleccione el grupo de confianza que desea editar.
1. Haga clic en **Configurar grupo**.

   :::image type="content" source="./media/server-trust-group-overview/configure-sql-trust-group.png" alt-text="Configuración del grupo de confianza de SQL":::

1. Agregue o quite instancias administradas del grupo.
1. Haga clic en **Guardar** para confirmar la elección o en **Cancelar** para abandonar los cambios.

## <a name="delete-group"></a>Eliminar grupo

Para eliminar un grupo de confianza del servidor, siga estos pasos: 

1. Vaya a Azure Portal.
1. Navegue a una instancia administrada que pertenezca al grupo de confianza de SQL.
1. En la configuración de **Seguridad**, seleccione la pestaña **SQL trust groups** (Grupos de confianza SQL).
1. Seleccione el grupo de recursos que quiere eliminar.

   :::image type="content" source="./media/server-trust-group-overview/select-delete-sql-trust-group.png" alt-text="Selección de un grupo de confianza de SQL":::

1. Seleccione **Eliminar grupo**.

   :::image type="content" source="./media/server-trust-group-overview/delete-sql-trust-group.png" alt-text="Eliminación de un grupo de confianza de SQL"::: 

1. Escriba el nombre del grupo de confianza de SQL para confirmar la eliminación y seleccione **Eliminar**.

   :::image type="content" source="./media/server-trust-group-overview/confirm-delete-sql-trust-group-2.png" alt-text="Confirmación de la eliminación del grupo de confianza de SQL":::

> [!NOTE]
> Es posible que la eliminación del grupo de confianza de SQL no quite inmediatamente la confianza entre las dos instancias administradas. Para aplicar la eliminación de la confianza, puede invocar la [conmutación por error](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) de instancias administradas. Consulte los [Problemas conocidos](../managed-instance/doc-changes-updates-known-issues.md) para obtener información actualizada sobre este tema.

## <a name="limitations"></a>Limitaciones

Las siguientes limitaciones se aplican a los grupos de confianza del servidor: 

 * El grupo solo puede contener instancias de Azure SQL Managed Instance.
 * No se puede cambiar el ámbito de confianza cuando se crea o modifica un grupo.
 * El nombre del grupo de confianza del servidor debe ser único para su suscripción, grupo de recursos y región.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información acerca de las transacciones distribuidas en Azure SQL Managed Instance, consulte [Transacciones distribuidas](../database/elastic-transactions-overview.md).
* Para obtener las actualizaciones de versión y el estado de los problemas conocidos, vea [Novedades](doc-changes-updates-release-notes-whats-new.md).
* Si tiene solicitudes de características, agréguelas al [foro de SQL Managed Instance](https://feedback.azure.com/forums/915676-sql-managed-instance).
