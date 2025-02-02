---
title: Notas de la versión del servidor flexible de Azure Database for PostgreSQL
description: Notas de la versión del servidor flexible de Azure Database for PostgreSQL
author: sr-msft
ms.author: srranga
ms.custom: references_regions
ms.service: postgresql
ms.topic: overview
ms.date: 11/18/2021
ms.openlocfilehash: 672fa91d0fc6785332ace1d45f8e1c080895347e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721867"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>Notas de la versión del servidor flexible de Azure Database for PostgreSQL

En esta página se proporcionan las últimas noticias y actualizaciones relacionadas con las adiciones de características, la compatibilidad con las versiones del motor, las extensiones y cualquier otro anuncio importante para la opción de servidor flexible de Azure Database for PostgreSQL.

> [!IMPORTANT]
> Azure Database for PostgreSQL: Servidor flexible en versión preliminar

## <a name="release-november-2021"></a>Publicación: noviembre de 2021

* Compatibilidad con las [versiones secundarias más recientes de PostgreSQL](./concepts-supported-versions.md) (13.4, 12.8 y 11.13) en la creación de servidores<sup>$</sup>.
* Compatibilidad con la característica de [copia de seguridad y restauración con redundancia geográfica](concepts-backup-restore.md) en versión preliminar en regiones emparejadas seleccionadas: Este de EE. UU. 2, Centro de EE. UU., Norte de Europa, Oeste de Europa, Este de Japón y Oeste de Japón.
*  Compatibilidad con el [conector de Azure Stream Analytics (ASA) en versión preliminar](https://techcommunity.microsoft.com/t5/analytics-on-azure/stream-analytics-updates-ignite-fall-2021-new-outputs-new/ba-p/2919170) para ingerir datos de streaming de alto rendimiento en la tabla existente.
*  Varias correcciones de errores y mejoras de estabilidad y rendimiento<sup>$</sup>.

<sup> **$**</sup> Los nuevos servidores obtienen estas características automáticamente. En los servidores existentes, estas características se habilitan durante la ventana de mantenimiento futuro del servidor.

## <a name="release-october-2021"></a>Publicación: octubre de 2021

*   Compatibilidad con las [SKU Ddsv4 y Edsv4](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/flexible-server-now-supports-v4-compute-series-in-postgresql-on/ba-p/2815092). 
*   Capacidad de elegir el disco local para el espacio de tablas temporal mediante el parámetro de servidor `azure.enable_temp_tablespaces_on_local_ssd`.
*   La página Parámetros del servidor de Azure Portal muestra la unidad de medida y el vínculo a la documentación de PostgreSQL para la mayoría de los parámetros.
*   Varias correcciones de errores y mejoras en la estabilidad y el rendimiento.

## <a name="release-september-2021"></a>Versión: septiembre de 2021

* Compatibilidad con [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server).
* Compatibilidad con las [nuevas regiones](overview.md#azure-regions) Centro de la India y Japón Occidental.
* Compatibilidad con el modo de conectividad no SSL mediante el nuevo parámetro de servidor `require_secure_transport`.
* Compatibilidad con el parámetro de servidor `log_line_prefix`, que agrega la cadena al principio de cada línea de registro.
* Compatibilidad con el diagnóstico de estado de [Azure Resource Health](../../service-health/resource-health-overview.md) para Servidor flexible y para obtener soporte técnico.
* Varias correcciones de errores y mejoras en la estabilidad y el rendimiento.

## <a name="release-july-2021"></a>Versión: julio de 2021

* Compatibilidad con las [nuevas regiones](overview.md#azure-regions) Este de Asia, Centro-oeste de Alemania, Sur de Corea del Sur, Centro-sur de EE. UU., Oeste de Reino Unido.
* Compatibilidad con la [extensión pglogical](concepts-logical.md) v2.3.2 con PostgreSQL 11, 12 y 13.<sup>$</sup>
* PgBouncer ahora incluye `ignore_startup_parameters` para omitir determinados parámetros del controlador del lado cliente, incluidos los parámetros `extra_float_digits` y `pgbouncer.query_wait_timeout`.  <sup>$</sup>.
* Compatibilidad con `pg_stat_reset_shared('bgwriter');` y `pg_stat_reset_shared('archiver');` para restablecer los contadores que se muestran en las vistas `pg_stat_bgwriter` y `pg_stat_archiver` <sup>$</sup>.
* Varias correcciones de errores y mejoras de estabilidad y rendimiento<sup>$</sup>.

<sup> **$**</sup> Los nuevos servidores obtienen estas características automáticamente. En los servidores existentes, estas características se habilitan durante la ventana de mantenimiento futuro del servidor.

## <a name="release-june-2021"></a>Versión: junio de 2021

* Compatibilidad con las [versiones secundarias más recientes de PostgreSQL](./concepts-supported-versions.md) (13.3, 12.7 y 11.12) en la creación de servidores<sup>$</sup>.
* Compatibilidad con [nuevas regiones](overview.md#azure-regions), incluidas Sudeste de Australia, Sur de Brasil, Centro de Corea del Sur, Este de Noruega, Norte de Sudáfrica, Norte de Suiza, Norte de Emiratos Árabes Unidos y Oeste de EE. UU.
* Compatibilidad con funcionalidades de [conmutación por error a petición](./concepts-high-availability.md#on-demand-failover), incluida la conmutación por error forzada y la conmutación por error planeada para implementaciones de alta disponibilidad con redundancia de zona.
* Compatibilidad con la [autenticación de SCRAM](how-to-connect-scram.md) para todas las versiones principales con la creación de servidores<sup>$</sup>.
* Compatibilidad con `pg_prewarm` para que se cargue previamente mediante `shared_preload_libraries` en la creación de servidores<sup>$</sup>.
* Compatibilidad con la extensión lo. Vea la [página de extensiones](./concepts-extensions.md) para obtener las versiones compatibles con cada versión principal <sup>$</sup>.
* Varias correcciones de errores y mejoras de estabilidad y rendimiento<sup>$</sup>.
  
<sup> **$**</sup> Los nuevos servidores obtienen estas características automáticamente.  Los servidores existentes se actualizarán automáticamente a la versión secundaria admitida más reciente y también se habilitarán nuevas características durante la ventana de mantenimiento futura del servidor.

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

## <a name="release-october-2020---march-2021"></a>Versión: de octubre de 2020 a marzo de 2021

*  Se ha mejorado la experiencia para [conectarse](connect-azure-cli.md) al servidor flexible mediante la CLI de Azure con el comando `az postgres flexible- server connect`.
*  Se admiten [nuevas regiones](overview.md#azure-regions).
*  Varias mejoras del portal, incluida la presentación de versiones secundarias y el resumen de las métricas en la hoja de información general.
*  Varias correcciones de errores y mejoras en la estabilidad y el rendimiento.

## <a name="contacts"></a>Contactos

Para cualquier pregunta o sugerencia que pueda tener sobre el servidor flexible de Azure Database for PostgreSQL, envíe un correo electrónico al equipo de Azure Database for PostgreSQL ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Tenga en cuenta que esta dirección de correo electrónico no es un alias de soporte técnico.

Además, tenga en cuenta los siguientes puntos de contacto según corresponda:

- Para ponerse en contacto con el servicio de soporte técnico de Azure, [presente una incidencia de soporte técnico en Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corregir un problema con la cuenta, envíe una [solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en Azure Portal.
- Para proporcionar comentarios o solicitar nuevas características, cree una entrada mediante [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
  

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha leído la introducción al modo de implementación Servidor flexible de Azure Database for PostgreSQL, ya está listo para crear su primer servidor: [Creación de una instancia de Azure Database for PostgreSQL con la opción Servidor flexible mediante Azure Portal](./quickstart-create-server-portal.md)
