---
title: 'Versiones admitidas en Azure Database for PostgreSQL: servidor único'
description: 'Aquí se describen las versiones principales y secundarias de Postgres admitidas en Azure Database for PostgreSQL: servidor único.'
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/01/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: c20eb75fbb248ff67fb244fde1355aae9c726d7a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745155"
---
# <a name="supported-postgresql-major-versions"></a>Versiones principales de PostgreSQL admitidas

Consulte [Directiva de control de versiones de Azure Database for PostgreSQL](concepts-version-policy.md) para obtener detalles de la directiva de soporte técnico.

Azure Database for PostgreSQL actualmente admite las siguientes versiones principales:

## <a name="postgresql-version-11"></a>PostgreSQL, versión 11
La versión secundaria actual es la 11.11. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-11.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

## <a name="postgresql-version-10"></a>PostgreSQL, versión 10
La versión secundaria actual es la 10.16. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-16.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

## <a name="postgresql-version-96"></a>PostgreSQL, versión 9.6
La versión secundaria actual es la 9.6.21. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-21.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

## <a name="postgresql-version-95-retired"></a>PostgreSQL versión 9.5 (retirada)
En consonancia con la [directiva de control de versiones](https://www.postgresql.org/support/versioning/) de la comunidad de Postgres, a partir del 11 de febrero de 2021 Azure Database for PostgreSQL ha retirado la versión 9.5 de Postgres. Para más información y conocer las restricciones, consulte [Directiva de control de versiones de Azure Database for PostgreSQL](concepts-version-policy.md). Si está ejecutando esta versión principal, actualice a una versión posterior lo antes posible, preferiblemente a PostgreSQL 11.

## <a name="managing-upgrades"></a>Administración de actualizaciones
El proyecto de PostgreSQL publica periódicamente versiones secundarias para corregir errores detectados. Azure Database for PostgreSQL aplica automáticamente revisiones a los servidores con versiones secundarias durante las implementaciones mensuales del servicio. 

No se admiten las actualizaciones locales automáticas de las versiones principales. Para actualizar a la siguiente versión principal, puede 
   * Usar alguno de los métodos que se documentan en [Actualizaciones de versiones principales con volcado y restauración](./how-to-upgrade-using-dump-and-restore.md).
   * Usar [pg_dump y pg_restore](./howto-migrate-using-dump-and-restore.md) para mover una base de datos a un servidor creado con la nueva versión del motor.
   * Usar [Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) para realizar actualizaciones en línea.

### <a name="version-syntax"></a>Sintaxis de versión
Antes de la versión 10 de PostgreSQL, la [directiva de versiones de PostgreSQL](https://www.postgresql.org/support/versioning/) consideraba que una actualización de la _versión principal_ suponía un aumento en el primer _o_ segundo número. Por ejemplo, de 9.5 a 9.6 se consideraba una actualización de la versión _principal_. A partir de la versión 10, solo se considera una actualización de la versión principal un cambio en el primer número. Por ejemplo, de 10.0 a 10.1 es una actualización de versión _secundaria_. De la versión 10 a la 11 se consideraría una actualización de versión _principal_.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las extensiones de PostgreSQL admitidas,consulte el [documento de extensiones](concepts-extensions.md).
