---
title: 'Nivel básico: Hiperescala (Citus) en Azure Database for PostgreSQL'
description: 'Nivel básico de un solo nodo para Azure Database for PostgreSQL : Hiperescala (Citus)'
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: b39ddb8841b5b9a1e0665c94776363162575ad53
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734979"
---
# <a name="basic-tier"></a>Nivel Basic

El nivel básico de Azure Database for PostgreSQL: Hiperescala (Citus) es una manera sencilla de crear un grupo de servidores pequeño que se pueda escalar más adelante. Aunque los grupos de servidores del nivel estándar tienen un nodo de coordinación y al menos dos nodos de trabajo, el nivel básico ejecuta todo en un único nodo de base de datos.

Aparte de usar menos nodos, el nivel básico tiene todas las características del nivel estándar. Al igual que el nivel estándar, admite alta disponibilidad, réplicas de lectura y almacenamiento de tablas en columnas, entre otras características.

## <a name="choosing-basic-vs-standard-tier"></a>Elección del nivel básico frente al estándar

El nivel básico puede ser una opción de implementación económica y cómoda para el desarrollo inicial, las pruebas y la integración continua. Usa un único nodo de base de datos y presenta la misma API de SQL que el nivel estándar. Puede probar aplicaciones con el nivel básico y, posteriormente, [cambiar al nivel estándar](howto-hyperscale-scale-grow.md#add-worker-nodes) con la confianza de que la interfaz sigue siendo la misma.

El nivel básico también es apropiado para cargas de trabajo más pequeñas en producción. Hay espacio para escalar verticalmente *dentro* del nivel básico aumentando el número de núcleos virtuales de servidor.

Cuando se requiera una mayor escala de inmediato, use el nivel estándar. Su grupo de servidores permitidos más pequeño tiene un nodo de coordinación y dos trabajadores. Puede optar por usar más nodos en función de su caso de uso, como se describe en el proceso de [tamaño inicial](howto-hyperscale-scale-initial.md).

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [aprovisionar el nivel básico](quickstart-create-hyperscale-basic-tier.md).
* Cuando esté listo, consulte cómo [cambiar](howto-hyperscale-scale-grow.md#add-worker-nodes) del nivel básico al nivel estándar.
* La opción de [almacenamiento en columnas](concepts-hyperscale-columnar.md) está disponible en el nivel básico y estándar.
