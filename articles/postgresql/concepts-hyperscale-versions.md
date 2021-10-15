---
title: 'Versiones compatibles: Hiperescala (Citus) en Azure Database for PostgreSQL'
description: Versiones de PostgreSQL disponibles en Azure Database for PostgreSQL para Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 63f9cf024101fee3049eddc5f91497ab978d2821
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399695"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Versiones de base de datos compatibles en Azure Database for PostgreSQL para Hiperescala (Citus)

## <a name="postgresql-versions"></a>Versiones de PostgreSQL

La versión de PostgreSQL que se ejecuta en un grupo de servidores de Hiperescala (Citus) se puede personalizar durante la creación. Actualmente Hiperescala (Citus) admite las siguientes versiones principales:

### <a name="postgresql-version-14"></a>PostgreSQL, versión 14

La versión secundaria actual es la 14.0. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/14/release-14.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

### <a name="postgresql-version-13"></a>PostgreSQL, versión 13

La versión secundaria actual es la 13.4. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/13/release-13-4.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

### <a name="postgresql-version-12"></a>PostgreSQL, versión 12

La versión secundaria actual es la 12.8. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/12/release-12-8.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

### <a name="postgresql-version-11"></a>PostgreSQL, versión 11

La versión secundaria actual es la 11.13. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/11/release-11-13.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL, versión 10 y anteriores

No se admite la versión 10 de PostgreSQL ni las anteriores para Azure Database for PostgreSQL: Hiperescala (Citus).

## <a name="citus-and-other-extension-versions"></a>Citus y otras versiones de extensión

En función de la versión de PostgreSQL que se ejecute en un grupo de servidores, también se instalarán diferentes [versiones de las extensiones de Postgres](concepts-hyperscale-extensions.md). En concreto, las versiones 12-14 de Postgres vienen con Citus 10, y las versiones anteriores de Postgres vienen con Citus 9.5.

## <a name="next-steps"></a>Pasos siguientes

* Consulte qué [extensiones](concepts-hyperscale-extensions.md) se instalan con cada versión.
* Aprenda a [crear un grupo de servidores de Hiperescala (Citus)](quickstart-create-hyperscale-portal.md).
