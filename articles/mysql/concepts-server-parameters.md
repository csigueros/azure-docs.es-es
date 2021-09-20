---
title: 'Parámetros del servidor: Azure Database for MySQL'
description: En este tema se proporcionan instrucciones para configurar parámetros del servidor en Azure Database for MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/26/2021
ms.openlocfilehash: 0aeaf068de0bdcc06deb3e28575047b459b56b27
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426119"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Parámetros del servidor en Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

En este artículo se incluyen consideraciones e instrucciones para configurar parámetros del servidor en Azure Database for MySQL.

## <a name="what-are-server-parameters"></a>¿Cuáles son los parámetros del servidor? 

El motor MySQL proporciona muchas variables o parámetros de servidor diferentes que se pueden usar para configurar y ajustar el comportamiento del motor. Algunos parámetros se pueden establecer de forma dinámica en tiempo de ejecución, mientras que otros son "estáticos", requiriendo un reinicio del servidor para su aplicación.

Azure Database for MySQL expone la capacidad de cambio del valor de diversos parámetros del servidor MySQL mediante [Azure Portal](./howto-server-parameters.md), la [CLI de Azure](./howto-configure-server-parameters-using-cli.md) y [PowerShell](./howto-configure-server-parameters-using-powershell.md) para satisfacer las necesidades de la carga de trabajo.

## <a name="configurable-server-parameters"></a>Parámetros configurables del servidor

La lista de parámetros del servidor admitidos crece constantemente. Use la pestaña Parámetros del servidor de Azure Portal para ver la lista completa y configurar valores de los parámetros del servidor.

Consulte las siguientes secciones para obtener más información sobre los límites de los diferentes parámetros del servidor actualizados con más frecuencia. Los límites vienen determinados por el plan de tarifa y los núcleos virtuales del servidor.

### <a name="thread-pools"></a>Agrupación de subprocesos

MySQL asigna tradicionalmente un subproceso para cada conexión de cliente. A medida que crece el número de usuarios simultáneos, hay una reducción correspondiente del rendimiento. Muchos subprocesos activos pueden afectar significativamente al rendimiento debido a un aumento del cambio de contexto, la contención de subprocesos y la localidad incorrecta de las memorias caché de la CPU.

La agrupación de subprocesos, que es una característica del lado servidor y es diferente a la agrupación de conexiones, maximiza el rendimiento al introducir un grupo dinámico de subprocesos de trabajo que se puede usar para limitar el número de subprocesos activos que se ejecutan en el servidor y minimizar la renovación del subproceso. Esto ayuda a garantizar que una ráfaga de conexiones no provocará que el servidor se quede sin recursos o se bloquee con un error de memoria insuficiente. Los grupos de subprocesos son más eficaces para las consultas cortas y las cargas de trabajo de gran consumo de CPU, por ejemplo, las cargas de trabajo OLTP.

Para obtener más información sobre los grupos de subprocesos, consulte el artículo sobre la [presentación de los grupos de subprocesos en Azure Database for MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)

> [!NOTE]
> La característica de grupo de subprocesos no se admite en MySQL versión 5.6. 

### <a name="configuring-the-thread-pool"></a>Configuración del grupo de subprocesos
Para habilitar el grupo de subprocesos, actualice el parámetro del servidor `thread_handling` a "pool-of-threads". De forma predeterminada, este parámetro se establece en `one-thread-per-connection`, lo que significa que MySQL crea un nuevo subproceso para cada nueva conexión. Tenga en cuenta que se trata de un parámetro estático y requiere que se reinicie el servidor.

También puede configurar el número máximo y mínimo de subprocesos del grupo mediante el establecimiento de los siguientes parámetros del servidor: 
- `thread_pool_max_threads`: este valor garantiza que no habrá una cantidad superior a este número de subprocesos en el grupo.
- `thread_pool_min_threads`: este valor establece el número de subprocesos que se reservarán incluso después de cerrarse las conexiones.

Para mejorar los problemas de rendimiento de las consultas cortas del grupo de subprocesos, Azure Database for MySQL permite habilitar la ejecución de lotes donde, en lugar de volver al grupo de subprocesos inmediatamente después de ejecutar una consulta, los subprocesos permanecerán activos durante un breve período de tiempo de espera para la siguiente consulta a través de esta conexión. Después, el subproceso ejecuta la consulta rápidamente y, una vez finalizada, espera a la siguiente, hasta que el consumo de tiempo total de este proceso supera un umbral. El comportamiento de la ejecución por lotes se determina mediante los siguientes parámetros del servidor:  

