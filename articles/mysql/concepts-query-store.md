---
title: 'Almacén de consultas: Azure Database for MySQL'
description: Más información sobre la característica Almacén de consultas de Azure Database for MySQL que le ayuda a realizar un seguimiento del rendimiento a lo largo del tiempo.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 95b9a28dd0d62df12b8a3ea4f9334f2edbdabdc7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781075"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Supervisión del rendimiento de Azure Database for MySQL con el Almacén de consultas

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

**Se aplica a:** Azure Database for MySQL 5.7, 8.0

La característica Almacén de consultas de Azure Database for MySQL proporciona una manera de realizar un seguimiento del rendimiento de las consultas a lo largo del tiempo. El Almacén de consultas simplifica la solución de problemas de rendimiento al ayudar a encontrar rápidamente las consultas que tardan más en ejecutarse y consumen más recursos. El Almacén de consultas captura automáticamente un historial de consultas y estadísticas de tiempo de ejecución y lo conserva para su revisión. Separa los datos por ventanas de tiempo para que pueda ver patrones de uso de la base de datos. Los datos de todos los usuarios, las bases de datos y las consultas se almacenan en la base de datos de esquema denominada **mysql** en la instancia de Azure Database for MySQL.

## <a name="common-scenarios-for-using-query-store"></a>Escenarios habituales para usar el Almacén de consultas

El Almacén de consultas se puede usar en diversos escenarios, incluidos los siguientes:

- Detectar consultas con regresión.
- Determinar el número de veces que se ha ejecutado una consulta en un período determinado
- Comparar el tiempo de ejecución medio de una consulta a través de ventanas de tiempo para ver diferenciales de gran tamaño

## <a name="enabling-query-store"></a>Habilitación del Almacén de consultas

El Almacén de consultas es una característica opcional, por lo que no está activo de forma predeterminada en un servidor. El Almacén de consultas se habilita o deshabilita globalmente para todas las bases de datos en un servidor determinado y no se puede activar o desactivar por base de datos.

### <a name="enable-query-store-using-the-azure-portal"></a>Habilitación del Almacén de consultas mediante Azure Portal

1. Inicie sesión en Azure Portal y seleccione el servidor de Azure Database for MySQL.
1. Seleccione **Parámetros del servidor** en la sección **Configuración** del menú.
1. Busque el parámetro query_store_capture_mode.
1. Establezca el valor en ALL y seleccione **Guardar**.

Para habilitar las estadísticas de espera en el Almacén de consultas:

1. Busque el parámetro query_store_wait_sampling_capture_mode.
1. Establezca el valor en ALL y seleccione **Guardar**.

Espere hasta 20 minutos para que el primer lote de datos se conserve en la base de datos mysql.

## <a name="information-in-query-store"></a>Información del Almacén de consultas

El Almacén de consultas tiene dos almacenes:

- Un almacén de estadísticas de tiempo de ejecución para conservar la información de estadísticas de ejecución de consultas.
- Un almacén de estadísticas de espera para conservar la información de estadísticas de espera.

Para minimizar el uso de espacio, las estadísticas de ejecución en tiempo de ejecución en el almacén de estadísticas en tiempo de ejecución se agregan durante un período fijo y configurable. La información de estos almacenes está visible consultando las vistas del almacén de consultas.

La consulta siguiente devuelve información sobre las consultas en el Almacén de consultas:

```sql
SELECT * FROM mysql.query_store;
```

O esta consulta para estadísticas de espera:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Búsqueda de consultas de espera

> [!NOTE]
> Las estadísticas de espera no deben habilitarse durante las horas de carga de trabajo máximas ni activarse indefinidamente para cargas de trabajo confidenciales. <br>En el caso de cargas de trabajo que hacen un uso intensivo de la CPU o en servidores configurados con menos núcleos virtuales, tenga cuidado al habilitar las estadísticas de espera. No deben activarse indefinidamente.

Los tipos de evento de espera combinan eventos de espera diferentes en ciclos por similitud. El Almacén de consultas proporciona el tipo de evento de espera, el nombre del evento de espera específico y la consulta en cuestión. Poder correlacionar esta información de espera con las estadísticas de tiempo de ejecución de consultas significa que puede mejorar la comprensión de lo que contribuye a las características de rendimiento de consulta.

Estos son algunos ejemplos de cómo puede obtener más información sobre la carga de trabajo con las estadísticas de espera del Almacén de consultas:

