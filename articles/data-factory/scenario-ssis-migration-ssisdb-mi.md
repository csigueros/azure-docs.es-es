---
title: Migración de SSIS con Instancia administrada de Azure SQL como destino de la carga de trabajo de base de datos
description: Migración de SSIS con Instancia administrada de Azure SQL como destino de la carga de trabajo de base de datos.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 4696a5405be18ca5ef0f95df9eea20756c02cacf
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852206"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migración de SSIS con Instancia administrada de Azure SQL como destino de la carga de trabajo de base de datos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Para migrar cargas de trabajo de base de datos de una instancia de SQL Server a Instancia administrada de Azure SQL, debe estar familiarizado con [Azure Data Migration Service](../dms/dms-overview.md) (DMS) y las [topologías de red para migraciones de Instancia administrada de SQL con DMS](../dms/resource-network-topologies.md).

Este artículo se centra en la migración de los paquetes de SQL Server Integration Services (SSIS) almacenados en el catálogo de SSIS (SSISDB) y los trabajos del Agente SQL Server que programan las ejecuciones de paquetes de SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migración del catálogo de SSIS (SSISDB)

La migración de SSISDB se puede realizar con DMS, tal como se describe en el artículo: [Migración de paquetes de SQL Server Integration Services a una instancia administrada de Azure SQL Database](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Migración de trabajos de SSIS al agente de Instancia administrada de SQL

Instancia administrada de SQL tiene un programador nativo de primera clase como el Agente SQL Server local.  Puede [ejecutar paquetes SSIS mediante el agente de Azure SQL Managed Instance](how-to-invoke-ssis-package-managed-instance-agent.md).

Puesto que aún no hay disponible una herramienta de migración para los trabajos de SSIS, se deben migrar del Agente SQL Server local al agente de Instancia administrada de SQL mediante scripts o copia manual.

## <a name="additional-resources"></a>Recursos adicionales

- [Azure Data Factory](./introduction.md)
- [Azure-SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [Topologías de red para migraciones de Instancia administrada de Azure SQL Database mediante Azure Database Migration Service](../dms/resource-network-topologies.md)
- [Migración de paquetes de SQL Server Integration Services a una instancia administrada de Azure SQL Database](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Pasos siguientes

- [Conexión a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Ejecución de paquetes de SSIS implementados en Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)