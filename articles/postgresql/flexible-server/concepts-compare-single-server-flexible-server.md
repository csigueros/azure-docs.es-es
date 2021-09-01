---
title: 'Comparación de Azure Database for PostgreSQL: servidor único y servidor flexible'
description: Comparación detallada de características y funcionalidades entre el servidor único y el servidor flexible de Azure Database for PostgreSQL
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 6d6e5ca910bfd24a8d10952d92441fcc7fe6fcfa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780707"
---
# <a name="comparison-chart---azure-database-for-postgresql-single-server-and-flexible-server"></a>Gráfico de comparación del servidor único y el servidor flexible de Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL: servidor flexible está en versión preliminar

En la tabla siguiente se proporcionan comparaciones de características y funcionalidades de alto nivel entre servidor único y servidor flexible.

| **Característica o funcionalidad** | **Servidor único** | **Servidor flexible** |
| ---- | ---- | ---- |
| **General**  | | |
| Disponibilidad general | GA desde 2018 | Vista previa pública |
| PostgreSQL | Comunidad | Comunidad |
| Versiones | 9.6, 10, 11 | 11, 12, 13 |
| Sistema operativo subyacente | Windows | Linux  |
| Selección de zona de disponibilidad para la coubicación de aplicaciones | No | Sí |
| Concentrador de conexión integrado | No | Sí (PgBouncer)|
| **Conectividad** | | |
| Nombre de usuario en la cadena de conexión | `<user_name>@server_name`. Por ejemplo, `pgadmusr@mypgServer`. | Solo nombre de usuario. Por ejemplo, `pgadmusr`. | 
| lc_collate  | English_United States.1252 | en_US.utf8 |
| lc_ctype    | English_United States.1252 |en_US.utf8 |
|lc_messages | English_United States.1252 |en_US.utf8|
| lc_monetary | English_United States.1252 |  en_US.utf-8 |
| lc_numeric  | English_United States.1252 |  en_US.utf-8 |
| lc_time     | English_United States.1252 | en_US.utf8 |
| Puerto de conexión | 5432 | 5432 (base de datos), 6432 (PgBouncer) |
| Máx. connections | 1982 | 5000 |
| ¿Se puede configurar el límite de conexiones? | No | Sí (parámetro `max_connections`) |
| **Proceso y almacenamiento** | | |
| Niveles de proceso | Básico, De uso general y Optimizado para memoria | Ampliable, De uso general y Optimizado para memoria |
| SKU ampliables | No | Sí |
| Capacidad de escalar entre niveles de proceso | No se puede escalar el nivel Básico | Sí. Se puede escalar entre niveles |
| Detener/Iniciar | No | Sí (para todas las SKU de proceso). Solo se detiene o se inicia el proceso |
| Máx. Tamaño de almacenamiento | 1 TB (Básico), 4 TB o 16 TB (GP, MO). Nota: No todas las regiones admiten 16 TB. | 16 TB |
| Tamaño de almacenamiento mínimo | 5 GB (básico), 100 GB (GP, MO) | 32 GB |
| Crecimiento automático del almacenamiento | Sí (incrementos de 1 GB) | No |
| IOPS máx. | Básico: variable. GP/MO: hasta 20 000  | Hasta 20 000 |
| **Redes y seguridad** | | |
| Redes admitidas | Red virtual, vínculo privado, acceso público | Acceso privado (inserción de red virtual en una subred delegada, acceso público) |
| Control de acceso público | Firewall | Firewall |
| Compatibilidad con vínculos privados | Sí |No|
| Compatibilidad de zona de DNS privado | No | Sí |
| Capacidad de moverse entre el acceso público y privado | No | No |
| Soporte técnico de TLS | TLS 1.2 | Se aplica TLS 1.2, 1.3|
| Puede desactivar SSL | Sí | No |
| Autenticación SCRAM | No | Sí |
| **Alta disponibilidad** | | |
| Alta disponibilidad con redundancia de zona | No | Sí (se establece un modo de espera sincrónico en otra zona dentro de una región) |
| Configuración de alta disponibilidad | Integrado con almacenamiento anclado a una zona. El proceso puede flotar entre regiones. | Computación y almacenamiento físicamente separados aprovisionados en dos zonas |
| Costo | 1x | 2x (Proceso y almacenamiento) |
| Disponibilidad con configuración que no es de alta disponibilidad | Reinicio automático, reubicación de proceso | Reinicio automático, reubicación de proceso)
| Protección frente a errores de zona | Proceso: sí Almacenamiento: no | Proceso y almacenamiento: sí |
| Protección frente a errores en la región | No | No |
| Modo de replicación de alta disponibilidad | N/D | Replicación de streaming físico de Postgres en modo SYNC)
| El modo en espera se puede usar con fines de lectura | N/D | No |
| Impacto en el rendimiento de la aplicación | No (sin replicación) | Sí (debido a la replicación de sincronización. Depende de la carga de trabajo) |
| Conmutación por error automática | Sí (gira otro servidor)| Sí |
| Cadena de conexión de la aplicación después de la conmutación por error | Sin cambios | Sin cambios |
| **Replicación lógica** | | |
| Se admite la descodificación lógica | Sí | Sí |
| Se ha agregado compatibilidad para la replicación lógica | No | Sí |
| Compatibilidad con la extensión Pglogical | No | Sí |
| Compatibilidad con la replicación lógica con alta disponibilidad | N/D | Limitado |
| **Recuperación ante desastres** | | |
| Recuperación ante desastres entre regiones | Uso de réplicas de lectura, copia de seguridad con redundancia geográfica | N/D |
| Recuperación ante desastres mediante réplica | Uso de la replicación física asincrónica | N/D |
| Conmutación por error automática | No | N/D |
| Puede usar el mismo punto de conexión de l/e | No | N/D |
| **Copia de seguridad y recuperación** | | |
| Copias de seguridad automatizadas | Sí | Sí |
| Retención de copias de seguridad | Entre 7 y 35 días | Entre 7 y 35 días |
| Funcionalidad de recuperación a un momento dado en cualquier momento dentro del período de retención | Sí | Sí
| Capacidad de restauración en una zona diferente | N/D | Sí |
| Capacidad de restauración en una red virtual diferente | No | Sí |
| Capacidad de restauración a una región diferente | Sí (Con redundancia geográfica) | No |
| Capacidad de restaurar un servidor eliminado | Limitado a través de la API | No |
| **Réplica de lectura** | | |
| Compatibilidad para réplicas de lectura | Sí | No |
| Número de réplicas de lectura | 5 | N/D |
| Modo de replicación | Async | N/D |
| Compatibilidad con varias regiones | Sí | N/D |
| **Ventana de mantenimiento** | | |
| Ventana programada del sistema | Sí | Sí |
| Ventana programada del cliente| No | Sí (puede elegir 1 hora cualquier día) |
| Período de aviso | 3 días | 5 días |
| Período de mantenimiento | En cualquier momento en un plazo de 15 horas | Período de 1 hora | 
| **Métricas** | | |
| Errors | Conexiones con errores | Conexiones con errores|
| Latencia | Retraso máximo entre réplicas, retraso de réplica | N/D |
| Saturación | Almacenamiento de copia de seguridad usado, % de CPU, % de E/S, % de memoria, límite de almacenamiento de registros del servidor, % de almacenamiento de registro de servidor, almacenamiento de registro de servidor usado, límite de almacenamiento, % almacenamiento, almacenamiento usado | Almacenamiento de copia de seguridad usado, créditos de CPU consumidos, créditos de CPU restantes, % de CPU, profundidad de cola de disco, IOPS, % de memoria, IOPS de lectura, bytes de rendimiento de lectura, almacenamiento libre, % de almacenamiento, almacenamiento usado, almacenamiento de registro de transacciones usado, IOPS de escritura, bytes de rendimiento de escritura |
| Tráfico | Conexiones activas, entrada de red, salida de red | Conexiones activas, máximo. id. de transacción usada, entrada de red, salida de red, conexiones correctas |
| **Extensiones** | | (ofrece las versiones más recientes)|
| TimescaleDB, orafce, plv8 | Sí | No |
| PgCron, lo, pglogical | No | Sí |
| pgAudit | Versión preliminar | Sí |
| **Otras características** | | |
| Clave administrada por el cliente (BYOK) | Sí | No |
| Alertas | Sí | Sí |
| Azure defender | Sí | No |
| Estado de los recursos | Sí | No |
| Estado del servicio | Sí | Sí |
| Información sobre el rendimiento (iPerf) | Sí | Sí (versión preliminar) |
| Compatibilidad con actualizaciones de versiones principales | No | No |
| Actualizaciones de versión secundarias | Sí. Automático durante la ventana de mantenimiento | Sí. Automático durante la ventana de mantenimiento |


## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [las opciones de proceso y almacenamiento disponibles en el servidor flexible](concepts-compute-storage.md)
- Conozca las [versiones de base de datos de PostgreSQL admitidas en el servidor flexible](concepts-supported-versions.md)
- Más información sobre las [limitaciones actuales en el servidor flexible](concepts-limits.md)