| **Observación** | **Acción** |
|---|---|
|Largas esperas de bloqueo | Compruebe los textos de consulta para las consultas afectadas e identifique las entidades de destino. Busque en el Almacén de consultas otras consultas que modifiquen la misma entidad, que se ejecuta con frecuencia o tiene una gran duración. Tras identificar estas consultas, considere la posibilidad de cambiar la lógica de aplicación para mejorar la simultaneidad o usar un nivel de aislamiento menos restrictivo. |
|Largas esperas de E/S de búfer | Encuentre las consultas con un gran número de lecturas físicas en el Almacén de consultas. Si coinciden con las consultas con largas esperas de E/S, considere la posibilidad de introducir un índice en la entidad subyacente para llevar a cabo búsquedas en lugar de exámenes. Esto podría minimizar la sobrecarga de E/S de las consultas. Compruebe las **Recomendaciones de rendimiento** para el servidor en el portal para ver si hay recomendaciones de índices para este servidor que optimizarían las consultas. |
|Largas esperas de memoria | Encuentre las consultas que más memoria consumen en el Almacén de consultas. Estas consultas probablemente retrasan el progreso de las consultas afectadas. Compruebe las **Recomendaciones de rendimiento** para el servidor en el portal para ver si hay recomendaciones de índices que optimizarían estas consultas. |

## <a name="configuration-options"></a>Opciones de configuración

Cuando el Almacén de consultas está habilitado, guarda los datos en ventanas de agregación de 15 minutos, hasta 500 consultas diferentes en cada ventana.

Las siguientes opciones están disponibles para configurar los parámetros del Almacén de consultas.

| **Parámetro** | **Descripción** | **Valor predeterminado** | **Range** |
|---|---|---|---|
| query_store_capture_mode | Active o desactive la característica de almacén de consultas según el valor. Nota: Si performance_schema está desactivado, al activar query_store_capture_mode se activarán performance_schema y un subconjunto de instrumentos de esquema de rendimiento necesarios para esta característica. | ALL | NONE, ALL |
| query_store_capture_interval | Intervalo de captura del Almacén de consultas en minutos. Permite especificar el intervalo en el que se agregan las métricas de consulta. | 15 | 5 - 60 |
| query_store_capture_utility_queries | Se activa o se desactiva para capturar todas las consultas de utilidades que se están ejecutando en el sistema. | No | YES, NO |
| query_store_retention_period_in_days | Período de tiempo en días que se conservan los datos en el Almacén de consultas. | 7 | 1 - 30 |

Las siguientes opciones afectan específicamente a las estadísticas de espera.

| **Parámetro** | **Descripción** | **Valor predeterminado** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Permite activar o desactivar las estadísticas de espera. | Ninguno | NONE, ALL |
| query_store_wait_sampling_frequency | Altera la frecuencia de muestreo de espera en segundos. De 5 a 300 segundos. | 30 | 5 - 300 |

> [!NOTE]
> Actualmente, **query_store_capture_mode** reemplaza a esta configuración, lo que significa que **query_store_capture_mode** y **query_store_wait_sampling_capture_mode** tienen que estar habilitados para que funcionen todas las estadísticas de espera. Si **query_store_capture_mode** está desactivado, las estadísticas de espera también estarán desactivadas, dado que usan el performance_schema habilitado y el query_text capturado por el Almacén de consultas.

Use [Azure Portal](howto-server-parameters.md) o la [CLI de Azure](howto-configure-server-parameters-using-cli.md) para obtener o establecer otro valor para un parámetro.

## <a name="views-and-functions"></a>Funciones y vistas

