---
title: Interpretación de la puntuación | Microsoft Docs
description: Obtenga información sobre cómo interpretar la puntuación. La pestaña de puntuación contiene los resultados agregados y analizados de las pruebas.
services: internet-analyzer
author: KumudD
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: kumud
ms.openlocfilehash: dbfe2147c87d1d113543935915923966e965b102
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437036"
---
# <a name="interpreting-your-scorecard"></a>Interpretación de la puntuación

La pestaña de puntuación contiene los resultados agregados y analizados de las pruebas. Cada prueba tiene su propia puntuación. Las puntuaciones proporcionan resúmenes rápidos y significativos de los resultados de medidas para proporcionar resultados controlados por datos para los requisitos de red. Internet Analyzer se encarga del análisis, lo que le permite centrarse en la decisión.

La pestaña de puntuación se encuentra en el menú de recursos de Internet Analyzer. 


## <a name="filters"></a>Filtros

* ***Prueba:*** Seleccione la prueba cuyos resultados desea ver: cada prueba tiene su propia puntuación. Los datos de prueba aparecerán una vez que haya datos suficientes para completar el análisis; en la mayoría de los casos, esto tomará aproximadamente 24 horas. 
* ***Período de tiempo y fecha de finalización:*** Se generan tres puntuaciones cada día, cada puntuación refleja un período de agregación diferente: las 24 horas anteriores (día), los siete días anteriores (semana) y los 30 días anteriores (mes). Use el filtro "fecha de finalización" para seleccionar el último día del período de tiempo que desea ver. 
* ***País:*** Para cada país en el que tenga usuarios finales, se genera una puntuación. El filtro global contiene todos los usuarios finales.

## <a name="measurement-count"></a>Número de medidas

El número de medidas afecta a la confianza del análisis. Cuanto mayor sea el número, más preciso será el resultado. Como mínimo, las pruebas deben tener un mínimo de 100 medidas por punto de conexión al día. Si el número de medidas es demasiado bajo, configure el cliente de JavaScript para que se ejecute con más frecuencia en la aplicación. El número de medidas para los puntos de conexión A y B debe ser muy similar, aunque se esperan pequeñas diferencias y son aceptables. En caso de grandes diferencias, no se debe confiar en los resultados.

## <a name="percentiles"></a>Percentiles

La latencia, medida en milisegundos, es una métrica popular para medir la velocidad entre un origen y un destino en Internet. Los datos de latencia no se distribuyen de forma normal (es decir, no siguen una "curva de campana") porque hay una "larga cola" de grandes valores de latencia que sesgan los resultados cuando se usan estadísticas como la media aritmética. Como alternativa, los percentiles proporcionan una forma "sin distribución" para analizar los datos. Por ejemplo, la mediana, o el percentil 50, resume la mitad de la distribución: la mitad de los valores están por encima y la otra mitad están por debajo. Un valor del percentil 75 significa que es superior al 75 % de todos los valores de la distribución. Internet Analyzer hace referencia a percentiles abreviados como P50, P75 y P95.

Los percentiles de Internet Analyzer son _métricas de ejemplo_. Esto contrasta con la verdadera _métrica de población_. Por ejemplo, la verdadera mediana de latencia diaria de población entre alumnos de la Universidad del Sur de California y Microsoft es el valor de la mediana de latencia de todas las solicitudes durante ese día. Sin embargo, medir el valor de todas las solicitudes es poco práctico, por lo que suponemos que una muestra razonablemente grande es representativa de la población real.

Para fines de análisis, P50 (mediana), resulta útil como un valor esperado para una distribución de latencia. Los percentiles más altos, como P95, son útiles para identificar cuán alta es la latencia en los peores casos. Si está interesado en comprender la latencia del cliente en general, P50 es la métrica correcta en la que debe centrarse. Si lo que le preocupa es entender el rendimiento de los clientes con peores resultados, entonces debe centrarse en P95. P75 es el punto medio entre estos dos.


## <a name="deltas"></a>Deltas

Un delta es la diferencia en los valores de métricas para los puntos de conexión A y B. Los deltas se calculan para mostrar las ventajas de B sobre A. Los valores positivos indican que B tuvo un mejor rendimiento que A, mientras que los valores negativos indican que el rendimiento de B es peor. Los deltas pueden ser absolutos (por ejemplo, 10 milisegundos) o relativos (5 %).

## <a name="confidence-interval"></a>intervalo de confianza 

Los intervalos de confianza (CI) son un intervalo de valores que tienen una probabilidad de contener la métrica de población, como la mediana, el P75 o el promedio. Seguimos la convención estadística común del uso del CI de 95 %.

En el caso de Internet Analyzer, un intervalo de confianza reducido es bueno porque muestra que la métrica de muestra es probablemente muy cercana a la métrica de población real. Un intervalo de confianza amplio significa menos certeza de que la métrica de muestra refleje la métrica de población real. La mejor manera de mejorar el CI es aumentar el número de medidas.

## <a name="time-series"></a>Serie temporal 

Una serie temporal muestra cómo cambia una métrica con el tiempo. En Internet, hay muchos factores temporales que afectan al rendimiento, como períodos de tráfico máximo, las diferencias en el número de usuarios entre semana, en fines de semana y en días festivos.


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte la [Información general de Internet Analyzer](internet-analyzer-overview.md).
