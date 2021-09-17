---
title: Niveles de servicio de SQL Managed Instance habilitado para Azure Arc
description: Explica los niveles de servicio disponibles para Azure Arc las implementaciones de SQL Managed Instance habilitadas.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: f0196d2a763b27c43fedb6371668321460f09c6f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786184"
---
# <a name="azure-arc-enabled-sql-managed-instance-service-tiers"></a>Niveles de servicio de SQL Managed Instance habilitado para Azure Arc

Como parte de la familia de productos de Azure SQL, SQL Managed Instance para Azure Arc está disponible en dos niveles de servicio de [núcleo virtual](../../azure-sql/database/service-tiers-vcore.md).

- El nivel **De uso general** es un nivel económico diseñado para la mayoría de las cargas de trabajo con características comunes de rendimiento y disponibilidad.
- El nivel **Crítico para la empresa** está diseñado para las cargas de trabajo sensibles al rendimiento con requisitos estrictos de disponibilidad.

En este momento, el nivel de servicio Crítico para la empresa está en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). El nivel de servicio de uso general está disponible con carácter general. 

En Azure, Microsoft proporciona almacenamiento y proceso con Acuerdos de Nivel de Servicio garantizados para el rendimiento, el rendimiento, la disponibilidad, etc. en cada uno de los niveles de servicio. Con los servicios de datos para Azure Arc, los clientes proporcionan el almacenamiento y el proceso. Por lo tanto, no hay ningún Acuerdo de Nivel de Servicio garantizado proporcionado a los clientes con servicios de datos para Azure Arc. Sin embargo, los clientes obtienen la flexibilidad de traer su propio hardware de rendimiento independientemente del nivel de servicio. 

## <a name="service-tier-comparison"></a>Comparación de niveles de servicio

A continuación se muestra una descripción de las distintas funcionalidades disponibles en los servicios de datos para Azure Arc en los dos niveles de servicio:


Área | Crítico para la empresa (versión preliminar)* | De uso general
----------|-----------------|------------------
Conjunto de características | Igual que SQL Server Enterprise Edition | Igual que SQL Server Standard Edition
Límite de CPU/instancia | Sin límite  | 24 núcleos
Límite de memoria/instancia | Sin límite | 128 GB
Alta disponibilidad | grupo de disponibilidad | Instancia única con reimplementación y almacenamiento compartido de Kubernetes.
Escalado horizontal de lectura | grupo de disponibilidad | Ninguno
Tipos de cambio AHB para el componente IP del precio | 1:1 Enterprise Edition <br> 4:1 Edición estándar | 1:4 Enterprise Edition <br> 1:1 Edición estándar 
Precios para desarrollo/pruebas | Sin costo | Sin costo

\* Actualmente, el nivel de servicio crítico para la empresa está en versión preliminar y no incurre en ningún cargo por su uso durante esta versión preliminar. Algunas de las características pueden cambiar a medida que nos acercamos a la disponibilidad general.

## <a name="how-to-choose-between-the-service-tiers"></a>Cómo elegir entre los niveles de servicio

Dado que los clientes traen su propio hardware con requisitos de rendimiento y disponibilidad en función de sus necesidades empresariales, los principales diferenciadores entre los niveles de servicio son los que se proporcionan en el nivel de software. 

### <a name="choose-general-purpose-if"></a>Elija el propósito general si

- Los requisitos de la CPU o memoria cumplen o están dentro de los límites del nivel de servicio de uso general.
- Las opciones de alta disponibilidad proporcionadas por Kubernetes, como las reimplementaciones de pods, son suficientes para la carga de trabajo.
- La aplicación no necesita escalado horizontal de lectura.
- La aplicación no requiere ninguna de las características que se encuentran en el nivel de servicio crítico para la empresa (igual que SQL Server Enterprise Edition).

### <a name="choose-business-critical-if"></a>Elija la opción de crítico para la empresa si

- Los requisitos de CPU o memoria superan los límites del nivel de servicio de uso general.
- La aplicación requiere un mayor nivel de alta disponibilidad, como los grupos de disponibilidad integrados, para controlar las conmutaciones por error de la aplicación que lo que ofrece Kubernetes. 
- La aplicación puede aprovechar el escalado horizontal de lectura para descargar cargas de trabajo de lectura en las réplicas secundarias.
- La aplicación requiere características que solo se encuentran en el nivel de servicio crítico para la empresa (igual que SQL Server Enterprise Edition).
