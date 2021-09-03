---
title: Servicios de base de datos de Azure Alemania | Microsoft Docs
description: Este artículo proporciona una comparación de los servicios de base de datos de SQL de Azure Alemania.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 6316c381a601d1a28c3f6ecf529b3d31526bfd45
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029140"
---
# <a name="azure-germany-database-services"></a>Servicios de base de datos de Azure Alemania

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="sql-database"></a>SQL Database
Azure SQL Database y Azure SQL Managed Instance V12 están disponibles con carácter general en Azure Alemania. Para obtener instrucciones sobre la configuración de visibilidad de los metadatos y los procedimientos recomendados de protección, consulte los artículos sobre el [centro de seguridad de Microsoft para el motor de base de datos SQL](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database), la [documentación global de SQL Database](../azure-sql/database/index.yml) y la [documentación global de SQL Managed Instance](../azure-sql/managed-instance/index.yml).

### <a name="variations"></a>Variaciones
La dirección de SQL Database en Azure Alemania es diferente de la dirección de Azure global:

| Tipo de servicio | Azure global | Azure Alemania |
| --- | --- | --- |
| SQL Database | *.database.windows.net | .database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Para más información sobre Azure Cache for Redis y cómo usarlo, consulte [Documentación global de Azure Cache for Redis](../azure-cache-for-redis/index.yml).

### <a name="variations"></a>Variaciones
Las direcciones URL para acceder a Azure Cache for Redis y administrarlo en Azure Alemania son diferentes de las direcciones URL de Azure global:

| Tipo de servicio | Azure global | Azure Alemania |
| --- | --- | --- |
| Punto de conexión de caché | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Azure portal | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> Todos los scrips y todo el código deben tener en cuenta los puntos de conexión y entornos adecuados. Para más información, consulte "Conexión a Microsoft Azure Alemania" en [Administración de Azure Cache for Redis con Azure PowerShell](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md).
>
>


## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones, suscríbase al [blog de Azure Alemania](/archive/blogs/azuregermany/).