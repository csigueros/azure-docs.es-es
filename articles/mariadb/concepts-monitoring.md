---
title: 'Supervisión de servidores: Azure Database for MariaDB'
description: En este artículo se describen las métricas de supervisión y alertas de Azure Database for MariaDB, incluidas las estadísticas de CPU, almacenamiento y conexión.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 36b3c29872c73122a0dc7913499f8a5a10df94e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065178"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Supervisión en Azure Database for MariaDB
La supervisión de los datos sobre los servidores le permite solucionar problemas y optimizar la carga de trabajo. Azure Database for MariaDB proporciona diversas métricas que proporcionan información sobre el comportamiento del servidor.

## <a name="metrics"></a>Métricas
Todas las métricas de Azure tienen una frecuencia de un minuto y cada métrica proporciona 30 días de historial. Puede configurar alertas en las métricas. Otras tareas incluyen la configuración de acciones automatizadas, la realización de análisis avanzados y el archivo del historial. Para obtener más información, consulte [Información general sobre las métricas en Microsoft Azure](../azure-monitor/data-platform.md).

Para obtener instrucciones paso a paso, consulte [How to set up alerts](howto-alert-metric.md) (Configuración de alertas).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas están disponibles para Azure Database for MariaDB:

|Métrica|Nombre de métrica para mostrar|Unidad|Descripción|
|---|---|---|---|
|cpu_percent|Porcentaje de CPU|Percent|Porcentaje de CPU en uso.|
|memory_percent|Porcentaje de memoria|Percent|Porcentaje de memoria en uso.|
|io_consumption_percent|Porcentaje de E/S|Percent|Porcentaje de E/S en uso. (No se aplica a los servidores de nivel Básico)|
|storage_percent|Porcentaje de almacenamiento|Percent|Porcentaje de almacenamiento que se usa más allá del límite máximo del servidor.|
|storage_used|Almacenamiento utilizado|Bytes|Cantidad de almacenamiento en uso. El almacenamiento que usa el servicio puede incluir los archivos de base de datos, los registros de transacciones y los registros de servidor.|
|serverlog_storage_percent|Porcentaje de almacenamiento del registro del servidor|Percent|El porcentaje usado del almacenamiento máximo de registro del servidor.|
|serverlog_storage_usage|Almacenamiento del registro del servidor usado|Bytes|La cantidad de almacenamiento de registro del servidor en uso.|
|serverlog_storage_limit|Límite de almacenamiento del registro del servidor|Bytes|El almacenamiento máximo de registro de este servidor.|
|storage_limit|Límite de almacenamiento|Bytes|Almacenamiento máximo de este servidor.|
|active_connections|Conexiones activas|Count|Número de conexiones activas al servidor.|
|connections_failed|Conexiones con errores|Count|Número de conexiones con errores al servidor.|
|seconds_behind_master|Intervalo de replicación en segundos|Count|El número de segundos que el servidor de réplica se retrasa en relación con el servidor de origen. (No se aplica a los servidores de nivel Básico)|
|network_bytes_egress|Red interna|Bytes|Red externa a través de conexiones activas.|
|network_bytes_ingress|Red interna|Bytes|Red interna a través de conexiones activas.|
|backup_storage_used|Almacenamiento de copia de seguridad utilizado|Bytes|Cantidad de almacenamiento de copia de seguridad utilizado. Esta métrica representa la suma del almacenamiento consumido por todas las copias de seguridad de base de datos completas, copias de seguridad diferenciales y copias de seguridad de registros, conservadas según el período de retención de copia de seguridad establecido para el servidor. La frecuencia de las copias de seguridad la administra el servicio, y se explicó en el [artículo sobre los conceptos](concepts-backup.md). En el caso del almacenamiento con redundancia geográfica, el uso del almacenamiento de copia de seguridad es dos veces el del almacenamiento con redundancia local.|

## <a name="server-logs"></a>Registros del servidor

Puede habilitar el registro de consultas lentas en el servidor. Estos registros también están disponibles mediante los registros de diagnóstico de Azure en los registros de Azure Monitor, Event Hubs y la cuenta de almacenamiento. Para más información sobre el registro, visite la página [Registros de servidor](concepts-server-logs.md).

## <a name="query-store"></a>Almacén de consultas

El [Almacén de consultas](concepts-query-store.md) realiza un seguimiento del rendimiento de las consultas a lo largo del tiempo, que incluye las estadísticas en tiempo de ejecución y los eventos de espera de consultas. La característica conserva la información de rendimiento del entorno de ejecución de consulta en el esquema **mysql**. Puede controlar la recopilación y el almacenamiento de datos mediante diversos botones de configuración.

## <a name="query-performance-insight"></a>Información de rendimiento de consultas

[Información de rendimiento de consultas](concepts-query-performance-insight.md) funciona en combinación con el Almacén de consultas para proporcionar visualizaciones accesibles desde Azure Portal. Estos gráficos le permiten identificar las consultas clave que afectan al rendimiento. Información de rendimiento de consultas es accesible en la sección **Rendimiento inteligente** de la página del portal del servidor de Azure Database for MariaDB.

## <a name="planned-maintenance-notification"></a>Notificación de mantenimiento planeado

Las [notificaciones de mantenimiento planeado](./concepts-planned-maintenance-notification.md) le permiten recibir alertas de mantenimiento planeado futuro para su instancia de Azure Database for MariaDB. Estas notificaciones se integran con el mantenimiento planeado de [Service Health](../service-health/overview.md) y le permiten ver todo el mantenimiento programado para sus suscripciones en un mismo lugar. También ayuda a escalar la notificación a las audiencias adecuadas de distintos grupos de recursos, ya que puede tener distintos contactos responsables para los distintos recursos. Recibirá la notificación sobre el próximo mantenimiento 72 horas antes del evento.

Puede encontrar más información sobre cómo configurar notificaciones en el documento [Notificaciones de mantenimiento planeado](./concepts-planned-maintenance-notification.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre cómo acceder a las métricas y exportarlas con Azure Portal, la API de REST o la CLI, consulte [Información general sobre las métricas en Microsoft Azure](../azure-monitor/data-platform.md).
- Consulte [How to set up alerts](howto-alert-metric.md) (Configuración de alertas) para obtener instrucciones sobre cómo crear una alerta en una métrica.
- Más información sobre las [notificaciones de mantenimiento planeado](./concepts-planned-maintenance-notification.md) en Azure Database for MariaDB.