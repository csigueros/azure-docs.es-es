---
title: 'Parámetros del servidor: Azure Database for PostgreSQL: servidor flexible'
description: 'Describe los parámetros del servidor en Azure Database for PostgreSQL: servidor flexible'
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d8690bd33f10744b6d9cc9cd671e1ee46b102793
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711899"
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
 | **work_mem** | Este parámetro establece la cantidad de memoria que se va a usar en las operaciones de ordenación internas y las tablas hash antes de escribir en los archivos de disco temporales. Si la carga de trabajo tiene pocas consultas con una gran cantidad de ordenación compleja y cuenta con una gran cantidad de memoria, aumentar este parámetro puede permitir que Postgres realice exámenes más grandes en memoria frente a desbordamientos en el disco, lo que será más rápido.  Sin embargo, tenga cuidado, ya que una consulta compleja puede tener un número de operaciones hash de ordenación que se ejecutan simultáneamente. Cada una de esas operaciones usará tanta memoria como su valor permita antes de empezar a escribir en archivos temporales basados en disco. Por lo tanto, en un sistema relativamente ocupado, el uso total de memoria será muchas veces del parámetro work_mem. Si decide ajustar este valor globalmente, puede usar la fórmula RAM total * 0,25 / max_connections como valor inicial. Azure Database for PostgreSQL: servidor flexible admite un intervalo de 4096-2097151 bytes para este parámetro.|
| **effective_cache_size** |El parámetro effective_cache_size calcula cuánta memoria está disponible para el almacenamiento en caché de disco por parte del sistema operativo y dentro de la propia base de datos. El planificador de consultas de PostgreSQL decide si se ha corregido en RAM o no. Los exámenes de índice se suelen usar con valores más altos. Si el valor es bajo, se usarán exámenes secuenciales. Las recomendaciones son establecer Effective_cache_size en el 50 % de la memoria RAM total de la máquina. |
| **maintenance_work_mem** | Este parámetro proporciona básicamente la cantidad máxima de memoria que se usará en las operaciones de mantenimiento, como vacío, creación de índices, modificación de tablas y adición de claves externas.  El valor predeterminado para ese parámetro es de 64 KB. Se recomienda establecer este valor por encima de work_mem, ya que así se puede mejorar el rendimiento de la limpieza. |
| **effective_io_concurrency** | Establece el número de operaciones de E/S de disco simultáneas que PostgreSQL espera que se puedan ejecutar simultáneamente. Si aumenta este valor, también lo hace el número de operaciones de E/S que cualquier sesión de PostgreSQL individual intenta iniciar en paralelo. El intervalo permitido es de 1 a 1000 o cero para deshabilitar la emisión de solicitudes de E/S asincrónicas. Actualmente, esta configuración solo afecta a los exámenes del montón de mapa de bits. |
 |**require_secure_transport** | Si la aplicación no admite la conectividad SSL con el servidor, puede deshabilitar opcionalmente el transporte protegido desde el cliente si cambia a `OFF` este valor de parámetro. |

>[!NOTE]
> A medida que escale o reduzca verticalmente las SKU de Azure Database for PostgreSQL: servidor flexible, lo que afecta a la memoria disponible para el servidor, es posible que quiera optimizar los parámetros globales de memoria, como work_mem o effective_cache_size en función de la información anterior. 

 
## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las extensiones de PostgreSQL admitidas,consulte el [documento de extensiones](concepts-extensions.md).