Vea y administre el Almacén de consultas mediante las siguientes vistas y funciones. Cualquier usuario en el [rol público con privilegios seleccionado](howto-create-users.md#to-create-more-admin-users-in-azure-database-for-mysql) puede usar estas vistas para ver los datos en el Almacén de consultas. Estas vistas solo están disponibles en la base de datos **mysql**.

Las consultas se normalizan examinando su estructura después de quitar los literales y constantes. Si dos consultas son idénticas salvo por los valores literales, tienen el mismo hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Esta vista devuelve todos los datos del Almacén de consultas. Hay una fila por cada identificador de base de datos, de usuario y de consulta diferentes.

| **Nombre** | **Tipo de datos** | **IS_NULLABLE** | **Descripción** |
|---|---|---|---|
| `schema_name`| varchar(64) | No | El nombre del esquema. |
| `query_id`| bigint(20) | No| El identificador único generado para la consulta específica. Si la misma consulta se ejecuta en un esquema diferente, se generará un nuevo identificador. |
| `timestamp_id` | timestamp| No| La marca de tiempo en la que se ejecuta la consulta. Se basa en la configuración de query_store_interval.|
| `query_digest_text`| longtext| No| El texto de consulta normalizado después de quitar todos los literales.|
| `query_sample_text` | longtext| No| La primera aparición de la consulta real con literales.|
| `query_digest_truncated` | bit| SÍ| Indica si el texto de consulta se ha truncado. El valor será Sí si la consulta tiene más de 1 KB.|
| `execution_count` | bigint(20)| No| El número de veces que se ejecutó la consulta para este identificador o marca de tiempo durante el intervalo configurado.|
| `warning_count` | bigint(20)| No| El número de advertencias que generó la consulta durante el intervalo.|
| `error_count` | bigint(20)| No| El número de errores que generó la consulta durante el intervalo.|
| `sum_timer_wait` | double| SÍ| El tiempo de ejecución total de esta consulta durante el intervalo.|
| `avg_timer_wait` | double| SÍ| El tiempo de ejecución promedio de esta consulta durante el intervalo.|
| `min_timer_wait` | double| SÍ| El tiempo de ejecución mínimo de esta consulta.|
| `max_timer_wait` | double| SÍ| El tiempo de ejecución máximo.|
| `sum_lock_time` | bigint(20)| No| El tiempo total empleado en todos los bloqueos para la ejecución de esta consulta durante este período de tiempo.|
| `sum_rows_affected` | bigint(20)| No| Número de filas afectadas.|
| `sum_rows_sent` | bigint(20)| No| El número de filas enviadas al cliente.|
| `sum_rows_examined` | bigint(20)| No| Número de filas examinadas|
| `sum_select_full_join` | bigint(20)| No| El número de combinaciones completas.|
| `sum_select_scan` | bigint(20)| No| El número de exámenes seleccionados. |
| `sum_sort_rows` | bigint(20)| No| El número de filas ordenadas.|
| `sum_no_index_used` | bigint(20)| No| El número de veces que la consulta no usó índices.|
| `sum_no_good_index_used` | bigint(20)| No| El número de veces que el motor de ejecución de consultas no usó índices correctos.|
| `sum_created_tmp_tables` | bigint(20)| No| El número total de tablas temporales creadas.|
| `sum_created_tmp_disk_tables` | bigint(20)| No| El número total de tablas temporales creadas en el disco (genera E/S).|
| `first_seen` | timestamp| No| La primera aparición (UTC) de la consulta durante el período de agregación.|
| `last_seen` | timestamp| No| La última aparición (UTC) de la consulta durante este período de agregación.|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Esta vista devuelve datos de eventos de espera en el Almacén de consultas. Hay una fila por cada identificador de base de datos, identificador de usuario, identificador de consulta y evento únicos.

| **Nombre**| **Tipo de datos** | **IS_NULLABLE** | **Descripción** |
|---|---|---|---|
| `interval_start` | timestamp | No| El inicio del intervalo (incremento de 15 minutos).|
| `interval_end` | timestamp | No| El fin del intervalo (incremento de 15 minutos).|
| `query_id` | bigint(20) | No| El identificador único generado en la consulta normalizada (desde el Almacén de consultas).|
| `query_digest_id` | varchar(32) | No| El texto de consulta normalizado después de quitar todos los literales (desde el Almacén de consultas). |
| `query_digest_text` | longtext | No| La primera aparición de la consulta real con literales (desde el Almacén de consultas). |
| `event_type` | varchar(32) | No| La categoría del evento de espera. |
| `event_name` | varchar(128) | No| El nombre del evento de espera. |
| `count_star` | bigint(20) | No| El número de eventos de espera muestreados durante el intervalo de la consulta. |
| `sum_timer_wait_ms` | double | No| El tiempo de espera total (en milisegundos) de esta consulta durante el intervalo. |

### <a name="functions"></a>Functions

| **Nombre**| **Descripción** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Purga todos los datos del Almacén de consultas antes de la marca de tiempo determinada. |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Purga todos los datos de eventos de espera antes de la marca de tiempo determinada. |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Purga las recomendaciones cuya expiración se produce antes de la marca de tiempo determinada. |

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

- Si un servidor de MySQL tiene el parámetro `read_only` activado, el Almacén de consultas no puede capturar datos.
- Se puede interrumpir la funcionalidad de Almacén de consultas si encuentra consultas largas de Unicode (\>= 6000 bytes).
- El período de retención de las estadísticas de espera es de 24 horas.
- Las estadísticas de espera usan una muestra para capturar una fracción de los eventos. Se puede modificar la frecuencia con el parámetro `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Pasos siguientes

- Más detalles sobre la [Información de rendimiento de consultas](concepts-query-performance-insight.md)
