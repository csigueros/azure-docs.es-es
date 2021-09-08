---
title: Uso de métricas de supervisión con Azure Functions
description: Aprenda a usar las métricas de Azure Monitor para ver y consultar los datos de telemetría de Azure Functions que Azure Application Insights recopila y almacena.
ms.topic: how-to
ms.date: 07/4/2021
ms.openlocfilehash: b5f0b483295cccff8caf97503233e1e7881173cf
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568619"
---
# <a name="using-azure-monitor-metric-with-azure-functions"></a>Uso de métricas de Azure Monitor con Azure Functions

Azure Functions se integra con las métricas de Azure Monitor para permitirle el análisis de las métricas generadas por la aplicación de funciones durante la ejecución. Para más información, consulte [Información general sobre las métricas de Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md). Estas métricas indican cómo se ejecuta la aplicación de funciones en la plataforma de App Service. Puede revisar los datos de consumo de recursos usados para calcular los costos del plan de consumo. Para investigar la telemetría detallada de las ejecuciones de la función, incluidos los datos de registro, también debe usar [Application Insights](functions-monitoring.md) en Azure Monitor. 

> [!NOTE]
> Las métricas de Azure Monitor no se admiten actualmente cuando la aplicación de funciones se ejecuta en Linux en un plan de consumo.

## <a name="available-metrics"></a>Métricas disponibles

Azure Monitor recopila datos numéricos de un conjunto de recursos supervisados, los cuales se almacenan en una base de datos de serie temporal. Azure Monitor recopila métricas específicas de Functions y los recursos de App Service subyacentes.   

### <a name="functions-specific-metrics"></a>Métricas específicas de Functions

Hay dos métricas específicas de Functions que son de interés:

| Métrica | Descripción |
| ---- | ---- |
| **FunctionExecutionCount** | El número de ejecuciones de función indica el número de veces que se ha ejecutado la aplicación de funciones. Este valor se correlaciona con el número de veces que se ejecuta una función en la aplicación. |
| **FunctionExecutionUnits** | Las unidades de ejecución de función son una combinación del tiempo de ejecución y el uso de memoria.  Los datos de memoria no son una métrica disponible actualmente en Azure Monitor. Sin embargo, si desea optimizar el uso de memoria de la aplicación, puede utilizar los datos del contador de rendimiento recopilados por Application Insights. Esta métrica no se admite actualmente en los planes Prémium y Dedicado (App Service) que se ejecutan en Linux.|

Estas métricas se usan específicamente al [calcular los costos del plan de consumo](functions-consumption-costs.md). 

### <a name="general-app-service-metrics"></a>Métricas de App Service generales

Además de las métricas específicas de la función, la plataforma de App Service implementa más métricas, las cuales las puede usar para supervisar las aplicaciones de funciones. Para obtener la lista completa, consulte [métricas disponibles para aplicaciones de App Service](../app-service/web-sites-monitor.md#understand-metrics).

## <a name="accessing-metrics"></a>Acceso a métricas

Puede usar el [explorador de métricas de Azure Monitor](../azure-monitor/essentials/metrics-getting-started.md) en [Azure Portal](https://portal.azure.com) o las API REST para obtener los datos de métricas de Monitor. 

En los ejemplos siguientes se usan las métricas de Monitor para ayudar a calcular el costo de ejecutar la aplicación de funciones en un plan de consumo. Para más información sobre los costos del plan de consumo, consulte [Estimación de los costos según el plan de consumo](functions-consumption-costs.md).

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]  

Para más información sobre el uso del explorador de supervisión para ver las métricas, consulte [Introducción al Explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md).

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de la supervisión de Azure Functions:

+ [Monitor Azure Functions](functions-monitoring.md)
+ [Análisis de la telemetría de Azure Functions en Application Insights](analyze-telemetry-data.md)
