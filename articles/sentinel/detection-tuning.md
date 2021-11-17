---
title: Obtención de recomendaciones de ajuste preciso para las reglas de análisis en Microsoft Sentinel
description: Obtenga información sobre cómo ajustar las reglas de detección de amenazas en Microsoft Sentinel mediante el uso de recomendaciones generadas automáticamente para reducir los falsos positivos y, al mismo tiempo, mantener la cobertura de detección de amenazas.
author: yelevin
ms.author: yelevin
ms.service: azure-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 854e8fa7d129140fca1a16dc02971bd2cd99ceb7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323052"
---
# <a name="get-fine-tuning-recommendations-for-your-analytics-rules-in-microsoft-sentinel"></a>Obtención de recomendaciones de ajuste preciso para las reglas de análisis en Microsoft Sentinel

> [!IMPORTANT]
>
> El ajuste de la detección se encuentra actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

El ajuste preciso de las reglas de detección de amenazas de SIEM puede ser un proceso difícil, delicado y continuo de equilibrio entre maximizar la cobertura de detección de amenazas y minimizar las tasas de falsos positivos. Microsoft Sentinel simplifica este proceso mediante el uso del aprendizaje automático para analizar miles de millones de señales de los orígenes de datos, y sus respuestas a incidentes a lo largo del tiempo, deduciendo patrones y proporcionando recomendaciones y conclusiones útiles que pueden reducir significativamente la sobrecarga de optimización y permitirle centrarse en detectar y responder a amenazas reales.

Ahora, las recomendaciones y la información de optimización están integradas en las reglas de análisis. En este artículo se explica qué muestran estas conclusiones y cómo se pueden implementar las recomendaciones.

## <a name="view-rule-insights-and-tuning-recommendations"></a>Visualización de conclusiones sobre las reglas y recomendaciones de ajuste

Para ver si Microsoft Sentinel tiene recomendaciones de ajuste para cualquiera de sus reglas de análisis, seleccione **Análisis** en el menú de navegación de Microsoft Sentinel.

Las reglas que tengan recomendaciones presentarán un icono de bombilla, como se muestra a continuación:

:::image type="content" source="media/detection-tuning/rule-list-with-insight.png" alt-text="Captura de pantalla de la lista de reglas de análisis con un indicador de recomendación." lightbox="media/detection-tuning/rule-list-with-insight.png":::

Edite la regla para ver las recomendaciones y el resto de conclusiones, que se mostrarán en la pestaña **Establecer la lógica de la regla** del asistente para reglas de análisis, debajo del área **Results simulation** (Simulación de resultados).

:::image type="content" source="media/detection-tuning/tuning-insights.png" alt-text="Captura de pantalla de las conclusiones de optimización en la regla de análisis." lightbox="media/detection-tuning/tuning-insights.png":::

### <a name="types-of-insights"></a>Tipos de conclusiones

El área **Optimización de conclusiones** consta de varios paneles por los que puede desplazarse, y cada uno muestra algo diferente. El período de tiempo (14 días) para el que se muestran las conclusiones aparece en la parte superior del cuadro.

1. El primer panel de conclusiones muestra información estadística: el número medio de alertas por incidente, el número de incidentes abiertos y el número de incidentes cerrados, agrupados por clasificación (verdadero/falso positivo). Esta información le ayuda a conocer la carga de esta regla y a comprender si es necesario realizar alguna optimización, por ejemplo, si hace falta ajustar la configuración de agrupación.

    :::image type="content" source="media/detection-tuning/rule-efficiency.png" alt-text="Captura de pantalla de la información de eficiencia de la regla.":::

    Esta conclusión es el resultado de una consulta de Log Analytics. Si selecciona **Average alerts per incident** (Promedio de alertas por incidente), se le redirigirá a la consulta de Log Analytics que produjo esta conclusión. Si selecciona **Incidentes abiertos**, se le redirigirá a la hoja **Incidentes**.

1. El segundo panel de conclusiones le recomienda que excluya una lista de [entidades](entities-in-azure-sentinel.md). Estas entidades están muy correlacionadas con los incidentes que ha cerrado y que ha clasificado como **falsos positivos**. Seleccione el signo más junto a cada entidad de la lista para excluirla de la consulta en ejecuciones futuras de esta regla. 

    :::image type="content" source="media/detection-tuning/entity-exclusion.png" alt-text="Captura de pantalla de la recomendación de exclusión de entidades.":::

    Esta recomendación la generan los modelos avanzados de ciencia de datos y Machine Learning de Microsoft. La inclusión de este panel en el área **Optimización de conclusiones** depende de si hay recomendaciones para mostrar.

1. El tercer panel de conclusiones muestra las cuatro entidades asignadas que aparecen con más frecuencia en todas las alertas generadas por esta regla. La asignación de entidades debe configurarse en la regla para que esta conclusión produzca resultados. Esta conclusión podría ayudarle a tener en cuenta las entidades que "acaparan toda la atención" y desvían la atención del resto. Es posible que quiera controlar estas entidades por separado con una regla diferente, o bien puede decidir que son falsos positivos o entidades irrelevantes y excluirlas de la regla.

    :::image type="content" source="media/detection-tuning/top-entities.png" alt-text="Captura de pantalla de la conclusión sobre las entidades principales.":::

    Esta conclusión es el resultado de una consulta de Log Analytics. Si selecciona cualquiera de las entidades, se le redirigirá a la consulta de Log Analytics que produjo esta conclusión.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:
- [Control de falsos positivos en Microsoft Sentinel](false-positives.md)
- [Uso de datos de UEBA para analizar falsos positivos](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md)
