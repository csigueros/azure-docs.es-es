---
title: Actualización de alertas y supervisión clásicas en Azure Monitor
description: Descripción de la retirada de la funcionalidad y los servicios de supervisión clásicos, que antes se mostraban en Azure Portal en Alertas (clásico).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: 64e9c454bfe8df9b65176fe344999e88204720d0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539126"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Alertas y supervisión unificadas de Azure Monitor reemplaza a alertas y supervisión clásicas

Azure Monitor es una pila de supervisión unificada que admite "métricas unificadas" y "alertas unificadas" en los recursos de Azure. Para obtener más información, vea esta [entrada de blog](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Las nuevas plataformas de supervisión y generación de alertas de Azure se han creado para ser más rápidas, inteligentes y extensibles (con lo que se sigue el ritmo de la creciente expansión de la informática en la nube y la línea de la filosofía de Microsoft Intelligent Cloud).

Con la nueva plataforma de supervisión y alertas de Azure, se han retirado las alertas clásicas de Azure Monitor para los usuarios de la nube pública. Las alertas clásicas para la nube de Azure Government y Azure China 21Vianet se retirarán el **29 de febrero de 2024**.

 ![Alerta clásica en Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Le animamos a volver a crear las alertas en la nueva plataforma.

> [!IMPORTANT]
> Las reglas de alerta clásicas creadas en el registro de actividad no caerán en desuso ni se migrarán. Puede acceder a todas las reglas de alerta clásicas creadas en el registro de actividad y usarlas tal cual, desde la nueva Azure Monitor: Alertas. Para más información, vea [Crear, ver y administrar las alertas del registro de actividad mediante Azure Monitor](./alerts-activity-log.md). De forma similar, puede acceder a alertas en Service Health y usarlas tal cual desde la nueva sección Service Health. Para más información, consulte las [alertas en las notificaciones de Service Health](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Métricas y alertas unificadas para recursos de Azure

En marzo de 2018, publicamos la última generación de alertas para recursos de Azure. Las nuevas alertas y plataforma de métricas son más rápidas y proporcionan mayor granularidad con las dimensiones. Las dimensiones permiten segmentar y filtrar por una combinación de valores específicos, condición u operación. Las nuevas alertas de métricas usan grupos de acciones que permiten más notificaciones y acciones de automatización. Consulte más información sobre la [administración de alertas de métricas mediante Azure Monitor](./alerts-metric.md).

Las métricas más recientes de los recursos de Azure están disponibles como:

- **Métricas de la plataforma estándar de Azure Monitor**: que proporciona métricas populares rellenadas previamente de varios productos y servicios de Azure. Para más información, consulte este artículo acerca de las [métricas compatibles con Azure Monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) y [Compatibilidad con alertas de métricas en Azure Monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Métricas personalizadas de Azure Monitor**: que proporciona métricas de orígenes controlados por el usuario, lo que incluye el agente de Azure Diagnostics. Para más información, consulte este artículo acerca de las [métricas personalizadas en Azure Monitor](../essentials/metrics-custom-overview.md). Con las métricas personalizadas también puede publicar las métricas recopiladas por el [agente de Windows Azure Diagnostics](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) y el [agente de InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Retirada de la plataforma de alertas y supervisión clásica

Como se indicó anteriormente, la supervisión y las alertas clásicas anteriores se retiran para los usuarios de la nube pública. Incluye el cierre de las API relacionadas, la interfaz de Azure Portal y los servicios que contiene, aunque siguen activas con un uso limitado hasta el **31 de mayo de 2021**. Las alertas clásicas para la nube de Azure Government y Azure China 21Vianet se retirarán el **29 de febrero de 2024**.

En concreto, el ámbito de retirada es para las métricas clásicas disponibles actualmente en la [sección de alertas (clásicas)](./alerts-classic.overview.md) de Azure portal y accesibles como recursos de [microsoft.insights/alertrules](/rest/api/monitor/alertrules).

Esto significa:

- El servicio de alertas y supervisión clásico se retirará y dejará de estar disponible para la creación de nuevas reglas de alerta.
- Todas las reglas de alerta que sigan en Alertas (clásicas) continuarán ejecutándose y enviando notificaciones.
- Se migrarán la mayoría de las reglas de alertas clásicas. El proceso será ininterrumpido, no habrá tiempo de inactividad y garantizará que los clientes no pierden cobertura de supervisión.
- Las notificaciones activas incluirán la nueva estructura de cargas. Será necesario adaptar el destino para trabajar con la nueva estructura.
- Algunas [reglas de alertas clásicas que no se pueden migrar automáticamente](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) y requerirán una acción manual por parte de los usuarios para seguir ejecutándose.

> [!IMPORTANT]
> Azure Monitor ha implantado una [herramienta para migrar voluntariamente](alerts-using-migration-tool.md) sus reglas de alertas clásicas a la nueva plataforma. Las reglas restantes se migrarán automáticamente una vez que se retire el servicio. Los clientes deberán garantizar la adaptación de la automatización que consume la carga útil de reglas de alertas clásicas para poder controlar la nueva carga útil siguiendo los procedimientos descritos en [Métricas y alertas unificadas para otros recursos de Azure](#unified-metrics-and-alerts-for-azure-resources) después de la migración de las reglas de alertas clásicas. Para obtener más información, consulte [Preparar sus aplicaciones lógicas y los runbooks para la migración de las reglas de alertas clásicas](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Precios de las reglas de alerta migradas

Vamos a implementar una herramienta de migración para ayudarle a migrar las [alertas clásicas](./alerts-classic.overview.md) de Azure Monitor a la nueva experiencia de alertas. Las reglas de alertas migradas y los grupos de acciones migrados correspondientes (correo electrónico, webhook o LogicApp) seguirán siendo gratuitos. La funcionalidad que tenía con las alertas clásicas, incluida la capacidad de editar el umbral, el tipo de agregación y la granularidad de agregaciones, seguirá estando disponible de forma gratuita con la regla de alertas migrada. Sin embargo, si edita la regla de alertas migrada para usar cualquiera de las nuevas características, notificaciones o tipos de acción de la plataforma de alertas, se aplicará el cargo correspondiente. Para obtener más información sobre los precios de las reglas de alertas y las notificaciones, consulte los [precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

A continuación se muestran ejemplos de casos en los que se incurrirá en un cargo por la regla de alertas:

- Todas las nuevas reglas de alertas (no migradas) creadas después de las unidades gratuitas en la nueva plataforma de Azure Monitor
- Todos los datos ingeridos y conservados en unidades que no sean las gratuitas incluidas por Azure Monitor
- Todas las pruebas web que ejecuta Application Insights
- Todas las métricas personalizadas almacenadas en unidades que no sean las gratuitas que se incluyen en Azure Monitor
- Todas las reglas de alertas migradas que se editen para usar las características de alertas de métricas más recientes, como la frecuencia, varios recursos o dimensiones, los [umbrales dinámicos](../alerts/alerts-dynamic-thresholds.md), el cambio de recurso o señal, etc.
- Todos los grupos de acciones migrados que se editen para usar notificaciones más recientes, o tipos de acción como SMS, llamada de voz o integración de ITSM.

## <a name="next-steps"></a>Pasos siguientes

* Más información acerca del [nuevo Azure Monitor unificado](../overview.md).
* Más información acerca de las nuevas [alertas de Azure](./alerts-overview.md).
