---
title: Optimización del rendimiento para los clústeres de Apache Kafka HDInsight
description: Proporciona una introducción a las técnicas para optimizar las cargas de trabajo de Apache Kafka en Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: b5c149499fe21940e58c795dbed2407c046ac43d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044428"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optimización del rendimiento para los clústeres de Apache Kafka HDInsight

En este artículo se proporcionan algunas sugerencias para optimizar el rendimiento de las cargas de trabajo de Apache Kafka en HDInsight. El enfoque es sobre el ajuste de la configuración del productor y agente. Hay diferentes maneras de medir el rendimiento, y las optimizaciones que aplique dependerá de sus necesidades empresariales.

## <a name="architecture-overview"></a>Introducción a la arquitectura

Los temas de Kafka se usan para organizar los registros. Los registros los generan productores y los consumen consumidores. Los productores envían registros a los agentes de Kafka, que luego almacenan los datos. Cada nodo de trabajo del clúster de HDInsight es un agente de Kafka.

Los temas particionan los registros entres los agentes. Al consumir registros, puede usar hasta un consumidor por partición para lograr el procesamiento en paralelo de los datos.

La replicación se usa para duplicar las particiones entre nodos. Esto protege contra las desconexiones de nodos (agente). Una sola partición entre el grupo de réplicas se designa como el líder de particiones. El tráfico del productor se enruta al líder de cada nodo, con el estado administrado por ZooKeeper.

## <a name="identify-your-scenario"></a>Identificación del escenario

El rendimiento general de Apache Kafka tiene dos aspectos principales: rendimiento y latencia. El rendimiento es la velocidad máxima a la que se pueden procesar los datos. Cuando más alto sea el rendimiento, mejor. La latencia es el tiempo que se necesita para que los datos que se almacena o recuperen. Cuando más baja sea la latencia, mejor. Encontrar el equilibrio correcto entre el rendimiento, latencia y coste de la infraestructura de la aplicación puede ser algo desafiante. Los requisitos de rendimiento probablemente coincidirán con una de las siguientes tres situaciones comunes, en función de si requiere alto rendimiento, baja latencia o ambos:

* Alto rendimiento, baja latencia. Este escenario requiere un alto rendimiento y baja latencia (~ 100 milisegundos). Un ejemplo de este tipo de aplicación es la supervisión de la disponibilidad del servicio.
* Alto rendimiento, alta latencia. Este escenario requiere un alto rendimiento (~1,5 GBps), pero puede tolerar una latencia mayor (< 250 ms). Un ejemplo de este tipo de aplicación es la ingesta de datos de telemetría para procesos casi en tiempo real, como aplicaciones de seguridad y detección de intrusiones.
* Bajo rendimiento, baja latencia. Este escenario requiere una latencia baja (< 10 ms) para el procesamiento en tiempo real, pero puede tolerar un menor rendimiento. Un ejemplo de este tipo de aplicación es las comprobaciones de ortografía y gramática en línea.

## <a name="producer-configurations"></a>Configuraciones de productor

