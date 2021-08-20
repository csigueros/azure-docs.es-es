---
title: Notas de la versión del servidor flexible de Azure Database for PostgreSQL
description: Notas de la versión del servidor flexible de Azure Database for PostgreSQL
author: sr-msft
ms.author: srranga
ms.custom: references_regions
ms.service: postgresql
ms.topic: overview
ms.date: 06/23/2021
ms.openlocfilehash: 87af6f9764c2ab01b0e0d02d8eb4a6c7342ca31c
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894633"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>Notas de la versión del servidor flexible de Azure Database for PostgreSQL

En esta página se proporcionan las últimas noticias y actualizaciones relacionadas con las adiciones de características, la compatibilidad con las versiones del motor, las extensiones y cualquier otro anuncio importante para la opción de servidor flexible de Azure Database for PostgreSQL.

> [!IMPORTANT]
> Azure Database for PostgreSQL: Servidor flexible en versión preliminar

## <a name="release-june-2021"></a>Versión: junio de 2021

* Compatibilidad con las [versiones secundarias más recientes de PostgreSQL](./concepts-supported-versions.md) (13.3, 12.7 y 11.12) en la creación de servidores<sup>$</sup>.
* Compatibilidad con [nuevas regiones](overview.md#azure-regions), incluidas Sudeste de Australia, Sur de Brasil, Centro de Corea del Sur, Este de Noruega, Norte de Sudáfrica, Norte de Suiza, Norte de Emiratos Árabes Unidos y Oeste de EE. UU.
* Compatibilidad con funcionalidades de [conmutación por error a petición](./concepts-high-availability.md#on-demand-failover), incluida la conmutación por error forzada y la conmutación por error planeada para implementaciones de alta disponibilidad con redundancia de zona.
* Compatibilidad con la [autenticación de SCRAM](how-to-connect-scram.md) para todas las versiones principales con la creación de servidores<sup>$</sup>.
* Compatibilidad con `pg_prewarm` para que se cargue previamente mediante `shared_preload_libraries` en la creación de servidores<sup>$</sup>.
* Compatibilidad con la extensión lo. Vea la [página de extensiones](./concepts-extensions.md) para obtener las versiones compatibles con cada versión principal <sup>$</sup>.
* Varias correcciones de errores y mejoras de estabilidad y rendimiento<sup>$</sup>.
  
<sup> **$** </sup> Los servidores existentes se actualizarán automáticamente a la versión secundaria admitida más reciente y también se habilitarán nuevas características durante la ventana de mantenimiento futura del servidor.

## <a name="release-may-2021"></a>Versión: mayo de 2021

* Compatibilidad con la [versión principal 13 de PostgreSQL](./concepts-supported-versions.md).
* Compatibilidad con extensiones, como pg_partman, pg_cron y pgaudit. Consulte la [página de extensiones](./concepts-extensions.md) para ver las versiones compatibles con cada versión principal.
* Varias correcciones de errores y mejoras en la estabilidad y el rendimiento.

## <a name="release-april-2021"></a>Versión: abril de 2021

* Compatibilidad con las [versiones secundarias más recientes de PostgreSQL](./concepts-supported-versions.md) (12.6 y 11.11) en la creación de nuevos servidores.
* Compatibilidad con la [zona DNS privada](./concepts-networking.md#private-access-vnet-integration) de red virtual (VNET).
* Se admite elegir la zona de disponibilidad durante la operación de recuperación a un momento dado.
* Se admiten nuevas [regiones](./overview.md#azure-regions), entre ellas, Este de Australia, Centro de Canadá y Centro de Francia.
* Compatibilidad con el agrupador de conexiones [PgBouncer integrado](./concepts-pgbouncer.md). 
<!--- * Support for [pglogical](https://github.com/2ndQuadrant/pglogical) extension version 2.3.2. -->
* [Rendimiento inteligente](concepts-query-store.md) en versión preliminar pública.
* Varias correcciones de errores y mejoras en la estabilidad y el rendimiento.

## <a name="contacts"></a>Contactos

Para cualquier pregunta o sugerencia que pueda tener sobre el servidor flexible de Azure Database for PostgreSQL, envíe un correo electrónico al equipo de Azure Database for PostgreSQL ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Tenga en cuenta que esta dirección de correo electrónico no es un alias de soporte técnico.

Además, tenga en cuenta los siguientes puntos de contacto según corresponda:

- Para ponerse en contacto con el servicio de soporte técnico de Azure, [presente una incidencia de soporte técnico en Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corregir un problema con la cuenta, envíe una [solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en Azure Portal.
- Para proporcionar comentarios o solicitar nuevas características, cree una entrada mediante [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
  

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha leído la introducción al modo de implementación Servidor flexible de Azure Database for PostgreSQL, ya está listo para crear su primer servidor: [Creación de una instancia de Azure Database for PostgreSQL con la opción Servidor flexible mediante Azure Portal](./quickstart-create-server-portal.md)