---
title: Cifrado de datos transparente (Portal) para un grupo de SQL dedicado (anteriormente SQL DW)
description: Cifrado de datos transparente (TDE) de un grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/23/2021
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: ddaa7656b5d926b3bf7fdb34548eafe99e9bcf89
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113092066"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Introducción del Cifrado de datos transparente (TDE) de un grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics

> [!div class="op_single_selector"]
>
> * [Información general sobre seguridad](sql-data-warehouse-overview-manage-security.md)
> * [Autenticación](sql-data-warehouse-authentication.md)
> * [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

> [!NOTE]
> Este artículo se aplica a Azure SQL Database, Azure SQL Managed Instance y Azure Synapse Analytics (grupos de SQL dedicados [anteriormente SQL DW]). Para obtener documentación sobre el Cifrado de datos transparente para grupos de SQL dedicados en áreas de trabajo de Synapse, consulte [Cifrado de Azure Synapse Analytics](../security/workspaces-encryption.md).

## <a name="required-permissions"></a>Permisos necesarios

Para habilitar el Cifrado de datos transparente (TDE), debe ser un administrador o un miembro del rol dbmanager.

## <a name="enabling-encryption"></a>Habilitar el cifrado

Para habilitar TDE, siga los pasos que aparecen a continuación:

1. Abra la base de datos en el [Portal de Azure](https://portal.azure.com)
2. En la hoja de la base de datos, haga clic en el botón **Configuración**
3. Seleccione la opción **Cifrado de datos transparente**.![configuración del portal](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Seleccione la opción **Activado**.![configuración del portal: activado](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Seleccione **Guardar**.
   ![configuración del portal: guardar](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Deshabilitar el cifrado

Para deshabilitar TDE, siga los pasos que aparecen a continuación:

1. Abra la base de datos en el [Portal de Azure](https://portal.azure.com)
2. En la hoja de la base de datos, haga clic en el botón **Configuración**
3. Seleccione la opción **Cifrado de datos transparente**.![configuración del portal](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Seleccione la opción **Desactivado**.![configuración del portal: desactivado](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Seleccione **Guardar**.
   ![configuración del portal: guardar 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>DMV de cifrado

El cifrado se puede confirmar con las siguientes DMV:

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