-  `thread_pool_batch_wait_timeout`: este valor especifica el tiempo que espera un subproceso para que se procese otra consulta.
- `thread_pool_batch_max_time`: este valor determina el tiempo máximo que empleará un subproceso para repetir el ciclo de ejecución de la consulta y esperar a la siguiente consulta.

> [!IMPORTANT]
> Pruebe el grupo de subprocesos antes de activarlo en producción. 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

En Azure Database for MySQL, siempre están habilitados los registros binarios (p. ej., `log_bin` está establecido en ACTIVADO). En el caso de que desee usar desencadenadores, obtendrá un error similar a *you do not have the SUPER privilege and binary logging is enabled (you might want to use the less safe `log_bin_trust_function_creators` variable)* [No tiene el privilegio SUPER y el registro binario está habilitado (es posible que desee usar la variable menos segura)]. 

El formato de registro binario siempre es **ROW** y todas las conexiones al servidor **ALWAYS** usan un registro binario basado en filas. Con el registro binario basado en filas, no existen problemas de seguridad y el registro binario no se puede interrumpir, de modo que puede establecer [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) en **TRUE** de forma segura.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Consulte la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) para más información acerca de este parámetro.

#### <a name="servers-on-general-purpose-storage-v1-supporting-up-to-4-tb"></a>Servidores con [almacenamiento de uso general v1 (admite hasta 4 TB)](concepts-pricing-tiers.md#general-purpose-storage-v1-supports-up-to-4-tb)

