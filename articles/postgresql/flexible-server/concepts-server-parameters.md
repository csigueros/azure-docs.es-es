---
title: 'Parámetros del servidor: Azure Database for PostgreSQL: servidor flexible'
description: 'Describe los parámetros del servidor en Azure Database for PostgreSQL: servidor flexible'
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/04/2021
ms.openlocfilehash: 662faef8b7a3afbf3d3d3b021c52754af3a3c0bc
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559921"
---
# <a name="server-parameters-in-azure-database-for-postgresql---flexible-server"></a>Parámetros del servidor en Azure Database for PostgreSQL: servidor flexible

> [!IMPORTANT]
> Servidor flexible de Azure Database for PostgreSQL está en versión preliminar

Azure Database for PostgreSQL proporciona un subconjunto de parámetros que pueden configurarse para cada servidor. Para obtener más información sobre los parámetros de Postgres, consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/13/config-setting.html).

## <a name="an-overview-of-postgresql-parameters"></a>Introducción a los parámetros de PostgreSQL 

El servidor de Azure Database for PostgreSQL se preconfigura con los valores predeterminados óptimos para cada parámetro en el momento de la creación. Los parámetros estáticos requieren un reinicio del servidor y los que requieren acceso de superusuario no los puede configurar el usuario. 

Para revisar qué parámetros están disponibles para ver o modificar, se recomienda ir a Azure Portal y a la página Parámetros del servidor. También puede configurar los parámetros por usuario o por base de datos con los comandos `ALTER DATABASE` o `ALTER ROLE`.

>[!NOTE]
> Dado que Azure Database for PostgreSQL es un servicio de base de datos administrado, no se proporciona a los usuarios acceso al host o al sistema operativo para ver o modificar archivos de configuración como `postgresql.conf`. El contenido del archivo se actualiza automáticamente en función de los cambios de parámetros en la página Parámetros del servidor.

:::image type="content" source="./media/concepts-server-parameters/server-parameters.png" alt-text="Parámetros del servidor: portal":::

A continuación se enumeran algunos de los parámetros:

| Nombre de parámetro             | Descripción |
|----------------------|--------|
| **max_connections** | Puede ajustar max_connections en el servidor flexible de Postgres, donde puede establecerse en 5000 conexiones. Consulte la [documentación sobre límites](concepts-limits.md) para obtener más detalles. | 
| **shared_buffers**    | La configuración "shared_buffers" cambia en función de la SKU seleccionada (la SKU determina la memoria disponible). Los servidores de uso general tienen un valor de "shared_buffers" de 2 GB para dos núcleos virtuales; los servidores optimizados para memoria tienen un valor de "shared_buffers" de 4 GB para dos núcleos virtuales. El valor de shared_buffers se escala linealmente (aproximadamente) a medida que los núcleos virtuales aumentan en un nivel. | 
| **shared_preload_libraries** | Este parámetro está disponible para la configuración con un conjunto predefinido de extensiones admitidas. Tenga en cuenta que siempre se carga la extensión `azure` (que se usa para tareas de mantenimiento) y la extensión `pg_stat_statements` (puede usar el parámetro pg_stat_statements.track para controlar si la extensión está activa). |
| **connection_throttling** | Puede habilitar o deshabilitar la limitación de conexiones temporales por IP si hay demasiados errores de inicio de sesión con una contraseña no válida. |
 
## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las extensiones de PostgreSQL admitidas,consulte el [documento de extensiones](concepts-extensions.md).
