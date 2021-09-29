---
title: Rendimiento de Integration Runtime
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo optimizar y mejorar el rendimiento de Azure Integration Runtime en Azure Data Factory y Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 13f632da70944d7c1aec00df112782429dc9620e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787914"
---
# <a name="optimizing-performance-of-the-azure-integration-runtime"></a>Optimización del rendimiento de Azure Integration Runtime

Los flujos de datos se ejecutan en clústeres de Spark que se activan en tiempo de ejecución. La configuración del clúster que se usa se define en el entorno de ejecución de integración (IR) de la actividad. Hay tres consideraciones de rendimiento que se deben tener en cuenta a la hora de definir el entorno de ejecución de integración: el tipo de clúster, el tamaño del clúster y el período de vida.

Para obtener más información sobre cómo crear un entorno de Integration Runtime, consulte [Integration Runtime](concepts-integration-runtime.md).

## <a name="cluster-type"></a>Tipo de clúster

Hay tres opciones disponibles para la activación del tipo de clúster de Spark: uso general, optimizado para memoria y optimizado para proceso.

Los clústeres de **uso general** son la selección predeterminada y resultan la opción idónea en la mayoría de las cargas de trabajo de flujo de datos. Suelen ofrecer el mejor equilibrio entre rendimiento y costo.

Si el flujo de datos tiene muchas combinaciones y búsquedas, puede que desee usar un clúster **optimizado para memoria**. Los clústeres optimizados para memoria pueden almacenar más datos en memoria y reducirán los errores de memoria insuficiente que puedan aparecer. Son los más caros por núcleo, pero también tienden a generar canalizaciones más correctas. Si aparecen errores de memoria insuficiente al ejecutar los flujos de datos, cambie a una configuración de Azure IR optimizada para memoria. 

Los clústeres **optimizados para proceso** no son idóneos para flujos de trabajo de ETL y no se recomiendan para la mayoría de las cargas de trabajo de producción. En el caso de transformaciones de datos más sencillas, sin un uso intensivo de memoria, como el filtrado de datos o la incorporación de columnas derivadas, se pueden usar los clústeres optimizados para proceso, con un precio más barato por núcleo.

## <a name="cluster-size"></a>Tamaño del clúster

Los flujos de datos distribuyen el procesamiento de datos en diferentes nodos de un clúster de Spark para realizar operaciones en paralelo. Un clúster de Spark con más núcleos aumenta el número de nodos en el entorno de proceso. Más nodos aumentan la capacidad de procesamiento del flujo de datos. El aumento del tamaño del clúster suele ser una forma sencilla de reducir el tiempo de procesamiento.

El tamaño de clúster predeterminado es de cuatro nodos de controlador y cuatro nodos de trabajo.  A medida que se procesan más datos, se recomiendan clústeres más grandes. A continuación se muestran las opciones de tamaño posibles:

| Núcleos de trabajo | Núcleos de controlador | Núcleos totales | Notas |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | No disponible en la opción optimizada para proceso |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

El precio de los flujos de datos se calcula en horas de núcleo virtual, por lo que se tiene en cuenta tanto el tamaño del clúster como el tiempo de ejecución. A medida que escale verticalmente, aumentará el costo por minuto del clúster, pero se reducirá el tiempo total.

> [!TIP]
> Existe un punto máximo en el que el tamaño de un clúster deja de afectar al rendimiento de un flujo de datos. En función del tamaño de los datos, hay un punto en el que el aumento del tamaño de un clúster dejará de mejorar el rendimiento. Por ejemplo, si tiene más nodos que particiones de datos, agregar más nodos no ayudará más. Un procedimiento recomendado es comenzar con un tamaño pequeño y escalar verticalmente hasta satisfacer las necesidades de rendimiento. 

## <a name="time-to-live"></a>Período de vida

De forma predeterminada, cada actividad de flujo de datos pone en marcha un nuevo clúster de Spark en función de la configuración de Azure IR. El clúster tarda unos minutos en iniciar su actividad y el procesamiento de datos no puede iniciarse hasta que se complete. Si las canalizaciones contienen varios **flujos de datos** secuenciales, puede habilitar un valor de período de vida (TTL). Si se especifica un valor de período de vida, el clúster se mantiene activo durante un determinado período de tiempo después de que se complete la ejecución. Si un nuevo trabajo comienza a usar el IR durante el tiempo de TTL, volverá a usar el clúster existente y el tiempo de inicio de actividad se reducirá drásticamente. Una vez completado el segundo trabajo, el clúster permanecerá activo de nuevo durante el tiempo de TTL.

Además, puede minimizar el tiempo de inicio de los clústeres en caliente estableciendo la opción "Quick re-use" (Reutilización rápida) en Azure Integration Runtime en las propiedades del flujo de datos. Si se establece en true, el servicio no desmontará el clúster existente después de cada trabajo y, en su lugar, volverá a usarlo, principalmente manteniendo activo el entorno de proceso que ha establecido en Azure IR durante el período de tiempo especificado en el TTL. Esta opción permite el menor tiempo de inicio de las actividades de flujo de datos al ejecutarse desde una canalización.

Sin embargo, si la mayoría de los flujos de datos se ejecutan en paralelo, no se recomienda habilitar TTL para las instancia de IR que se usa para esas actividades. Solo se puede ejecutar un trabajo en un único clúster cada vez. Si hay un clúster disponible, pero se inician dos flujos de datos, solo uno usará el clúster activo. El segundo trabajo pondrá en marcha su propio clúster aislado.

> [!NOTE]
> El período de vida no está disponible cuando se usa el entorno de ejecución de integración de resolución automática.

## <a name="next-steps"></a>Pasos siguientes

Vea el resto de artículos sobre Data Flow relacionados con el rendimiento:

- [Rendimiento de flujo de datos](concepts-data-flow-performance.md)
- [Actividad Data Flow](control-flow-execute-data-flow-activity.md)
- [Supervisión del rendimiento de flujo de datos](concepts-data-flow-monitoring.md)
