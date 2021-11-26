---
title: Modelo de compra de núcleo virtual
titleSuffix: Azure SQL Database & SQL Managed Instance
description: El modelo de compra de núcleo virtual permite escalar los recursos de proceso y de almacenamiento de manera independiente, igualar el rendimiento local y optimizar el precio para Azure SQL Database e Instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 05/18/2021
ROBOTS: NOINDEX
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 67e79d8b307de6327f3bacf2bfd57f95080b50d1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553199"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Información general del modelo de núcleo virtual: Azure SQL Database y Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

El modelo de compra de núcleo virtual usado por Azure SQL Database e Instancia administrada de Azure SQL proporciona varias ventajas:

- Control sobre la generación de hardware para satisfacer mejor los requisitos de proceso y memoria de la carga de trabajo.
- Descuentos en los precios de [Ventaja híbrida de Azure (AHB)](../azure-hybrid-benefit.md) e [Instancia reservada (RI)](reserved-capacity-overview.md).
- Mayor transparencia en los detalles de hardware que potencian el proceso, lo que facilita la planeación de las migraciones desde implementaciones locales.
- En el caso de Azure SQL Database, el modelo de compra de núcleo virtual proporciona mayores límites de proceso, memoria, E/S y almacenamiento que el modelo DTU.

Para más información sobre cómo elegir entre los modelos de compra de núcleos virtuales y DTU, consulte [Elección entre los modelos de compra de núcleo virtual y DTU: Azure SQL Database y SQL Managed Instance](purchasing-models.md).

> [!div class="nextstepaction"]
> [Encuesta para mejorar Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="service-tiers"></a>Niveles de servicio

En los artículos siguientes se proporciona información específica sobre el modelo de compra de núcleo virtual en cada producto.

- Para obtener información sobre los niveles de servicio de Azure SQL Database para el modelo de núcleo virtual, consulte [Introducción al modelo de núcleo virtual: Azure SQL Database](service-tiers-sql-database-vcore.md).
- Para obtener información sobre los niveles de servicio de Azure SQL Managed Instance para el modelo de núcleo virtual, consulte [Introducción al modelo de núcleo virtual: Azure SQL Managed Instance](../managed-instance/service-tiers-managed-instance-vcore.md).

## <a name="next-steps"></a>Pasos siguientes

Para empezar, consulte: 
- [Creación de una instancia de SQL Database mediante Azure Portal](single-database-create-quickstart.md)
- [Creación de una instancia administrada de SQL mediante Azure Portal](../managed-instance/instance-create-quickstart.md)

- Para ver los detalles de precios, consulte: 
    - [Página de precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)
    - [Página de precios de instancia única de Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Página de precios de grupos de Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
    
Para obtener más información sobre los tamaños específicos de proceso y almacenamiento disponibles en los niveles de servicio De uso general y Crítico para la empresa, consulte:

- [Límites de recursos basados en núcleos virtuales de Azure SQL Database](resource-limits-vcore-single-databases.md).
- [Límites de recursos basados en núcleos virtuales de Azure SQL Database agrupada](resource-limits-vcore-elastic-pools.md).
- [Límites de recursos basados en núcleos virtuales para Instancia administrada de Azure SQL](../managed-instance/resource-limits.md).
