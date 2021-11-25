---
title: Control del retraso de ingesta en Microsoft Sentinel | Microsoft Docs
description: Controle el retraso de ingesta en las reglas de análisis programado de Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2021
ms.author: bagol
ms.openlocfilehash: 41143675d54e4353f7ea5402a4503c78a74a12e2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522942"
---
# <a name="handle-ingestion-delay-in-scheduled-analytics-rules"></a>Control del retraso de ingesta en las reglas de análisis

Aunque Microsoft Sentinel puede ingerir datos de [varios orígenes](connect-data-sources.md), el tiempo de ingesta de cada origen de datos puede diferir en distintas circunstancias.

En este artículo se describe cómo el retraso de ingesta puede afectar a las reglas de análisis programadas y cómo puede corregirlas para cubrir estas brechas.

## <a name="why-delay-is-significant"></a>Por qué el retraso es significativo

Por ejemplo, puede escribir una regla de detección personalizada, estableciendo las opciones **Ejecutar consulta cada** y **Búsqueda de datos de los últimos** para que la regla se ejecute cada cinco minutos, buscando datos de los últimos cinco minutos:

:::image type="content" source="media/ingestion-delay/create-rule.png" alt-text="Captura de pantalla que muestra el Asistente para reglas de Analytics: ventana Crear nueva regla." border="false":::

El campo **Buscar datos de los últimos** define una configuración conocida como *ventana de búsqueda.* Idealmente, cuando no hay ningún retraso, esta detección no pierde ningún evento, como se muestra en el diagrama siguiente:

:::image type="content" source="media/ingestion-delay/look-back.png" alt-text="Diagrama que muestra una ventana de cinco minutos atrás." border="false":::

El evento llega a medida que se genera y se incluye en el período de la *ventana de búsqueda.*

Ahora, suponga que hay algún retraso para el origen de datos. En este ejemplo, supongamos que el evento se *ingirió* dos minutos después de haberse *generado*. El retraso es de dos minutos:

:::image type="content" source="media/ingestion-delay/look-back-delay.png" alt-text="Diagrama que muestra ventanas de cinco minutos atrás con un retraso de dos minutos." border="false":::

El evento se genera dentro del primer período de ventana de búsqueda, pero no se ingiere en el área de trabajo de Microsoft Sentinel en la primera ejecución. La próxima vez que se ejecute la consulta programada, ingiere el evento, pero el filtro generado por el tiempo quita el evento porque se produjo hace más de cinco minutos. En este caso, **la regla no activa una alerta**.

## <a name="how-to-handle-delay"></a>Cómo controlar el retraso

Para solucionar el problema, debe conocer el retraso del tipo de datos. En este ejemplo, ya sabe que el retraso es de dos minutos. 

Para sus propios datos, puede reconocer el retraso mediante la función`ingestion_time()` Kusto y calcular la diferencia entre el momento de generación, **TimeGenerated**, y el tiempo de ingesta. Para más información, consulte [Cálculo del retraso en la ingesta](#calculate-ingestion-delay).

Después de determinar el retraso, puede solucionar el problema de la siguiente manera:

- **Aumente el período de la ventana de búsqueda**. La intuición básica le indica que aumentar el tamaño del período de la ventana de búsqueda será útil. Puesto que el período de la ventana de búsqueda es de cinco minutos y el retraso es de dos minutos, establecer el período de la ventana de búsqueda en *siete* minutos le ayudará a solucionar este problema. Por ejemplo, en la configuración de la regla:

    :::image type="content" source="media/ingestion-delay/set-look-back.png" alt-text="Captura de pantalla que muestra cómo establecer la ventana de búsqueda en siete minutos":::.

    En el diagrama siguiente se muestra cómo la ventana de búsqueda incluye ahora el evento que falta:

    :::image type="content" source="media/ingestion-delay/longer-look-back.png" alt-text="Diagrama que muestra ventanas de búsqueda de siete minutos con un retraso de dos minutos." border="false":::

- **Controlar la duplicación**. Solo aumentar el período de la ventana de búsqueda atrás puede crear duplicación, ya que las ventanas de búsqueda se superponen. Por ejemplo, un evento diferente puede tener el aspecto que se muestra en el diagrama siguiente:

    :::image type="content" source="media/ingestion-delay/overlapping-look-back.png" alt-text="Diagrama que muestra cómo las ventanas de búsqueda superpuestas crean duplicación." border="false":::

    Puesto que el valor **TimeGenerated** (hora de generación) del evento se encuentra en ambas ventanas de búsqueda, el evento activa dos alertas. Debe encontrar una manera de resolver la duplicación.

- **Asocie el evento a una ventana de búsqueda específica.** En el primer ejemplo, perdió eventos porque los datos no se ingirieron cuando se ejecutó la consulta programada. Ha ampliado la ventana de búsqueda para incluir el evento, pero esto ha provocado la duplicación. Tiene que asociar el evento a la ventana que amplió para incluirlo.

    Para ello, establezca `ingestion_time() > ago(5m)` en lugar de la regla original `look-back = 5m`. Esta configuración asocia el evento a la primera ventana de búsqueda. Por ejemplo:

    :::image type="content" source="media/ingestion-delay/ago-restriction.png" alt-text="Diagrama que muestra cómo establecer la restricción «ago» (hace) evita la duplicación." border="false":::

    La restricción de tiempo de ingesta ahora recorta los dos minutos adicionales que agregó al período de la ventana de búsqueda. Y, para el primer ejemplo, el segundo período de la ventana de búsqueda de ejecución captura ahora el evento:

    :::image type="content" source="media/ingestion-delay/ago-restriction-capture.png" alt-text="Diagrama que muestra cómo establecer la restricción «ago» (hace) captura el evento." border="false":::

En la consulta de ejemplo siguiente se resume la solución para resolver problemas de retraso en la ingesta:

```kusto
let ingestion_delay = 2min;
let rule_look_back = 5min;
CommonSecurityLog
| where TimeGenerated >= ago(ingestion_delay + rule_look_back)
| where ingestion_time() > ago(rule_look_back)
```


## <a name="calculate-ingestion-delay"></a>Cálculo del retraso en la ingesta

Por defecto, las reglas de alertas programadas de Microsoft Sentinel están configuradas para tener un período de 5 minutos de ventana de búsqueda. Sin embargo, cada origen de datos puede tener su propio retraso individual de ingesta. Al unir varios tipos de datos, debe reconocer los distintos retrasos de cada tipo de datos para configurar correctamente el período de la ventana de búsqueda.

El **Informe de uso del área de trabajo**, que se proporciona de forma predeterminada en Microsoft Sentinel, incluye un panel que muestra la latencia y los retrasos de los distintos tipos de datos que fluyen al área de trabajo.

Por ejemplo:

:::image type="content" source="media/ingestion-delay/end-to-end-latency.png" alt-text="Captura de pantalla del informe de uso del área de trabajo que muestra la latencia de un extremo a otro por tabla":::


## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md)
- [Personalización de los detalles de las alertas de Azure Sentinel | Microsoft Docs](customize-alert-details.md)
- [Administración de versiones de plantilla para las reglas de análisis programados en Azure Sentinel](manage-analytics-rule-templates.md)