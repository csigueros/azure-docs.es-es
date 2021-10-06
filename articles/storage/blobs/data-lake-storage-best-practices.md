---
title: Procedimientos recomendados para el uso de Azure Data Lake Storage Gen2 | Microsoft Docs
description: Conozca los procedimientos recomendados de ingesta y seguridad de datos y de rendimiento relacionados con el uso de Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: b2d631d165b4a8cd585fbe012d9ab9afaa2e6bf3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555439"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Procedimientos recomendados para usar Azure Data Lake Storage Gen2

En este artículo se presentan procedimientos recomendados sobre seguridad, resistencia y estructura de directorios en una cuenta de almacenamiento compatible con Data Lake Storage Gen2. Para conocer procedimientos recomendados sobre la optimización del rendimiento, consulte [Optimización de Azure Data Lake Storage Gen2 para aumentar el rendimiento](data-lake-storage-performance-tuning-guidance.md).

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

### <a name="use-security-groups-instead-of-individual-users"></a>Uso de grupos de seguridad en lugar de usuarios individuales

Al aplicar listas de control de acceso (ACL), use siempre grupos de seguridad de Azure AD como entidad de seguridad asignada en una entrada de ACL. Ofrece la oportunidad de asignar directamente usuarios individuales o entidades de servicio. Con esta estructura, podrá agregar y quitar usuarios o entidades de servicio sin necesidad de volver a aplicar las ACL en una estructura de directorios completa. En su lugar, puede agregar o quitar usuarios y entidades de servicio del grupo de seguridad de Azure AD adecuado.