|**Plan de tarifa**|**Núcleos virtuales**|**Valor predeterminado (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|872415232|134217728|872415232|
|Básico|2|2684354560|134217728|2684354560|
|De uso general|2|3758096384|134217728|3758096384|
|De uso general|4|8053063680|134217728|8053063680|
|De uso general|8|16106127360|134217728|16106127360|
|De uso general|16|32749125632|134217728|32749125632|
|De uso general|32|66035122176|134217728|66035122176|
|De uso general|64|132070244352|134217728|132070244352|
|Memoria optimizada|2|7516192768|134217728|7516192768|
|Memoria optimizada|4|16106127360|134217728|16106127360|
|Memoria optimizada|8|32212254720|134217728|32212254720|
|Memoria optimizada|16|65498251264|134217728|65498251264|
|Memoria optimizada|32|132070244352|134217728|132070244352|

#### <a name="servers-on-general-purpose-storage-v2-supporting-up-to-16-tb"></a>Servidores con [almacenamiento de uso general v2 (admite hasta 16 TB)](concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage)

|**Plan de tarifa**|**Núcleos virtuales**|**Valor predeterminado (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|872415232|134217728|872415232|
|Básico|2|2684354560|134217728|2684354560|
|De uso general|2|7516192768|134217728|7516192768|
|De uso general|4|16106127360|134217728|16106127360|
|De uso general|8|32212254720|134217728|32212254720|
|De uso general|16|65498251264|134217728|65498251264|
|De uso general|32|132070244352|134217728|132070244352|
|De uso general|64|264140488704|134217728|264140488704|
|Memoria optimizada|2|15032385536|134217728|15032385536|
|Memoria optimizada|4|32212254720|134217728|32212254720|
|Memoria optimizada|8|64424509440|134217728|64424509440|
|Memoria optimizada|16|130996502528|134217728|130996502528|
|Memoria optimizada|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` solo se puede actualizar en los planes de tarifa de uso general y optimizada para memoria en [almacenamiento de uso general v2](concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage).

MySQL almacena la tabla InnoDB en distintos espacios de tabla en función de la configuración proporcionada durante la creación de la tabla. El [espacio de tablas del sistema](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) es el área de almacenamiento del diccionario de datos de InnoDB. Un [espacio de tabla de archivo por tabla](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) contiene datos e índices para una sola tabla de InnoDB y se almacena en el sistema de archivos, en su propio archivo de datos. Este comportamiento se controla mediante el parámetro de servidor `innodb_file_per_table`. Si `innodb_file_per_table` se establece en `OFF`, InnoDB crea tablas en el espacio de tablas del sistema. De lo contrario, InnoDB crea tablas en espacios de tabla de archivo por tabla.

Azure Database for MySQL admite **4 TB** como máximo en un solo archivo de datos en el [almacenamiento de uso general v2](concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage). Si el tamaño de la base de datos es superior a 4 TB, debería crear la tabla en el espacio de tabla [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table). Si tiene una sola tabla de tamaño superior a 4 TB, debería usar la tabla de particiones.

### <a name="join_buffer_size"></a>join_buffer_size

Consulte la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) para más información acerca de este parámetro.

|**Plan de tarifa**|**Núcleos virtuales**|**Valor predeterminado (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|No se puede configurar en el nivel Básico|N/D|N/D|
|Básico|2|No se puede configurar en el nivel Básico|N/D|N/D|
|De uso general|2|262 144|128|268435455|
|De uso general|4|262 144|128|536870912|
|De uso general|8|262 144|128|1073741824|
|De uso general|16|262 144|128|2147483648|
|De uso general|32|262 144|128|4294967295|
|De uso general|64|262 144|128|4294967295|
|Memoria optimizada|2|262 144|128|536870912|
|Memoria optimizada|4|262 144|128|1073741824|
|Memoria optimizada|8|262 144|128|2147483648|
|Memoria optimizada|16|262 144|128|4294967295|
|Memoria optimizada|32|262 144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Plan de tarifa**|**Núcleos virtuales**|**Valor predeterminado**|**Valor mínimo**|**Valor máximo**|
|---|---|---|---|---|
|Básico|1|50|10|50|
|Básico|2|100|10|100|
|De uso general|2|300|10|600|
|De uso general|4|625|10|1250|
|De uso general|8|1250|10|2.500|
|De uso general|16|2.500|10|5000|
|De uso general|32|5000|10|10000|
|De uso general|64|10000|10|20000|
|Memoria optimizada|2|625|10|1250|
|Memoria optimizada|4|1250|10|2.500|
|Memoria optimizada|8|2.500|10|5000|
|Memoria optimizada|16|5000|10|10000|
|Memoria optimizada|32|10000|10|20000|

Si las conexiones superan el límite, puede que reciba el error siguiente:
> ERROR 1040 (08004): Demasiadas conexiones

> [!IMPORTANT]
> Para obtener la mejor experiencia posible, se recomienda usar un agrupador de conexiones, como ProxySQL, para administrar las conexiones de forma eficaz.

La creación de conexiones de cliente a MySQL lleva tiempo y, una vez establecidas, estas conexiones ocupan recursos de bases de datos, incluso cuando están inactivas. La mayoría de las aplicaciones solicitan muchas conexiones de corta duración, y esto es lo que conforma esta situación. El resultado es que hay menos recursos disponibles para la carga de trabajo real, lo que baja el rendimiento. Esto se puede evitar con un agrupador de conexiones, ya que reduce las conexiones inactivas y reutiliza las conexiones existentes. Para más información sobre cómo configurar ProxySQL, consulte nuestra [entrada de blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL es una herramienta de la comunidad de código abierto. Es compatible con Microsoft dentro de lo posible. Para obtener soporte a la producción con instrucciones autoritativas, puede evaluar y ponerse en contacto con el [servicio de soporte técnico de ProxySQL](https://proxysql.com/services/support/).

### <a name="max_heap_table_size"></a>max_heap_table_size

Consulte la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) para más información acerca de este parámetro.

|**Plan de tarifa**|**Núcleos virtuales**|**Valor predeterminado (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|No se puede configurar en el nivel Básico|N/D|N/D|
|Básico|2|No se puede configurar en el nivel Básico|N/D|N/D|
|De uso general|2|16777216|16384|268435455|
|De uso general|4|16777216|16384|536870912|
|De uso general|8|16777216|16384|1073741824|
|De uso general|16|16777216|16384|2147483648|
|De uso general|32|16777216|16384|4294967295|
|De uso general|64|16777216|16384|4294967295|
|Memoria optimizada|2|16777216|16384|536870912|
|Memoria optimizada|4|16777216|16384|1073741824|
|Memoria optimizada|8|16777216|16384|2147483648|
|Memoria optimizada|16|16777216|16384|4294967295|
|Memoria optimizada|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

La caché de consulta está desactivada de forma predeterminada. Para habilitar la caché de consulta, configure el parámetro `query_cache_type`. 

Consulte la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) para más información acerca de este parámetro.

> [!NOTE]
> La caché de consulta está en desuso desde MySQL 5.7.20 y se quitó en MySQL 8.0.

|**Plan de tarifa**|**Núcleos virtuales**|**Valor predeterminado (bytes)**|**Valor mínimo (bytes)**|**Valor máximo **|
|---|---|---|---|---|
|Básico|1|No se puede configurar en el nivel Básico|N/D|N/D|
|Básico|2|No se puede configurar en el nivel Básico|N/D|N/D|
|De uso general|2|0|0|16777216|
|De uso general|4|0|0|33554432|
|De uso general|8|0|0|67108864|
|De uso general|16|0|0|134217728|
|De uso general|32|0|0|134217728|
|De uso general|64|0|0|134217728|
|Memoria optimizada|2|0|0|33554432|
|Memoria optimizada|4|0|0|67108864|
|Memoria optimizada|8|0|0|134217728|
|Memoria optimizada|16|0|0|134217728|
|Memoria optimizada|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

De manera predeterminada, lower_case_table_name está establecido en 1 y puede actualizar este parámetro en MySQL 5.6 y MySQL 5.7.

Consulte la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names) para más información acerca de este parámetro.

> [!NOTE]
> En MySQL 8.0, el valor de lower_case_table_name está establecido en 1 de manera predeterminada y no puede cambiarlo.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Si recibe un error similar a "Tamaño de la fila demasiado grande (> 8126)", es posible que quiera desactivar el parámetro **innodb_strict_mode**. El parámetro de servidor **innodb_strict_mode** no se puede modificar globalmente en el nivel de servidor porque, si el tamaño de los datos de fila es superior a 8 KB, se truncarán los datos sin que se produzca una posible pérdida de datos. Se recomienda modificar el esquema para ajustarlo al límite de tamaño de página. 

Este parámetro se puede establecer en un nivel de sesión mediante `init_connect`. Para establecer **innodb_strict_mode** en el nivel de sesión, consulte cómo [ajustar un parámetro que no aparece en la lista](./howto-server-parameters.md#setting-parameters-not-listed).

> [!NOTE]
> Si tiene un servidor de réplica de lectura, al establecer **innodb_strict_mode** como desactivado en el nivel de sesión de un servidor de origen, se interrumpirá la replicación. Se recomienda mantener el parámetro establecido así si tiene réplicas de lectura.

### <a name="sort_buffer_size"></a>sort_buffer_size

Consulte la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) para más información acerca de este parámetro.

|**Plan de tarifa**|**Núcleos virtuales**|**Valor predeterminado (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|No se puede configurar en el nivel Básico|N/D|N/D|
|Básico|2|No se puede configurar en el nivel Básico|N/D|N/D|
|De uso general|2|524 288|32 768|4 194 304|
|De uso general|4|524 288|32 768|8388608|
|De uso general|8|524 288|32 768|16777216|
|De uso general|16|524 288|32 768|33554432|
|De uso general|32|524 288|32 768|33554432|
|De uso general|64|524 288|32 768|33554432|
|Memoria optimizada|2|524 288|32 768|8388608|
|Memoria optimizada|4|524 288|32 768|16777216|
|Memoria optimizada|8|524 288|32 768|33554432|
|Memoria optimizada|16|524 288|32 768|33554432|
|Memoria optimizada|32|524 288|32 768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

Consulte la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) para más información acerca de este parámetro.

|**Plan de tarifa**|**Núcleos virtuales**|**Valor predeterminado (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|No se puede configurar en el nivel Básico|N/D|N/D|
|Básico|2|No se puede configurar en el nivel Básico|N/D|N/D|
|De uso general|2|16777216|1024|67108864|
|De uso general|4|16777216|1024|134217728|
|De uso general|8|16777216|1024|268435456|
|De uso general|16|16777216|1024|536870912|
|De uso general|32|16777216|1024|1073741824|
|De uso general|64|16777216|1024|1073741824|
|Memoria optimizada|2|16777216|1024|134217728|
|Memoria optimizada|4|16777216|1024|268435456|
|Memoria optimizada|8|16777216|1024|536870912|
|Memoria optimizada|16|16777216|1024|1073741824|
|Memoria optimizada|32|16777216|1024|1073741824|

### <a name="innodb-buffer-pool-warmup"></a>Preparación del grupo de búferes de InnoDB
Después de reiniciar el servidor de Azure Database for MySQL, las páginas de datos que residen en el disco se cargan cuando se consultan las tablas. Esto da lugar a una mayor latencia y un rendimiento más lento para la primera ejecución de las consultas. Esta situación puede no ser aceptable para cargas de trabajo sensibles a la latencia. El uso de la preparación del grupo de búferes de InnoDB acorta el período de preparación mediante la recarga de las páginas del disco que estaban en el grupo de búferes antes del reinicio, en lugar de esperar a que las operaciones DML o SELECT accedan a las filas correspondientes.

Puede reducir el período de preparación después de reiniciar el servidor de Azure Database for MySQL, lo que representa una ventaja de rendimiento mediante la configuración de los [parámetros del servidor del grupo de búferes de InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html). InnoDB guarda un porcentaje de las páginas usadas más recientemente para cada grupo de búferes al cerrar el servidor y restaura estas páginas al iniciar el servidor.

También es importante tener en cuenta que el rendimiento mejorado se produce a costa del tiempo de inicio más largo para el servidor. Cuando este parámetro está habilitado, se espera que el tiempo de inicio y reinicio del servidor aumente en función de las IOPS aprovisionadas en dicho servidor. Se recomienda probar y supervisar el tiempo de reinicio para garantizar que el rendimiento de inicio y reinicio es aceptable, ya que el servidor no está disponible durante ese tiempo. No se recomienda usar este parámetro cuando el aprovisionamiento de IOPS es inferior a 1000 IOPS (o en otras palabras, cuando el almacenamiento aprovisionado es inferior a 335 GB.

Para guardar el estado del grupo de búferes al cerrar el servidor, establezca el parámetro de servidor `innodb_buffer_pool_dump_at_shutdown` en `ON`. Del mismo modo, establezca el parámetro de servidor `innodb_buffer_pool_load_at_startup` en `ON` para restaurar el estado del grupo de búferes al iniciar el servidor. Puede controlar el impacto en el inicio o reinicio si reduce y ajusta el valor del parámetro de servidor `innodb_buffer_pool_dump_pct`. De forma predeterminada, este parámetro se establece en `25`.

> [!Note]
> Los parámetros de preparación del grupo de búferes de InnoDB solo se admiten en servidores de almacenamiento de uso general con un almacenamiento de hasta 16 TB. Obtenga más información sobre las [opciones de almacenamiento de Azure Database for MySQL aquí](./concepts-pricing-tiers.md#storage).

### <a name="time_zone"></a>time_zone

Tras la implementación inicial, un servidor de Azure para MySQL incluye tablas de sistemas para la información de zona horaria, pero estas tablas no se rellenan. Las tablas de la zona horaria se pueden rellenar mediante una llamada al procedimiento almacenado `mysql.az_load_timezone` desde una herramienta como la línea de comandos de MySQL o MySQL Workbench. Vea los artículos de [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) o de la [CLI de Azure](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) sobre cómo llamar al procedimiento almacenado y establecer las zonas horarias globales o de nivel de sesión.

### <a name="binlog_expire_logs_seconds"></a>binlog_expire_logs_seconds 

En Azure Database for MySQL, este parámetro especifica el número de segundos que el servicio espera antes de purgar el archivo de registro binario.

El registro binario contiene "eventos" que describen los cambios de la base de datos, como las operaciones de creación de tablas o los cambios en los datos de la tabla. También contiene eventos para instrucciones que puedan haber realizado cambios. El registro binario se usa con dos finalidades principalmente: las operaciones de replicación y de recuperación de datos.  Normalmente, los registros binarios se purgan en cuanto el identificador queda libre del servicio, de la copia de seguridad o del conjunto de réplicas. En el caso de varias réplicas, espera a que la réplica más lenta lea los cambios antes de purgarse. Si quiere conservar los registros binarios durante más tiempo, puede configurar el parámetro binlog_expire_logs_seconds. Si binlog_expire_logs_seconds se establece en 0, que es el valor predeterminado, se purgará en cuanto se libere el identificador para el registro binario. Si el valor de binlog_expire_logs_seconds es > 0, se esperaría durante los segundos configurados antes de purgar. En Azure Database for MySQL, las características administradas, como la copia de seguridad y la purga de réplicas de lectura de archivos binarios, se controlan internamente. Si se replican los datos fuera del servicio Azure Database for MySQL, este parámetro debe establecerse como primario para evitar la purga de registros binarios antes de que la réplica lea los cambios del elemento primario. Si binlog_expire_logs_seconds se establece en un valor superior, los registros binarios no se purgarán con suficiente antelación, lo que puede provocar un aumento en la facturación del almacenamiento. 


## <a name="non-configurable-server-parameters"></a>Parámetros no configurables del servidor

Estos parámetros del servidor no son configurables en el servicio:

|**Parámetro**|**Valor fijo**|
| :------------------------ | :-------- |
|innodb_file_per_table en el nivel básico|Apagado|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Otras variables que no se muestran aquí se establecen en los valores integrados de MySQL predeterminados. Consulte los documentos de MySQL para las versiones [8.0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5.7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) y [5.6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) para los valores predeterminados. 

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [configurar parámetros del servidor con Azure Portal](./howto-server-parameters.md).
- Obtenga información sobre cómo [configurar parámetros del servidor con la CLI de Azure](./howto-configure-server-parameters-using-cli.md).
- Obtenga información sobre cómo [configurar parámetros del servidor con PowerShell](./howto-configure-server-parameters-using-powershell.md).
