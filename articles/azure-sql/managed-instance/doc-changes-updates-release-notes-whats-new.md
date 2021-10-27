---
title: Novedades
titleSuffix: Azure SQL Managed Instance
description: Obtenga información sobre las nuevas características y mejoras de la documentación de Azure SQL Managed Instance.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 89adb496452a0aa0351c5ac1f544a415c7cf9efc
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178640"
---
# <a name="whats-new-in-azure-sql-managed-instance"></a>Novedades de Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqlmi.md)]

En este artículo se resumen los cambios en la documentación asociados a las nuevas características y mejoras de las versiones recientes de [Azure SQL Managed Instance](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance). Para obtener más información sobre Azure SQL Managed Instance, vea la [introducción](sql-managed-instance-paas-overview.md).


En el caso de Azure SQL Database, vea [Novedades](../database/doc-changes-updates-release-notes-whats-new.md).


## <a name="preview"></a>Versión preliminar

En la tabla siguiente se enumeran las características de Azure SQL Managed Instance que se encuentran actualmente en versión preliminar:


| Característica | Detalles |
| ---| --- |
| [Compatibilidad con 16 TB para SQL Managed Instance de uso general](resource-limits.md)| Compatibilidad con la asignación de hasta 16 TB de espacio en SQL Managed Instance de uso general |
| [Autenticación solo de Azure Active Directory para Azure SQL](../database/authentication-azure-ad-only-authentication.md) |  Ahora es posible restringir la autenticación en Azure SQL Managed Instance solo a usuarios de Azure Active Directory. |
| [Transacciones elásticas](../database/elastic-transactions-overview.md) | Las transacciones elásticas permiten ejecutar transacciones distribuidas en bases de datos en la nube de Azure SQL Database y Azure SQL Managed Instance. |
| [Grupos de instancias](instance-pools-overview.md) | Una manera cómoda y rentable de migrar instancias de SQL Server más pequeñas a la nube. |
| [Migración con el servicio de reproducción de registros](log-replay-service-migrate.md) | Migre bases de datos de SQL Server a SQL Managed Instance mediante el servicio de reproducción de registros. |
| [Ventana de mantenimiento](../database/maintenance-window.md)| La característica de ventana de mantenimiento permite configurar la programación de mantenimiento de Azure SQL Managed Instance. |
| [Retención de copia de seguridad a largo plazo](long-term-backup-retention-configure.md) | Compatibilidad con la retención de copias de seguridad a largo plazo hasta 10 años en Azure SQL Managed Instance. |
| [Intercambio de mensajes entre instancias de Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker) | Compatibilidad con el intercambio de mensajes entre instancias mediante Service Broker en Azure SQL Managed Instance. |
| [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) | SQL Insights es una solución completa para realizar la supervisión de cualquier producto de la familia de Azure SQL. SQL Insights usa las vistas de administración dinámica para exponer los datos que necesita para supervisar el estado, diagnosticar problemas y optimizar el rendimiento. |
| [Replicación transaccional](replication-transactional-overview.md) | Replique los cambios de las tablas en otras bases de datos de SQL Managed Instance, SQL Database o SQL Server. También puede actualizar las tablas al cambiarse algunas filas en otras instancias de SQL Managed Instance o SQL Server. Para más información, vea [Configuración de la replicación en una Instancia administrada de Azure SQL](replication-between-two-instances-configure-tutorial.md). |
| [Detección de amenazas](threat-detection-configure.md) | La detección de amenazas avisa de las amenazas de seguridad detectadas en la base de datos. |
| [Sugerencias del Almacén de consultas](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-mi-current&preserve-view=true) | Use sugerencias de consulta para optimizar la ejecución de consultas por medio de la cláusula OPTION. |
|||

## <a name="general-availability-ga"></a>Disponibilidad general (GA)

En la tabla siguiente se enumeran las características de Azure SQL Managed Instance que han pasado de la versión preliminar a la disponibilidad general (GA) en los últimos doce meses: 

