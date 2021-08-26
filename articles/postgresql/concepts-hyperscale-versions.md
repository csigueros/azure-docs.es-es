---
title: 'Versiones compatibles: Hiperescala (Citus) en Azure Database for PostgreSQL'
description: Versiones de PostgreSQL disponibles en Azure Database for PostgreSQL para Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 299005d8d7c961335decbce46bab8c558d2589a1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734976"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Versiones de base de datos compatibles en Azure Database for PostgreSQL para Hiperescala (Citus)

## <a name="postgresql-versions"></a>Versiones de PostgreSQL

La versión de PostgreSQL que se ejecuta en un grupo de servidores de Hiperescala (Citus) se puede personalizar durante la creación. Actualmente Hiperescala (Citus) admite las siguientes versiones principales:

### <a name="postgresql-version-13"></a>PostgreSQL, versión 13

La versión secundaria actual es la 13.3. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/13/static/release-13-2.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

### <a name="postgresql-version-12"></a>PostgreSQL, versión 12

La versión secundaria actual es la 12.7. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-6.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

### <a name="postgresql-version-11"></a>PostgreSQL, versión 11

La versión secundaria actual es la 11.12. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-11.html) para obtener más información sobre las mejoras y correcciones de esta versión secundaria.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL, versión 10 y anteriores

No se admite la versión 10 de PostgreSQL ni las anteriores para Azure Database for PostgreSQL: Hiperescala (Citus).

## <a name="citus-and-other-extension-versions"></a>Citus y otras versiones de extensión

En función de la versión de PostgreSQL que se ejecute en un grupo de servidores, también se instalarán diferentes [versiones de las extensiones de Postgres](concepts-hyperscale-extensions.md).  En concreto, Postgres 13 viene con Citus 10, y las versiones anteriores de Postgres vienen con Citus 9.5.

## <a name="next-steps"></a>Pasos siguientes

* Consulte qué [extensiones](concepts-hyperscale-extensions.md) se instalan con cada versión.
* Aprenda a [crear un grupo de servidores de Hiperescala (Citus)](quickstart-create-hyperscale-portal.md).
