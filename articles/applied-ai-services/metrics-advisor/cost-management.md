---
title: Administración de costos con Azure Metrics Advisor
titleSuffix: Azure Applied AI Services
description: Obtenga información sobre la administración de costos y los precios de Azure Metrics Advisor.
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: mbullwin
ms.openlocfilehash: e13aeefca9ab5b9430f1a6b591a8b8b7b415b46f
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342513"
---
# <a name="azure-metrics-advisor-cost-management"></a>Administración de costos de Azure Metrics Advisor

Azure Metrics Advisor supervisa el rendimiento de los motores de crecimiento de la organización, incluidos los ingresos de ventas y las operaciones de fabricación. Identifique y corrija rápidamente los problemas mediante una eficaz combinación de supervisión casi en tiempo real, adaptando los modelos a su escenario y ofreciendo análisis pormenorizados con diagnósticos y alertas. Solo se le cobrará por las series temporales analizadas por el servicio. No hay ningún compromiso por adelantado ni una cuota mínima.

> [!NOTE]
> En este artículo, se describe cómo se calculan los precios para ayudarle con el planeamiento y la administración de costos al usar Azure Metric Advisor. Los precios de este artículo no reflejan los precios reales y son solo para fines de ejemplo. Para obtener la información de precios más reciente, consulte la [página oficial de precios de Metrics Advisor](https://azure.microsoft.com/pricing/details/metrics-advisor/).  

## <a name="key-points-about-cost-management-and-pricing"></a>Puntos clave sobre la administración de costos y los precios

- Se le cobrará por el número de **series temporales distintas** analizadas durante un mes. Si se analiza un punto de datos para una serie temporal, también se calculará.
- El número de series temporales distintas es **independiente** de su granularidad. Una serie temporal por horas y una serie temporal diaria se cobrarán al mismo precio. 
- Se le cobrará en función de la estructura de precios por niveles que se muestra a continuación. El primer día del mes siguiente se inicializará una nueva ventana de estadísticas.  
- Cuantas más series temporales incorpore al servicio para su análisis, menor precio pagará por cada serie temporal. 

**Tenga en cuenta de nuevo que los precios siguientes son solo con fines de ejemplo**. Para obtener la información de precios más reciente, consulte la [página oficial de precios de Metrics Advisor](https://azure.microsoft.com/pricing/details/metrics-advisor/).

| Series temporales analizadas/mes| USD por serie temporal |
|--------|-----|
| Gratis: primeras 25 series temporales | $- |
| De 26 series temporales a 1000 series temporales | 0,75 USD |
| De 1001 series temporales a 5000 series temporales | 0,50 USD |
| De 5001 series temporales a 10 000 series temporales | 0,25 USD|
| De 20001 series temporales a 50 000 series temporales| 0,10 USD|
| Más de 50 000 series temporales | 0,05 USD |


Para ayudarle a obtener una comprensión básica de Metrics Advisor y empezar a explorar el servicio, se ofrece una cantidad incluida para permitirle analizar hasta 25 series temporales de forma gratuita. 

## <a name="pricing-examples"></a>Ejemplos de precios

### <a name="example-1"></a>Ejemplo 1
<!-- introduce statistic window-->

En el mes 1, un cliente ha incorporado una fuente de distribución de datos con 25 series temporales para analizar la primera semana. Después, incorporan otra fuente de distribución de datos con 30 series temporales la segunda semana. Pero en la tercera semana, eliminan las 30 series temporales que se incorporaron durante la segunda semana. En ese caso, se analizan **55** series temporales distintas en el mes 1, se cobrará al cliente por **30** de ellas (excluidas las 25 series temporales del nivel gratis), y se encuentran en el nivel 1. El costo mensual es: 30 * 0,75 USD = **22,5 USD**. 

| Nivel de volumen | USD por serie temporal | USD al mes | 
| ------------| ----------------- | ----------- |
| Primeras 30 series temporales (55-25) | 0,75 USD | 22,5 USD |
| **Total = 30 series temporales** | | **22,5 USD al mes** |

En el mes 2, el cliente no ha incorporado ni eliminado ninguna serie temporal. En ese caso, se han analizado 25 series temporales en el mes 2. No se cargará ningún costo. 

### <a name="example-2"></a>Ejemplo 2
<!-- introduce how time series is calculated-->

Un responsable de planeamiento de la empresa debe realizar un seguimiento de los ingresos de la empresa como indicador de la salud empresarial. Normalmente, hay un patrón semana a semana en el que el cliente incorpora las métricas a Metrics Advisor para analizar las anomalías. Metrics Advisor puede aprender el patrón a partir de los datos históricos y realizar la detección en los puntos de datos de seguimiento. Es posible que se detecte una caída repentina como una anomalía, lo que puede indicar un problema subyacente, como una interrupción del servicio o una oferta promocional que no funciona según lo previsto. También puede que se detecte un pico inesperado como una anomalía, lo que puede indicar una campaña de marketing de gran éxito o una importante ganancia de clientes. 

La métrica se analiza en **100 categorías de productos** y **10 regiones**, por lo que el número de series temporales distintas que se analizan se calcula como: 

```
1(Revenue) * 100 product categories * 10 regions = 1,000 analyzed time series
```

Según el modelo de precios por niveles descrito anteriormente, se cobran 1000 series temporales analizadas al mes a (1000 - 25) * 0,75 USD = **731,25 USD**. 

| Nivel de volumen | USD por serie temporal | USD al mes | 
| ------------| ----------------- | ----------- |
| Primeras 975 series temporales (1000-25) | 0,75 USD | 731,25 USD |
| **Total = 30 series temporales** | | **731,25 USD al mes** |

### <a name="example-3"></a>Ejemplo 3
<!-- introduce cost for multiple metrics and -->

Después de validar los resultados de la detección sobre la métrica de ingresos, al cliente le gustaría incorporar dos métricas más para analizarlas. Una es el costo y otra es el DAU (usuario activo diario) de su sitio web. También les gustaría agregar una nueva dimensión con **20 canales**. En el mes, 10 de las 100 categorías de productos se interrumpen después de la primera semana y no se analizan más. Además, se incluyen 10 nuevas categorías de productos en la tercera semana del mes y la serie temporal correspondiente se analiza para la mitad del mes. En ese caso, el número de series temporales distintas que se han analizado se calcula como: 

```    
3(Revenue, cost and DAU) * 110 product categories * 10 regions * 20 channels = 66,000 analyzed time series
```

Según el modelo de precios por niveles descrito anteriormente, se cobran 66 000 series temporales analizadas al mes clasificadas en el nivel 5 por un importe de **10 281,25 USD**. 

| Nivel de volumen | USD por serie temporal | USD al mes | 
| ------------| ----------------- | ----------- |
| Primeras 975 series temporales (1000-25) | 0,75 USD | 731,25 USD |
| Siguientes 4000 series temporales | 0,50 USD | 2000 USD |
| Siguientes 15 000 series temporales | 0,25 USD | 3750 USD |
| Siguientes 30 000 series temporales | 0,10 USD | 3000 USD |
| Siguientes 16 000 series temporales | 0,05 USD | 800 USD |
| **Total = 65 975 series temporales** | | **10 281,25 USD al mes** |

## <a name="next-steps"></a>Pasos siguientes

- [Administración de las fuentes de distribución de datos](how-tos/manage-data-feeds.md)
- [Configuraciones para distintos orígenes de datos](data-feeds-from-different-sources.md)
- [Configuración de métricas y ajuste de la configuración de la detección](how-tos/configure-metrics.md)


