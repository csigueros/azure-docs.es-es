---
title: 'Ajuste inteligente en Azure Database para PostgreSQL: servidor flexible'
description: En este artículo se describen las características de ajuste inteligente del servidor flexible de Azure Database for PostgreSQL.
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: d16a1842c4dfed044a8ac865a3dcdcd6cbe05bf3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780703"
---
# <a name="perform-intelligent-tuning-on-your-postgresql-flex-server"></a>Realizar el ajuste inteligente en el servidor flexible de PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL: servidor flexible está en versión preliminar

> [!IMPORTANT]
> El ajuste inteligente está en versión preliminar

**Se aplica a:** Azure Database for PostgreSQL: versiones de servidor flexible 11 y posteriores

Las características de ajuste inteligente del servidor flexible de Azure Database for PostgreSQL proporcionan una manera de mejorar automáticamente el rendimiento de las bases de datos. Con nuestro servicio, el ajuste inteligente ajusta automáticamente Checkpoint_completion_target, min_wal_size y max_wal_size en función de los patrones y valores de uso. Nuestro servicio consultará las estadísticas de la base de datos cada 30 minutos y realizará ajustes constantes para optimizar el rendimiento sin ninguna interacción del usuario.

## <a name="enabling-intelligent-tuning"></a>Habilitación del ajuste inteligente

El ajuste inteligente es una característica opcional, por lo que los servidores no lo tiene activo de forma predeterminada. El ajuste está disponible para bases de datos singulares y no es global, por lo que habilitarlo en una base de datos no lo habilita en todas las bases de datos conectadas.

### <a name="enable-intelligent-tuning-using-the-azure-portal"></a>Habilitación del ajuste inteligente mediante Azure Portal

1. Inicie sesión en Azure Portal y seleccione el servidor de Azure Database for PostgreSQL.
2. Seleccione Parámetros del servidor en la sección Configuración del menú.
3. Busque el parámetro Ajuste inteligente.
4. Establezca el valor en True y seleccione Guardar.

Espere hasta 35 minutos para que el primer lote de datos se conserve en la base de datos azure_sys.

## <a name="information-about-intelligent-tuning"></a>Información sobre el ajuste inteligente

El ajuste inteligente funciona en torno a tres características principales en este momento

* Checkpoint_completion_target
* Min_wal_size
* Min_wal_size

Estas tres variables afectan principalmente a lo siguiente

* La duración de los puntos de comprobación
* La frecuencia de los puntos de comprobación
* La duración de las sincronizaciones

El ajuste inteligente funciona en ambas direcciones, intenta reducir las duraciones durante cargas de trabajo elevadas y aumentarlas durante los segmentos inactivos. Estas reglas son las formas en que intentamos optimizar estas características para que los usuarios puedan obtener resultados personalizados durante períodos difíciles sin actualizaciones manuales.

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

* Las optimizaciones solo se emiten en rangos específicos; existe la posibilidad de que no se realice ningún cambio
* Las bases de datos eliminadas de la consulta pueden retrasar la funcionalidad de ajuste inteligente, lo que puede provocar ligeros retrasos en la ejecución de mejoras.
  
A partir de ahora, las optimizaciones solo se realizan en las secciones de almacenamiento; la expansión a otras categorías está aún por determinar.
