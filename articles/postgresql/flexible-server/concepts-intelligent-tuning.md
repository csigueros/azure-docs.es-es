---
title: 'Ajuste inteligente: Azure Database for PostgreSQL: servidor flexible'
description: 'En este artículo se describe la característica de ajuste inteligente de Azure Database for PostgreSQL: servidor flexible.'
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: ca4dbea3701007d29327b5f8f26e0566f77b0c13
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455917"
---
# <a name="perform-intelligent-tuning-in-azure-database-for-postgresql---flexible-server"></a>Ejecución del ajuste inteligente de Azure Database for PostgreSQL: servidor flexible

> [!IMPORTANT]
> Azure Database for PostgreSQL: servidor flexible y el ajuste inteligente están en versión preliminar.

**Se aplica a:** Azure Database for PostgreSQL: servidor flexible, versiones 11 y posteriores.

Las característica de ajuste inteligente de Azure Database for PostgreSQL: servidor flexible proporciona una manera de mejorar automáticamente el rendimiento de la base de datos. El ajuste inteligente ajusta automáticamente los parámetros `checkpoint_completion_target`, `min_wal_size` y `max_wal_size` en función de los patrones de uso y los valores. Consulta las estadísticas de la base de datos cada 30 minutos y realiza ajustes continuos para optimizar el rendimiento sin ninguna interacción.

El ajuste inteligente es una característica opcional, por lo que un servidor no la tiene activa de manera predeterminada. Está disponible para bases de datos únicas y no es global. Si se habilita en una base de datos, no se habilita en todas las bases de datos conectadas.

## <a name="enable-intelligent-tuning-by-using-the-azure-portal"></a>Habilitación del ajuste inteligente mediante Azure Portal

1. Inicie sesión en Azure Portal y seleccione el servidor de Azure Database for PostgreSQL.
2. Seleccione **Parámetros del servidor** en la sección **Configuración** del menú.
3. Busque el parámetro de ajuste inteligente.
4. Establezca el valor en **True** y seleccione **Guardar**.

Espere hasta 35 minutos para que el primer lote de datos se almacene en la base de datos *azure_sys*.

## <a name="information-about-intelligent-tuning"></a>Información sobre el ajuste inteligente

El ajuste inteligente funciona en torno a tres parámetros principales de momento: `checkpoint_completion_target`, `min_wal_size` y `max_wal_size`.

Estos tres parámetros afectan principalmente a: 

* La duración de los puntos de comprobación.
* La frecuencia de los puntos de comprobación.
* La duración de las sincronizaciones.

El ajuste inteligente funciona en ambas direcciones. Intenta reducir las duraciones durante cargas de trabajo elevadas y aumentarlas durante los segmentos inactivos. De esta manera puede obtener resultados personalizados durante períodos difíciles sin actualizaciones manuales.

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

* El ajuste inteligente realiza optimizaciones solo en rangos específicos. Es posible que la característica no realice ningún cambio.
* Las bases de datos eliminadas de la consulta pueden ocasionar ligeros retrasos en la ejecución de mejoras de la característica.
* En este momento, la característica realiza optimizaciones solo en las secciones de almacenamiento.
