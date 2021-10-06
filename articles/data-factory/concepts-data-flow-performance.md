---
title: Guía de optimización y rendimiento del flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Conozca los factores clave que afectan al rendimiento de los flujos de datos de asignación en las canalizaciones de Azure Data Factory y Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 7f12eb06d514ddc7d001012b0e3111c7603bdca9
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274052"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guía de optimización y rendimiento de la asignación de instancias de Data Flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Los flujos de datos de asignación de las canalizaciones de Azure Data Factory y Synapse proporcionan una interfaz sin código para diseñar y ejecutar transformaciones de datos a gran escala. Si no está familiarizado con los flujos de datos de asignación, consulte [Introducción a Mapping Data Flow](concepts-data-flow-overview.md). En este artículo se destacan varias maneras de ajustar y optimizar los flujos de datos para que cumplan con los parámetros deseados de rendimiento.

En el siguiente vídeo se muestran algunos intervalos de ejemplo que transforman datos con flujos de datos.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Supervisión del rendimiento de flujo de datos

Después de comprobar la lógica de transformación mediante el modo de depuración, puede ejecutar el flujo de datos de un extremo a otro como una actividad en una canalización. Los flujos de datos se convierten en operaciones de una canalización mediante la [actividad de ejecución de flujo de datos](control-flow-execute-data-flow-activity.md). La actividad de flujo de datos tiene una experiencia de supervisión única en comparación con otras actividades que muestra un plan de ejecución detallado y un perfil de rendimiento de la lógica de transformación. Para ver información detallada de la supervisión de un flujo de datos, haga clic en el icono de las gafas en la salida de la ejecución de actividad de una canalización. Para más información, consulte [Supervisión de los flujos de datos de asignación](concepts-data-flow-monitoring.md).

:::image type="content" source="media/data-flow/monitoring-details.png" alt-text="Supervisión de Data Flow":::

Cuando se supervisa el rendimiento del flujo de datos, hay cuatro posibles cuellos de botella que se deben considerar:

* Tiempo para el inicio de actividad del clúster
* Lectura desde un origen
* Tiempo de transformación
* Escritura en un receptor 

:::image type="content" source="media/data-flow/monitoring-performance.png" alt-text="Supervisión de Data Flow":::

