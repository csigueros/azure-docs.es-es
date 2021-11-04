---
title: Novedades
titleSuffix: Azure SQL Database
description: Obtenga información sobre las nuevas características y mejoras de la documentación de Azure SQL Database.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=2, references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 077d022c5d996f5c495c38e4789a37bf234d093b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006228"
---
# <a name="whats-new-in-azure-sql-database"></a>Novedades de Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

En este artículo se resumen los cambios en la documentación asociados a las nuevas características y mejoras de las versiones recientes de [Azure SQL Database](https://azure.microsoft.com/products/azure-sql/database/). Para más información acerca de Azure SQL Database, consulte la [información general](sql-database-paas-overview.md). 

Para información sobre Azure SQL Managed Instance, consulte [Novedades](../managed-instance/doc-changes-updates-release-notes-whats-new.md).



## <a name="preview"></a>Versión preliminar 

En la tabla siguiente se enumeran las características de Azure SQL Database que se encuentran actualmente en versión preliminar: 

| Característica | Detalles |
| ---| --- |
| [Captura de datos modificados](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | La captura de datos modificados (CDC) permite realizar un seguimiento de todos los cambios que se producen en una base de datos. Aunque esta característica ha estado disponible para SQL Server durante bastante tiempo, su uso con Azure SQL Database está actualmente en versión preliminar. |
| [Trabajos elásticos](elastic-jobs-overview.md) | La característica de trabajos elásticos es el reemplazo del Agente SQL Server para Azure SQL Database como una oferta de PaaS.  |
| [Consultas elásticas](elastic-query-overview.md) | La característica de consultas elásticas permite consultas entre bases de datos en Azure SQL Database. |
| [Transacciones elásticas](elastic-transactions-overview.md) | Las transacciones elásticas permiten ejecutar transacciones distribuidas entre bases de datos en la nube en Azure SQL Database. |
| [Libro de contabilidad](ledger-overview.md) | La función de libro de contabilidad de Azure SQL Database permite atestiguar criptográficamente a terceros, como auditores u otras partes interesadas de la empresa, que los datos no se han modificado. | 
| [Ventana de mantenimiento](maintenance-window.md)| La característica de ventana de mantenimiento permite configurar la programación de mantenimiento de Azure SQL Database. |
| [Editor de consultas de Azure Portal](connect-query-portal.md) | El editor de consultas del portal permite ejecutar consultas en Azure SQL Database directamente desde [Azure Portal](https://portal.azure.com).|
| [Sugerencias del Almacén de consultas](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true) | Use sugerencias de consulta para optimizar la ejecución de consultas por medio de la cláusula OPTION. |
| [SQL Analytics](../../azure-monitor/insights/azure-sql.md)|Azure SQL Analytics es una solución de supervisión en la nube que se utiliza para supervisar el rendimiento de todas las bases de datos de Azure SQL Database a escala, entre varias suscripciones y en una vista única. Azure SQL Analytics recopila y visualiza métricas clave del rendimiento con inteligencia integrada para solucionar problemas de rendimiento.|
| [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) |  SQL Insights es una solución completa para realizar la supervisión de cualquier producto de la familia de Azure SQL. SQL Insights usa las vistas de administración dinámica para exponer los datos que necesita para supervisar el estado, diagnosticar problemas y optimizar el rendimiento.|
| [Configuración con redundancia de zona para nivel de uso general](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | La característica de configuración con redundancia de zona emplea [Azure Availability Zones](../../availability-zones/az-overview.md#availability-zones) para replicar las bases de datos entre varias ubicaciones físicas dentro de una región de Azure. Al seleccionar la [redundancia de zona](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview), puede hacer que las bases de datos de uso general y los grupos elásticos sean capaces de resistir a un número mucho mayor de errores, como interrupciones catastróficas de los centros de datos, sin necesidad de cambiar la lógica de la aplicación. Actualmente, la característica solo está disponible en el nivel de uso general. | 
|||

## <a name="general-availability-ga"></a>Disponibilidad general (GA)

En la tabla siguiente se enumeran las características de Azure SQL Database que han pasado de la versión preliminar a la disponibilidad general (GA) en los últimos doce meses: 

| Característica | Mes de GA | Detalles |
| ---| --- |--- |
| [Autenticación solo de Azure Active Directory](authentication-azure-ad-only-authentication.md) | Noviembre de 2021 | Es posible configurar su instancia de Azure SQL Database permitir la autenticación solo desde Azure Active Directory. | 
| [Entidad de servicio de AAD](authentication-aad-service-principal.md) |  Septiembre de 2021 | Azure Active Directory (Azure AD) admite la creación de usuarios en Azure SQL Database en nombre de las aplicaciones de Azure AD (entidades de servicio).| 
| [Auditoría de operaciones de administración](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  Marzo de 2021 | Las capacidades de auditoría de Azure SQL permiten auditar las operaciones realizadas por los ingenieros de soporte técnico de Microsoft cuando necesitan acceder a los recursos de SQL durante una solicitud de soporte técnico, lo que permite una mayor transparencia de los recursos. | 
|||| 


## <a name="documentation-changes"></a>Cambios en la documentación

Obtenga información sobre los cambios importantes en la documentación de Azure SQL Database.

### <a name="november-2021"></a>Noviembre de 2021

| Cambios | Detalles |
| --- | --- |
| **Autenticación solo de Azure AD** | La restricción de la autenticación en la instancia de Azure SQL Database solo a usuarios de Azure Active Directory ya está disponible con carácter general. Para obtener más información, vea [Autenticación solo de Azure AD](../database/authentication-azure-ad-only-authentication.md). | 
| | | 


### <a name="october-2021"></a>Octubre de 2021

| Cambios | Detalles |
| --- | --- |
|**División de novedades** | El artículo **Novedades** antes combinado se ha dividido por producto: [Novedades de SQL Database](doc-changes-updates-release-notes-whats-new.md) y [Novedades de SQL Managed Instance](../managed-instance/doc-changes-updates-release-notes-whats-new.md), lo que facilita la identificación de las características que están actualmente en versión preliminar, disponibles con carácter general y de los cambios importantes en la documentación. Además, el contenido de [problemas conocidos de SQL Managed Instance](../managed-instance/doc-changes-updates-known-issues.md) se ha movido a su propia página.  | 

### <a name="september-2021"></a>Septiembre de 2021

| Cambios | Detalles |
| --- | --- |
| **Compatibilidad de la ventana de mantenimiento con zonas de disponibilidad** | Ahora puede usar la [característica Ventana de mantenimiento](maintenance-window.md) si Azure SQL Database se implementa en una zona de disponibilidad. Esta funcionalidad actualmente está en su versión preliminar.  | 
|||


### <a name="july-2021"></a>Julio de 2021

| Cambios | Detalles |
| --- | --- |
| **Autenticación solo de Azure AD** | Ahora es posible restringir la autenticación en Azure SQL Database solo a usuarios de Azure Active Directory. Esta funcionalidad actualmente está en su versión preliminar. Para más información, consulte [Autenticación solo de Azure AD](authentication-azure-ad-only-authentication.md). | 
|||

### <a name="june-2021"></a>Junio de 2021

| Cambios | Detalles |
| --- | --- |
| **Sugerencias del Almacén de consultas** | Ahora, es posible usar sugerencias de consulta para optimizar la ejecución de consultas por medio de la cláusula OPTION. Esta funcionalidad actualmente está en su versión preliminar. Para más información, consulte [Sugerencias del almacén de consultas.](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true) | 
|||

### <a name="may-2021"></a>Mayo de 2021

| Cambios | Detalles |
| --- | --- |
| **Captura de datos modificados** | El uso de captura de datos modificados (CDC) con Azure SQL Database se encuentra ahora en versión preliminar. Para más información, consulte [Cambio de la captura de datos.](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 
| **Libro de contabilidad de SQL Database** | El libro de contabilidad de SQL Database se encuentra en versión preliminar y presenta la capacidad de atestiguar criptográficamente a terceros, como auditores u otras partes interesadas de la empresa, que los datos no se han modificado. Para más información, consulte [Libro de contabilidad](ledger-overview.md). | 
|||

### <a name="march-2021"></a>Marzo de 2021

| Cambios | Detalles |
| --- | --- |
 | **Ventana de mantenimiento** | La característica de ventana de mantenimiento permite configurar una programación de mantenimiento de Azure SQL Database, actualmente en versión preliminar. Para más información, consulte [Ventana de mantenimiento](maintenance-window.md).|
| **SQL Insights** | SQL Insights es una solución completa para realizar la supervisión de cualquier producto de la familia de Azure SQL. SQL Insights usa las vistas de administración dinámica para exponer los datos que necesita para supervisar el estado, diagnosticar problemas y optimizar el rendimiento. Para más información, consulte [Información de SQL](../../azure-monitor/insights/sql-insights-overview.md). | 
||| 

## <a name="contribute-to-content"></a>Contribución al contenido

Para colaborar en la documentación de Azure SQL, consulte la [Guía para colaboradores de Microsoft Docs](/contribute/).
