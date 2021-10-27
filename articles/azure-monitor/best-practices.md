---
title: Procedimientos recomendados de Azure Monitor
description: Instrucciones y recomendaciones para implementar Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 2caff70e56f80d4f46859cec58275263375ee4de
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181582"
---
# <a name="azure-monitor-best-practices"></a>Procedimientos recomendados de Azure Monitor
En este escenario se proporcionan instrucciones recomendadas para configurar las características de Azure Monitor a fin de supervisar el rendimiento y la disponibilidad de sus aplicaciones y recursos tanto híbridos como de nube. 

Azure Monitor está disponible desde el momento en que se crea una suscripción de Azure. El registro de actividad comienza a recopilar eventos sobre la actividad en la suscripción de forma inmediata y se recopilan métricas de plataforma para los recursos de Azure que haya creado. Hay algunas características disponibles para analizar los datos, como el Explorador de métricas. Otras características requieren configuración. En este escenario se identifican los pasos de configuración necesarios para aprovechar todas las características de Azure Monitor. También se hacen recomendaciones sobre qué características debe aprovechar y cómo determinar las opciones de configuración en función de sus requisitos concretos.

Habilitar Azure Monitor para supervisar todos los recursos de Azure es una combinación de la configuración de los componentes de Azure Monitor y la configuración de los recursos de Azure para generar datos de supervisión para que Azure Monitor los recopile. El objetivo de una implementación completa es recopilar todos los datos útiles de todos sus recursos y aplicaciones de nube y habilitar el conjunto completo de características de Azure Monitor en función de los datos.


> [!IMPORTANT]
> Si no está familiarizado con Azure Monitor o se centra solamente en la supervisión de un único recurso de Azure, debe comenzar con el tutorial [Supervisión de recursos de Azure con Azure Monitor](/essentials/monitor-azure-resource.md). En el tutorial se proporcionan los conceptos generales de Azure Monitor e instrucciones para supervisar un único recurso de Azure. Este escenario ofrece recomendaciones para preparar el entorno de forma que puedan aprovecharse todas las características de Azure Monitor para supervisar el conjunto completo de aplicaciones y recursos a escala de forma conjunta.

## <a name="scope-of-the-scenario"></a>Ámbito del escenario
El objetivo de este escenario es guiarle a través de los pasos básicos de una implementación completa de Azure Monitor para asegurarse de que aprovecha sus características por completo y maximiza la observabilidad de sus aplicaciones y recursos híbridos y de nube. Se centra en los requisitos de configuración y en las opciones de implementación, en lugar de en los detalles de configuración en sí. Se ofrecen vínculos a otro contenido que proporciona los detalles para realizar la configuración necesaria en sí.

## <a name="scenario-articles"></a>Artículos de escenario
En este artículo se presenta el escenario. Si quiere ir directamente a un área específica, consulte uno de los otros artículos que forman parte de este escenario descrito en la tabla siguiente.

| Artículo | Descripción |
|:---|:---|
| [Planeamiento](best-practices-plan.md)  | Planeamiento que debe tener en cuenta antes de iniciar la implementación. Incluye decisiones de diseño e información sobre la organización y los requisitos que debe recopilar. |
| [Configuración de la recopilación de datos](best-practices-data-collection.md) | Tareas necesarias para recopilar los datos de supervisión de las aplicaciones y recursos híbridos y de Azure. |
| [Análisis y visualizaciones](best-practices-analysis.md) | Características estándar y visualizaciones adicionales que puede crear para analizar los datos de supervisión recopilados. |
| [Alertas y respuestas automatizadas](best-practices-alerts.md) | Configure las notificaciones y los procesos que se desencadenan de forma automática al crear una alerta. |




## <a name="next-steps"></a>Pasos siguientes

- [Planeamiento de la estrategia y configuración de supervisión](best-practices-plan.md)