El tiempo para el inicio de actividad del clúster es el tiempo que se tarda en poner en marcha un clúster de Apache Spark. Este valor se encuentra en la esquina superior derecha de la pantalla de supervisión. Los flujos de datos se ejecutan en un modelo Just-in-Time en el que cada trabajo usa un clúster aislado. Por lo general, el inicio de actividad tarda de tres a cinco minutos. En el caso de los trabajos secuenciales, este tiempo se puede reducir si se habilita un valor de período de vida. Para obtener más información, vea la sección **Período de vida** de [Rendimiento de Azure Integration Runtime](concepts-integration-runtime-performance.md#time-to-live).

Los flujos de datos usan un optimizador de Spark que reordena y ejecuta la lógica de negocios en "fases" para actuar lo más rápido posible. La salida de la supervisión muestra la duración de cada fase de transformación de cada uno de los receptores en los que escribe el flujo de datos, junto con el tiempo que se tarda en escribir los datos en el receptor. El mayor valor de tiempo es probablemente el cuello de botella del flujo de datos. Si la fase de transformación que más tarda contiene un origen, puede que le interese estudiar cómo mejorar la optimización del tiempo de lectura. Si una transformación tarda mucho tiempo, puede que tenga que volver a crear particiones o aumentar el tamaño del entorno de ejecución de integración. Si el tiempo de procesamiento del receptor es elevado, puede que necesite escalar verticalmente la base de datos o comprobar que no se está generando la salida en un único archivo.

Cuando haya identificado el cuello de botella del flujo de datos, use las siguientes estrategias de optimización para mejorar el rendimiento.

## <a name="testing-data-flow-logic"></a>Prueba de la lógica de flujo de datos

Cuando se diseñan y se prueban flujos de datos desde la interfaz de usuario, el modo de depuración permite realizar pruebas interactivas en un clúster de Spark activo. De este modo se puede obtener una vista previa de los datos y ejecutar los flujos de datos sin esperar a que se prepare un clúster. Para más información, consulte [Modo de depuración](concepts-data-flow-debug-mode.md).

## <a name="optimize-tab"></a>Pestaña Optimizar

La pestaña **Optimize** (Optimizar) contiene valores para configurar el esquema de partición del clúster de Spark. Esta pestaña existe en cada transformación de flujo de datos y especifica si se desea volver a crear particiones de los datos **después** de que se haya completado la transformación. Ajustar las particiones permite controlar la distribución de los datos entre los nodos de proceso y las optimizaciones de la localidad de los datos, lo que puede tener efectos tanto positivos como negativos en el rendimiento general de los flujos de datos.

:::image type="content" source="media/data-flow/optimize.png" alt-text="Captura de pantalla que muestra la pestaña Optimizar, que incluye la opción de partición, el tipo de partición y el número de particiones.":::

De manera predeterminada, se selecciona *Use current partitioning* (Usar particiones actuales), que indica al servicio que mantenga las particiones de salida actuales de la transformación. Dado que una nueva partición de datos lleva su tiempo, en la mayoría de los escenarios se recomienda la opción *Use current partitioning* (Usar particiones actuales). Entre los escenarios en los que es posible que desee volver a crear particiones de los datos se incluyen los que surgen tras agregados y combinaciones que sesgan significativamente los datos o cuando se usa la creación de particiones de origen en una base de datos SQL.

Para cambiar las particiones de cualquier transformación, seleccione la pestaña **Optimizar** y elija el botón de radio **Set Partitioning** (Establecer particiones). Aparecen una serie de opciones relacionadas con la creación de particiones. El mejor método para crear particiones depende de los volúmenes de datos, las claves candidatas, los valores nulos y la cardinalidad. 

> [!IMPORTANT]
> La opción Single partition (Partición única) combina todos los datos distribuidos en una sola partición. Se trata de una operación muy lenta que también afecta significativamente a todas las operaciones de transformación y escritura de nivel inferior. No es nada recomendable usar esta opción, a menos que haya una razón empresarial explícita para ello.

Las siguientes opciones de creación de particiones están disponibles en todas las transformaciones:

### <a name="round-robin"></a>Round robin 

Round robin distribuye los datos equitativamente entre las particiones. Use round robin cuando los candidatos principales no sean lo suficientemente buenos como para implementar una estrategia de creación de particiones sólida e inteligente. Puede establecer el número de particiones físicas.

### <a name="hash"></a>Hash

El servicio genera un hash de columnas a fin de crear particiones uniformes de tal modo que, por ejemplo, las filas con valores similares estén en la misma partición. Si usa la opción Hash, pruebe si hay una posible distorsión de las particiones. Puede establecer el número de particiones físicas.

### <a name="dynamic-range"></a>Intervalo dinámico

El intervalo dinámico usa los intervalos dinámicos de Spark en función de las columnas o expresiones que proporcione. Puede establecer el número de particiones físicas. 

### <a name="fixed-range"></a>Intervalo fijo

Cree una expresión que proporcione un intervalo de valores fijo en las columnas de datos con particiones. Para evitar la distorsión de la partición, es preciso que conozca perfectamente los datos antes de usar esta opción. Los valores que especifique para la expresión se usan como parte de una función de partición. Puede establecer el número de particiones físicas.

### <a name="key"></a>Clave

Si conoce bien la cardinalidad de los datos, la creación de particiones clave puede ser una buena estrategia. La creación de particiones clave crea particiones para cada valor único de la columna. No puede establecer el número de particiones porque dicho número depende de valores únicos de los datos.

> [!TIP]
> Al establecer manualmente el esquema de partición, los datos se reordenan y se pueden perder las ventajas del optimizador de Spark. Como procedimiento recomendado, conviene no establecer manualmente las particiones a menos que sea necesario hacerlo.

## <a name="logging-level"></a>Nivel de registro

Si no es necesario que cada ejecución de canalización de las actividades de flujo de datos anote completamente todos los registros de telemetría detallados, tiene la opción de establecer el nivel de registro en "básico" o "ninguno". Al ejecutar los flujos de datos en modo "detallado" (valor predeterminado), está solicitando al servicio que registre la actividad por completo en cada nivel de partición individual durante la transformación de los datos. Esta puede ser una operación costosa; por tanto, habilitar solo el modo detallado al solucionar problemas puede mejorar el flujo de datos y el rendimiento de la canalización en general. El modo "básico" solo registrará las duraciones de las transformaciones, mientras que "ninguno" solo proporcionará un resumen de las duraciones.

:::image type="content" source="media/data-flow/logging.png" alt-text="Nivel de registro":::

## <a name="next-steps"></a>Pasos siguientes

- [Optimización de orígenes](concepts-data-flow-performance-sources.md)
- [Optimización de receptores](concepts-data-flow-performance-sinks.md)
- [Optimización de transformaciones](concepts-data-flow-performance-transformations.md)
- [Uso de flujos de datos en canalizaciones](concepts-data-flow-performance-pipelines.md)

Vea el resto de artículos sobre Data Flow relacionados con el rendimiento:

- [Actividad Data Flow](control-flow-execute-data-flow-activity.md)
- [Supervisión del rendimiento de flujo de datos](concepts-data-flow-monitoring.md)
- [Rendimiento de Microsoft Integration Runtime](concepts-integration-runtime-performance.md)