Para obtener más información sobre cómo aplicar este procedimiento recomendado, consulte [Grupos de seguridad](data-lake-storage-access-control-model.md#security-groups).

Para obtener información general sobre el modelo de control de acceso de Data Lake Storage Gen2, consulte [Modelo de control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

### <a name="configure-the-azure-storage-firewall-with-azure-service-access"></a>Configuración del firewall de Azure Storage con acceso al servicio de Azure

Active el firewall de Azure Storage para limitar el vector de ataques externos. Para acceder a su cuenta de almacenamiento desde un servicio como [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), implemente una instancia de dicho servicio en su red virtual. A continuación, puede configurar el firewall para conceder al servicio acceso a la cuenta de almacenamiento.

Para obtener más información sobre cómo aplicar este procedimiento recomendado, consulte [Concesión de acceso a servicios de Azure de confianza](../common/storage-network-security.md).

Para conocer recomendaciones de seguridad generales, consulte [Recomendaciones de seguridad para Blob Storage](security-recommendations.md).

## <a name="resiliency-considerations"></a>Consideraciones de resistencia

Al diseñar un sistema con Data Lake Storage Gen2 o cualquier otro servicio en la nube, tenga en cuenta los requisitos de disponibilidad y cómo responder a posibles interrupciones del servicio. Un problema puede estar localizado en una instancia concreta o abarcar a toda una región. Por ello es importante disponer de un plan para ambos escenarios. Según lo estipulado en el contrato de nivel de servicio en relación con el objetivo de tiempo de recuperación y el objetivo de punto de recuperación de su carga de trabajo, puede elegir una estrategia más o menos exigente de alta disponibilidad y recuperación ante desastres.

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidad y recuperación ante desastres

Data Lake Storage Gen2 ya controla la replicación 3x para proteger frente a errores de hardware localizados. Otras opciones de replicación, como el almacenamiento con redundancia de zona (ZRS) o el almacenamiento con redundancia de zona geográfica (GZRS), mejoran la disponibilidad. Con estas funcionalidades, las cargas de trabajo pueden responder a una interrupción del servicio cambiando a una instancia replicada por separado, localmente o en una nueva región.

Con el fin de prepararse para un error catastrófico de una región, asegúrese de replicar datos en una región diferente usando opciones de replicación, como el almacenamiento con redundancia geográfica (GRS) y el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS). También debe tener en cuenta sus requisitos en casos extremos, por ejemplo, datos dañados donde quiere crear instantáneas periódicas a las que revertir. Según la importancia y el tamaño de los datos, considere la posibilidad de disponer de instantáneas delta graduales de períodos de 1, 6 y 24 horas, según las tolerancias al riesgo.

Además de elegir un modelo de replicación adecuado, estudie posibles formas de ayudar a los clientes que se conectan a realizar automáticamente la conmutación por error a la región secundaria mediante la supervisión de desencadenadores o la longitud de los intentos con error, o bien al menos a enviar una notificación a los administradores para que intervengan manualmente. Tenga en cuenta que también hay inconvenientes por realizar una conmutación por error en lugar de esperar a que un servicio vuelva a estar en línea.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Uso de Distcp para el traslado de datos entre dos ubicaciones

DistCp es la forma abreviada (en inglés) de copia distribuida, una herramienta de línea de comandos de Linux que se incluye con Hadoop y que permite el movimiento de datos distribuidos entre dos ubicaciones. Las dos ubicaciones pueden ser Data Lake Storage Gen2, Hadoop File System (HDFS) o S3. Esta herramienta usa trabajos de MapReduce en un clúster de Hadoop (por ejemplo, HDInsight) para realizar un escalado horizontal en todos los nodos. Distcp se considera una de las formas más rápidas de trasladar macrodatos sin necesidad de dispositivos de compresión de red especiales. Distcp también proporciona una opción para actualizar solo los valores delta entre dos ubicaciones y controla los reintentos automáticos así como el escalado dinámico de los procesos. Este enfoque es increíblemente eficaz cuando se trata de replicar elementos tales como tablas de Hive o Spark, que pueden incluir muchos archivos grandes en un único directorio, y usted solo desea copiar los datos modificados. Por estos motivos, Distcp es la herramienta más recomendada para copiar datos entre almacenes de macrodatos.

Los flujos de trabajo de Apache Oozie pueden desencadenar trabajos de copia mediante desencadenadores de frecuencia o de datos, así como trabajos de cron de Linux. En el caso de trabajos de replicación intensivos, se recomienda poner en marcha un clúster de HDInsight Hadoop independiente que se pueda optimizar y escalar específicamente para los trabajos de copia. Esto garantiza que los trabajos de copia no interferirán con trabajos críticos. Si se ejecuta la replicación en una frecuencia lo bastante amplia, se puede incluso desconectar el clúster entre cada trabajo. Si se realiza la conmutación por error a una región secundaria, asegúrese de que también se pone en marcha otro clúster en la región secundaria para replicar los nuevos datos otra vez en la cuenta principal de Data Lake Storage Gen2 una vez que se vuelva a conectar. Para obtener ejemplos del uso de Distcp, vea [Uso de Distcp para copiar datos entre Azure Storage Blob y Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Uso de Azure Data Factory para programar trabajos de copia

[Azure Data Factory](../../data-factory/introduction.md) también puede utilizarse para programar trabajos de copia mediante una actividad de copia e incluso configurarse con una frecuencia para esta actividad mediante el Asistente para copia. Tenga en cuenta que Azure Data Factory tiene un límite de unidades de movimiento de datos en la nube (DMU) que, en última instancia, limita el rendimiento y los procesos para cargas de trabajo con datos grandes. Además, en la actualidad Azure Data Factory no ofrece la actualización de valores delta entre cuentas de Data Lake Storage Gen2, por lo que directorios como las tablas de Hive necesitarán una copia completa para su replicación. Consulte el [artículo sobre factoría de datos](../../data-factory/load-azure-data-lake-storage-gen2.md) para obtener más información sobre cómo copiar con Data Factory.

## <a name="monitoring-considerations"></a>Consideraciones sobre supervisión

Data Lake Storage Gen2 proporciona métricas en Azure Portal en la cuenta de Data Lake Storage Gen2 y en Azure Monitor. La disponibilidad de Data Lake Storage Gen2 se muestra en Azure Portal. Para obtener la disponibilidad más actualizada de una cuenta de Data Lake Storage Gen2, deberá ejecutar sus propias pruebas sintéticas para validar la disponibilidad. Otras métricas como el uso de almacenamiento total, las solicitudes de escritura/lectura y las entradas/salidas están disponibles para que las usen las aplicaciones de supervisión y también pueden desencadenar alertas cuando se superan los umbrales (por ejemplo, la latencia promedio o el número de errores por minuto).

## <a name="directory-layout-considerations"></a>Consideraciones sobre el diseño del directorio

Al ingerir datos, es importante planificar previamente la estructura de los datos para que se puedan emplear eficazmente la seguridad, la creación de particiones y el procesamiento. Muchas de las recomendaciones siguientes son aplicables a todas las cargas de trabajo de macrodatos. Aunque cada carga de trabajo tiene distintos requisitos con respecto al consumo de los datos, a continuación se muestran algunos diseños habituales que deben tenerse en cuenta al trabajar con escenarios de IoT y lotes.

### <a name="iot-structure"></a>Estructura de IoT

En las cargas de trabajo de IoT, pueden ingerirse una gran cantidad de datos que abarquen numerosos productos, dispositivos, organizaciones y clientes. Es importante planificar previamente el diseño de directorios con el fin de garantizar la organización, la seguridad y un procesamiento eficaz de los datos para los consumidores de nivel inferior. Una plantilla general a tener en cuenta podría tener el siguiente diseño:

*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*

Por ejemplo, la telemetría de aterrizaje de un motor de un avión del Reino Unido podría ser parecida a la estructura siguiente:

*UK/Planes/BA1293/Engine1/2017/08/11/12/*

En este ejemplo, al colocar la fecha al final de la estructura de directorios, puede usar ACL para proteger más fácilmente regiones y asuntos para usuarios y grupos específicos. Si coloca la estructura de datos al principio, resultaría mucho más difícil proteger estas regiones y estos asuntos. Por ejemplo, si quisiera proporcionar acceso solo a los datos del Reino Unido o a determinados planos, tendría que aplicar un permiso independiente para numerosos directorios en cada directorio de hora. Esta estructura también aumentaría exponencialmente el número de directorios con el paso del tiempo.

### <a name="batch-jobs-structure"></a>Estructura de trabajos por lotes

Un enfoque que se usa habitualmente en el procesamiento por lotes es colocar los datos en un directorio "in". Posteriormente, una vez procesados los datos, coloque los nuevos datos en un directorio "out" para que los consuman los procesos de nivel inferior. Esta estructura de directorios se usa a veces con trabajos que requieren el procesamiento de archivos individuales, pero puede que no requieran un procesamiento paralelo masivo con grandes conjuntos de datos. Al igual que en la estructura de IoT que se recomienda anteriormente, una estructura adecuada cuenta con directorios de nivel primario para elementos como regiones o asuntos (por ejemplo, organización, producto o productor). Considere la posibilidad de usar la fecha y la hora en la estructura para permitir una mejor organización, búsquedas filtradas, seguridad y automatización del procesamiento. El nivel de granularidad de la estructura de fecha viene determinado por el intervalo en el que los datos se cargan o procesan como, por ejemplo, cada hora, cada día o incluso mensualmente.

En algunas ocasiones, el procesamiento de archivos es incorrecto debido a datos dañados o a formatos imprevistos. En tales casos, podría resultar útil que la estructura de directorios tuviera una carpeta **/bad** a la que mover los archivos para inspeccionarlos más a fondo. El trabajo por lotes también puede controlar el informe o notificación de estos archivos *incorrectos* para una posterior intervención manual. Tenga en cuenta la siguiente estructura de plantilla:

*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*

Por ejemplo, una empresa de marketing recibe a diario extractos de datos de actualizaciones de los clientes de Norteamérica. Podría tener el aspecto del siguiente fragmento de código antes y después del procesamiento:

*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\
*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*

En el caso habitual de procesamiento de datos por lotes directamente en bases de datos como Hive o instancias tradicionales de SQL Database, no se necesitan directorios **/in** o **/out**, ya que la salida ya va a una carpeta independiente de la tabla de Hive o la base de datos externa. Por ejemplo, los extractos diarios de los clientes llegarían a sus respectivos directorios. A continuación, un servicio como [Azure Data Factory](../../data-factory/introduction.md), [Apache Oozie](https://oozie.apache.org/)o [Apache Airflow](https://airflow.apache.org/) desencadenaría un trabajo diario de Hive o Spark para procesar y escribir los datos en una tabla de Hive.

## <a name="see-also"></a>Consulte también

- [Modelo de control de acceso de Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
- [Optimizar Azure Data Lake Storage Gen2 para el rendimiento](data-lake-storage-performance-tuning-guidance.md)
- [Guía del usuario de Data Lake](https://github.com/rukmani-msft/adlsguidancedoc/blob/master/Hitchhikers_Guide_to_the_Datalake.md)
- [Introducción a Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