En las secciones siguientes se resaltan algunas de las propiedades de configuración más importantes para optimizar el rendimiento de los productores de Kafka. Para obtener una explicación detallada de todas las propiedades de configuración, consulte la [documentación de Apache Kafka sobre las configuraciones de productor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Tamaño de lote

Los productores de Apache Kafka reúnen grupos de mensajes (denominados lotes) que se envían como una unidad que se almacenará en una partición de almacenamiento único. El tamaño de lote corresponde al número de bytes que deben estar presentes antes de que se transmita ese grupo. Aumentar el parámetro `batch.size` puede aumentar el rendimiento, ya que reduce la sobrecarga de procesamiento de red y las solicitudes de E/S. Bajo una carga ligera, un mayor tamaño de lote puede aumentar la latencia de envío de Kafka mientras el productor espera a que un lote esté preparado. Bajo una carga elevada, se recomienda aumentar el tamaño de lote para mejorar el rendimiento y la latencia.

### <a name="producer-required-acknowledgments"></a>Confirmaciones requeridas por el productor

La configuración `acks` requerida por el productor determina el número de confirmaciones que necesita el líder de particiones antes de que una solicitud de escritura se considere completada. Este valor afecta a la confiabilidad de los datos y toma los valores de `0`, `1`, o `-1`. Un valor de `-1` significa que se debe recibir una confirmación de todas las réplicas antes de que se complete la operación de escritura. El valor `acks = -1` proporciona mayores garantías contra la pérdida de datos, pero también resulta en mayor latencia y menor rendimiento. Si los requisitos de la aplicación exigen un mayor rendimiento, pruebe a establecer `acks = 0` o `acks = 1`. Tenga en cuenta que no reconocer todas las réplicas puede reducir la confiabilidad de los datos.

### <a name="compression"></a>Compresión

Un productor de Kafka puede configurarse para comprimir los mensajes antes de enviarlos a los agentes. El valor `compression.type` especifica el códec de compresión que se usará. Los códecs de compresión admitidos son "gzip", "snappy" y "lz4". La compresión es beneficiosa y se debe tener en cuenta si hay una limitación en la capacidad de disco.

Entre los dos códecs de compresión que se usan con frecuencia, `gzip` y `snappy`, `gzip` tiene una razón de compresión, lo que resulta en un menor uso de disco a costa de una carga más elevada de la CPU. El códec `snappy` proporciona menos compresión con menos sobrecarga de la CPU. Puede decidir qué códec usar en función de las limitaciones de CPU del disco de agente o productor. `gzip` puede comprimir los datos a una velocidad cinco veces mayor que `snappy`.

El uso de la compresión de datos aumentará el número de registros que se pueden almacenar en un disco. También puede aumentar la sobrecarga de la CPU en los casos en los que hay una discrepancia entre los formatos de compresión que usan el productor y el agente, ya que los datos se deben comprimir antes del envío y luego descomprimir antes del procesamiento.

## <a name="broker-settings"></a>Configuración del agente

En las secciones siguientes se resaltan algunas de las opciones de configuración más importantes para optimizar el rendimiento de los agentes de Kafka. Para obtener una explicación detallada de todos los valores del agente, consulte la [documentación de Apache Kafka sobre las configuraciones de agente](https://kafka.apache.org/documentation/#brokerconfigs).

### <a name="number-of-disks"></a>Número de discos

Los discos de almacenamiento tienen un valor de IOPS (operaciones de entrada/salida por segundo) y de bytes de lectura/escritura por segundo limitado. Al crear nuevas particiones, Kafka almacena cada nueva partición en el disco con el menor número de particiones existentes para equilibrarlas entre todos los discos disponibles. A pesar de la estrategia de almacenamiento, al procesar cientos de réplicas de particiones en cada disco, Kafka puede saturar fácilmente el rendimiento de disco disponible. Aquí el término medio es entre el rendimiento y el coste. Si la aplicación requiere mayor rendimiento, cree un clúster con más discos administrados por agente. Actualmente, HDInsight no admite la adición de discos administrados a un clúster en ejecución. Para más información sobre cómo configurar el número de discos administrados, consulte [Configuración del almacenamiento y la escalabilidad de Apache Kafka en HDInsight](apache-kafka-scalability.md). Debe comprender las implicaciones de coste de aumentar el espacio de almacenamiento para los nodos del clúster.

### <a name="number-of-topics-and-partitions"></a>Número de temas y particiones

Los productores de Kafka escriben en los temas. Los consumidores de Kafka leen de los temas. Un tema está asociado con un registro, que es una estructura de datos en el disco. Kafka anexa los registros de un productor al final de un registro de tema. Un registro de tema consta varias particiones distribuidas entre varios archivos. A su vez, estos archivos se distribuyen entre varios nodos de clúster de Kafka. Los consumidores leen los temas de Kafka a su ritmo y pueden elegir su posición (desplazamiento) en el registro de tema.

Cada partición de Kafka es un archivo de registro en el sistema y los subprocesos de productor pueden escribir en varios registros de manera simultánea. De forma similar, dado que cada subproceso de consumidor lee mensajes de una partición, el consumo desde varias particiones también se controla en paralelo.

Aumentar la densidad de la partición (el número de particiones por agente) agrega una sobrecarga relacionada con las operaciones de metadatos y solicitud/respuesta por partición entre el líder de particiones y sus seguidores. Incluso si no hay datos que fluyen, las réplicas de partición aún capturar datos de los líderes, lo que resulta en un procesamiento adicional para enviar y recibir solicitudes a través de la red.

Para clústeres 1.1 de Apache Kafka y superiores en HDInsight, recomendamos tener un máximo de 1000 particiones por agente, incluidas las réplicas. Aumentar el número de particiones por agente reduce el rendimiento y también puede provocar la falta de disponibilidad de temas. Para más información sobre la compatibilidad con particiones de Kafka, consulte la [entrada de blog oficial de Apache Kafka sobre el aumento del número de particiones compatibles en la versión 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Para más información sobre cómo modificar temas, consulte [Apache Kafka: modifying topics](https://kafka.apache.org/documentation/#basic_ops_modify_topic) (Apache Kafka: modificar temas).

### <a name="number-of-replicas"></a>Número de réplicas

Un mayor factor de replicación resulta en solicitudes adicionales entre el líder de particiones y los seguidores. Como consecuencia, un factor de replicación más alto consume más recursos de disco y CPU para controlar las solicitudes adicionales, lo que aumenta la latencia de escritura y reduce el rendimiento.

Recomendamos usar un factor de replicación de al menos 3x para Kafka en Azure HDInsight. La mayoría de las regiones de Azure tienen tres dominios de error, pero en regiones con solo dos dominios de error, los usuarios deben usar un factor de replicación de 4x.

Para más información sobre la replicación, consulte [Apache Kafka: replication](https://kafka.apache.org/documentation/#replication) (Apache Kafka: replicación) y [Apache Kafka: increasing replication factor](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor) (Apache Kafka: aumentar el factor de replicación).

## <a name="next-steps"></a>Pasos siguientes

* [Procesamiento de billones de eventos al día con Apache Kafka en Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Qué es Apache Kafka en HDInsight](apache-kafka-introduction.md)