| Característica | Mes de GA | Detalles |
| ---| --- |--- |
| [Machine Learning Service](machine-learning-services-overview.md) | Marzo de 2021 | Machine Learning Services es una característica de Instancia administrada de Azure SQL que proporciona aprendizaje automático en la base de datos, con compatibilidad con scripts de Python y R. Incluye paquetes de R y de Python de Microsoft para el análisis predictivos de alto rendimiento y el aprendizaje automático. |
| [Permisos granulares para enmascaramiento de datos dinámico](../database/dynamic-data-masking-overview.md)| Marzo de 2021 | El enmascaramiento de datos dinámicos ayuda a impedir el acceso no autorizado a datos confidenciales permitiendo a los usuarios designar la cantidad de los datos confidenciales que se revelarán con un impacto mínimo en el nivel de aplicación. Se trata de una característica de protección de datos que oculta la información confidencial del conjunto de resultados de una consulta de campos designados de una base de datos, sin modificar los datos de esta última. Ahora es posible asignar permisos granulares para los datos que se han enmascarado dinámicamente. Para obtener más información, vea [Enmascaramiento de datos dinámicos](../database/dynamic-data-masking-overview.md#permissions). |
| [Auditoría de operaciones de administración](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  Marzo de 2021 | Las capacidades de auditoría de Azure SQL permiten auditar las operaciones realizadas por los ingenieros de soporte técnico de Microsoft cuando necesitan acceder a los recursos de SQL durante una solicitud de soporte técnico, lo que permite una mayor transparencia de los recursos. | 
||| 

## <a name="documentation-changes"></a>Cambios en la documentación

Obtenga información sobre los cambios importantes en la documentación de Azure SQL Managed Instance.

### <a name="october-2021"></a>Octubre de 2021

| Cambios | Detalles |
| --- | --- |
|**División de novedades** | El artículo **Novedades** antes combinado se ha dividido por producto: [Novedades de SQL Database](../database/doc-changes-updates-release-notes-whats-new.md) y [Novedades de SQL Managed Instance](doc-changes-updates-release-notes-whats-new.md), lo que facilita la identificación de las características que están actualmente en versión preliminar, disponibles con carácter general y de los cambios importantes en la documentación. Además, el contenido de [problemas conocidos de SQL Managed Instance](doc-changes-updates-known-issues.md) se ha movido a su propia página.  | 


### <a name="june-2021"></a>Junio de 2021

| Cambios | Detalles |
| --- | --- |
|**Compatibilidad de 16 TB con De uso general** | Se ha agregado compatibilidad con la asignación de hasta 16 TB de espacio para SQL Managed Instance De uso general. Vea los [límites de recursos](resource-limits.md) para obtener más información. Esta oferta de instancia está actualmente en versión preliminar. | 
| **Copias de seguridad paralelas** | Ahora es posible realizar copias de seguridad en paralelo de SQL Managed Instance en el nivel De uso general, lo que permite copias de seguridad más rápidas. Vea la entrada de blog [Copias de seguridad paralelas para mejorar el rendimiento](https://techcommunity.microsoft.com/t5/azure-sql/parallel-backup-for-better-performance-in-sql-managed-instance/ba-p/2421762) para obtener más información. |
| **Autenticación solo de Azure AD** | Ahora es posible restringir la autenticación en Azure SQL Managed Instance solo a usuarios de Azure Active Directory. Esta funcionalidad actualmente está en su versión preliminar. Para obtener más información, vea [Autenticación solo de Azure AD](../database/authentication-azure-ad-only-authentication.md). | 
| **Supervisión de Resource Health** | Use Resource Health para supervisar el estado de Azure SQL Managed Instance. Vea [Resource Health](../database/resource-health-to-troubleshoot-connectivity.md) para obtener más información. |
| **Permisos granulares para enmascaramiento de datos GA** | Los permisos granulares para el enmascaramiento de datos dinámico para Azure SQL Managed Instance ahora están disponibles con carácter general (GA). Para obtener más información, vea [Enmascaramiento de datos dinámicos](../database/dynamic-data-masking-overview.md#permissions). | 
|  | |


### <a name="april-2021"></a>Abril de 2021

| Cambios | Detalles |
| --- | --- |
| **Tablas de rutas definidas por el usuario (UDR)** | La configuración de subredes asistida por el servicio para Azure SQL Managed Instance ahora usa etiquetas de servicio en tablas de rutas definidas por el usuario (UDR). Vea la [arquitectura de conectividad](connectivity-architecture-overview.md) para obtener más información. |
|  |  |


### <a name="march-2021"></a>Marzo de 2021

| Cambios | Detalles |
| --- | --- |
| **Auditoría de operaciones de administración** | La capacidad de auditar operaciones de SQL Managed Instance ahora está disponible con carácter general (GA). | 
| **Servicio de reproducción de registros** | Ahora es posible migrar bases de datos de SQL Server a Azure SQL Managed Instance mediante Log Replay Service. Para obtener más información, vea [Migración con Log Replay Service](log-replay-service-migrate.md). Esta funcionalidad actualmente está en su versión preliminar. | 
| **Retención de copia de seguridad a largo plazo** | Compatibilidad con la retención de copias de seguridad a largo plazo hasta 10 años en Azure SQL Managed Instance. Para obtener más información, vea [Retención de copia de seguridad a largo plazo](long-term-backup-retention-configure.md).|
| **Machine Learning Services GA** | Machine Learning Services de Azure SQL Managed Instance ahora está disponible con carácter general (GA). Para obtener más información, vea [Machine Learning Services en SQL Managed Instance](machine-learning-services-overview.md).| 
| **Ventana de mantenimiento** | La característica de ventana de mantenimiento permite configurar una programación de mantenimiento de Azure SQL Managed Instance, actualmente en versión preliminar. Para obtener más información, vea [ventana de mantenimiento](/database/maintenance-window.md).|
| **Intercambio de mensajes de Service Broker** | El componente Service Broker de Azure SQL Managed Instance permite componer las aplicaciones a partir de servicios independientes autocontenidos al proporcionar compatibilidad nativa para el intercambio de mensajes confiable y seguro entre las bases de datos asociadas al servicio. Actualmente en versión preliminar. Para obtener más información, vea [Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker).
| **SQL Insights** | SQL Insights es una solución completa para realizar la supervisión de cualquier producto de la familia de Azure SQL. SQL Insights usa las vistas de administración dinámica para exponer los datos que necesita para supervisar el estado, diagnosticar problemas y optimizar el rendimiento. Para obtener más información, vea [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md). | 
||| 

### <a name="2020"></a>2020

Los siguientes cambios se agregaron a SQL Managed Instance y la documentación en 2020: 

| Cambios | Detalles |
| --- | --- |
| **Auditoría de operaciones de soporte técnico** | La auditoría de la capacidad de operaciones de soporte técnico de Microsoft permite auditar operaciones de soporte técnico de Microsoft cuando es necesario acceder a los servidores o las bases de datos durante una solicitud de soporte técnico al destino de los registros de auditoría (versión preliminar). Para obtener más información, vea [Auditoría de operaciones de soporte técnico](../database/auditing-overview.md#auditing-of-microsoft-support-operations).|
| **Transacciones elásticas** | Las transacciones elásticas permiten transacciones de bases de datos distribuidas que abarcan varias bases de datos en Azure SQL Database y Azure SQL Managed Instance. Las transacciones elásticas se han agregado para permitir la migración sin problemas de aplicaciones existentes, así como el desarrollo de aplicaciones multiinquilino modernas basadas en una arquitectura de base de datos con particiones verticales u horizontales (versión preliminar). Para obtener más información, vea [Transacciones distribuidas](../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance). | 
| **Redundancia de almacenamiento de copia de seguridad configurable** | Ahora es posible configurar opciones de almacenamiento con redundancia local (LRS) y almacenamiento con redundancia de zona (ZRS) para la redundancia de almacenamiento de copia de seguridad, lo que proporciona más flexibilidad y opciones. Para obtener más información, vea [Configuración de la redundancia del almacenamiento de copia de seguridad](../database/automated-backups-overview.md?tabs=managed-instance#configure-backup-storage-redundancy).|
| **Mejoras de rendimiento de copia de seguridad cifrada con TDE** | Ahora es posible establecer el período de retención de copia de seguridad de restauración a un momento dado (PITR), y la compresión automatizada de copias de seguridad cifradas con cifrado de datos transparente (TDE) ahora es un 30 % más eficaz a la hora de consumir espacio de almacenamiento de copia de seguridad, lo que ahorra costos para el usuario final. Vea [Cambio de la directiva de retención a corto plazo](../database/automated-backups-overview.md?tabs=managed-instance#change-the-short-term-retention-policy) para obtener más información. |
| **Mejoras en la autenticación de Azure AD** | Automatice la creación de usuarios mediante aplicaciones de Azure AD y cree usuarios invitados individuales de Azure AD (versión preliminar). Para obtener más información, vea [Lectores de directorios en Azure AD](../database/authentication-aad-directory-readers-role.md).|
| **Compatibilidad con el emparejamiento de red virtual global** | Se ha agregado compatibilidad con el emparejamiento de red virtual global a SQL Managed Instance, lo que mejora la experiencia de replicación geográfica. Vea [Replicación geográfica entre instancias administradas](../database/auto-failover-group-overview.md?tabs=azure-powershell#enabling-geo-replication-between-managed-instances-and-their-vnets). |
| **Hospedaje de bases de datos de catálogo de SSRS** | SQL Managed Instance ahora puede hospedar bases de datos de catálogo de todas las versiones compatibles de SQL Server Reporting Services (SSRS). | 
| **Mejoras importantes del rendimiento** | Se han realizado mejoras en el rendimiento de SQL Managed Instance, lo que incluye un rendimiento mejorado de escritura en el registro de transacciones, la mejora de la IOPS de datos y registros para instancias críticas para la empresa y la mejora del rendimiento de TempDB. Vea el blog de la comunidad tecnológica sobre el [rendimiento mejorado](https://techcommunity.microsoft.com/t5/azure-sql/announcing-major-performance-improvements-for-azure-sql-database/ba-p/1701256) para obtener más información. 
| **Experiencia de administración mejorada** | Con la nueva [API de operaciones](/rest/api/sql/2021-02-01-preview/managed-instance-operations), ahora es posible comprobar el progreso de las operaciones de instancia de ejecución prolongada. Para más información, consulte [Introducción a las operaciones de administración de Azure SQL Managed Instance](management-operations-overview.md?tabs=azure-portal).
| **Compatibilidad de aprendizaje automático** | Machine Learning Services con compatibilidad con los lenguajes R y Python ahora incluye compatibilidad de versión preliminar en Azure SQL Managed Instance (versión preliminar). Para obtener más información, vea [Machine Learning en SQL Managed Instance](machine-learning-services-overview.md). | 
| **Conmutación por error manual iniciada por el usuario en SQL Managed Instance** | La conmutación por error iniciada por el usuario ahora está disponible con carácter general, lo que proporciona la capacidad de iniciar manualmente una conmutación por error automática mediante PowerShell, comandos de la CLI y llamadas API, lo que mejora la resistencia de la aplicación. Para obtener más información, vea [Prueba de la resistencia](../database/high-availability-sla.md#testing-application-fault-resiliency). 
|  |  |



## <a name="known-issues"></a>Problemas conocidos

El contenido de problemas conocidos se ha movido a un artículo dedicado de [problemas conocidos de SQL Managed Instance](doc-changes-updates-known-issues.md). 


## <a name="contribute-to-content"></a>Contribución al contenido

Para colaborar en la documentación de Azure SQL, consulte la [Guía para colaboradores de Microsoft Docs](/contribute/).
