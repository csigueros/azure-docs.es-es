---
title: Procedimientos recomendados para el uso de Azure Data Lake Storage Gen2 | Microsoft Docs
description: Más información sobre los procedimientos recomendados de ingesta de datos, seguridad de datos y rendimiento relacionados con el uso de Azure Data Lake Storage Gen2 (antes conocido como Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: cb68621017649c87849294be67bc2e12d96214e8
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732250"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Procedimientos recomendados para usar Azure Data Lake Storage Gen2

En este artículo obtendrá más información sobre los procedimientos recomendados y las consideraciones para trabajar con Azure Data Lake Storage Gen2. En este artículo se proporciona información sobre seguridad, rendimiento, resistencia y supervisión de Data Lake Storage Gen2. Antes de Data Lake Storage Gen2, el trabajo con macrodatos en servicios como Azure HDInsight era complicado. Había que particionar los datos en varias cuentas de almacenamiento de blobs para que se pudiera lograr un almacenamiento de petabytes y un rendimiento óptimo a esa escala. Data Lake Storage Gen2 admite tamaños de archivo individuales de hasta 190,7 TiB y la mayoría de los límites estrictos de rendimiento se han eliminado. Pero aún quedan algunas consideraciones que se describen en este artículo para que pueda obtener el mejor rendimiento con Data Lake Storage Gen2.

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Azure Data Lake Storage Gen2 ofrece controles de acceso de POSIX de usuarios, grupos y entidades de servicio de Azure Active Directory (Azure AD). Estos controles de acceso se pueden establecer para archivos y directorios ya existentes. Los controles de acceso también se pueden utilizar para crear permisos predeterminados que se pueden aplicar automáticamente a archivos o directorios nuevos. En [Control de acceso en Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md) encontrará más información sobre las listas de control de acceso de Data Lake Storage Gen2.

### <a name="use-security-groups-versus-individual-users"></a>Uso de grupos de seguridad frente a usuarios individuales

Cuando se trabaja con macrodatos en Data Lake Storage Gen2, lo más probable es que se use una entidad de servicio para permitir que servicios como Azure HDInsight puedan trabajar con los datos. Sin embargo, puede haber casos en que los usuarios individuales también necesiten acceder a los datos. En cualquier caso, es muy recomendable usar [grupos de seguridad](../common/authorize-data-access.md) de Azure Active Directory en lugar de asignar usuarios individuales a los directorios y archivos.

Una vez que se asignan permisos a un grupo de seguridad, agregar o quitar usuarios del grupo no requiere ninguna actualización de Data Lake Storage Gen2. Esto también ayuda a garantizar que no se supere el número máximo de entradas de control de acceso por cada lista de control de acceso (ACL). Actualmente, dicho número es 32 (incluidas las cuatro ACL de estilo POSIX que siempre están asociadas a cada archivo y directorio): el usuario propietario, el grupo propietario, la máscara y otros. Cada directorio puede tener dos tipos de ACL, la ACL de acceso y la ACL predeterminada, para un total de 64 entradas de control de acceso. Para más información sobre estas ACL, vea [Control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Seguridad de los grupos

Cuando usted o sus usuarios necesitan tener acceso a los datos en una cuenta de almacenamiento con el espacio de nombres jerárquico habilitado, es mejor usar grupos de seguridad de Azure Active Directory. Algunos grupos recomendados con los que empezar pueden ser **ReadOnlyUsers**, **WriteAccessUsers** y **FullAccessUsers** para la raíz del contenedor e incluso unos distintos para los subdirectorios principales. Si prevé que habrá más grupos que se tendrán que agregar posteriormente, pero aún no se han identificado, puede considerar la opción de crear grupos de seguridad ficticios que tengan acceso a determinadas carpetas. El uso de grupos de seguridad garantiza que se pueda evitar un tiempo de procesamiento largo al asignar nuevos permisos a miles de archivos.

### <a name="security-for-service-principals"></a>Seguridad de las entidades de servicio

Servicios como Azure Databricks suelen usar entidades de servicio de Azure Active Directory para acceder a datos de Data Lake Storage Gen2. Para muchos clientes, es posible que una única entidad de servicio de Azure Active Directory sea suficiente, y esta puede contener permisos completos en la raíz del contenedor de Data Lake Storage Gen2. Puede que otros clientes requieran varios clústeres con diferentes entidades de servicio donde uno de los clústeres tenga acceso total a los datos y otro tenga solo acceso de lectura. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Habilitación del firewall de Data Lake Storage Gen2 para el acceso de servicios de Azure

Data Lake Storage Gen2 admite la opción de activar un firewall y limitar el acceso solo a los servicios de Azure, lo que se recomienda para reducir los vectores de ataques externos. El firewall se puede habilitar en la cuenta de almacenamiento en Azure Portal con las opciones **Firewall** > **Habilitar firewall (ON)**  > **Allow access to Azure services** (Permitir acceso a servicios de Azure).

Para acceder a la cuenta de almacenamiento desde Azure Databricks, implemente Azure Databricks en la red virtual y, a continuación, agregue esa red virtual al firewall. Consulte [Configuración de redes virtuales y firewalls de Azure Storage](../common/storage-network-security.md).

## <a name="resiliency-considerations"></a>Consideraciones de resistencia

Al diseñar un sistema con Data Lake Storage Gen2 o cualquier otro servicio en la nube, debe tener en cuenta los requisitos de disponibilidad y cómo responder a posibles interrupciones del servicio. Un problema puede estar localizado en una instancia concreta o abarcar a toda una región. Por ello es importante disponer de un plan para ambos escenarios. Según lo estipulado en el Acuerdo de Nivel de Servicio en relación con el objetivo de tiempo de recuperación y el objetivo de punto de recuperación de la carga de trabajo, puede elegir una estrategia más o menos exigente para proporcionar alta disponibilidad y recuperación ante desastres.

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidad y recuperación ante desastres

La alta disponibilidad (HA) y la recuperación ante desastres (DR) se pueden combinar en algunas ocasiones, aunque cada una de ellas sigue una estrategia ligeramente diferente en lo que respecta a los datos. Data Lake Storage Gen2 ya controla la replicación 3x en segundo plano para proteger frente a los errores de hardware localizados. Además, otras opciones de replicación, como ZRS o GZRS, mejoran la alta disponibilidad, mientras que GRS y RA-GRS mejoran la DR. Cuando se crea un plan de alta disponibilidad, si se produce una interrupción del servicio, la carga de trabajo necesita acceder a los datos más recientes lo más rápidamente posible mediante el traslado a una instancia replicada de manera independiente localmente o en una nueva región.

En una estrategia de recuperación ante desastres, para estar preparados para el improbable caso de un error grave en una región, también es importante tener los datos replicados en una región diferente con la replicación de almacenamiento con redundancia geográfica y almacenamiento con redundancia geográfica con acceso de lectura. También debe tener en cuenta los requisitos en casos extremos como, por ejemplo, datos dañados donde quiere crear instantáneas periódicas a las que revertir. Según la importancia y el tamaño de los datos, considere la posibilidad de disponer de instantáneas delta graduales de períodos de 1, 6 y 24 horas, según las tolerancias al riesgo.

Para mejorar la resistencia de datos con Data Lake Storage Gen2, se recomienda realizar una replicación geográfica de los datos mediante el almacenamiento con redundancia geográfica o el almacenamiento con redundancia geográfica con acceso de lectura que satisfaga los requisitos de alta disponibilidad y recuperación ante desastres. Además, debe proporcionar medios para que la aplicación que usa Data Lake Storage Gen2 realice automáticamente la conmutación por error en la región secundaria mediante la supervisión de desencadenadores o la longitud de los intentos erróneos, o bien que al menos envíe una notificación a los administradores para que intervengan de forma manual. Tenga en cuenta que también hay inconvenientes por realizar una conmutación por error en lugar de esperar a que un servicio vuelva a estar en línea.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Uso de Distcp para el traslado de datos entre dos ubicaciones

DistCp es la forma abreviada (en inglés) de copia distribuida, una herramienta de línea de comandos de Linux que se incluye con Hadoop y que permite el movimiento de datos distribuidos entre dos ubicaciones. Las dos ubicaciones pueden ser Data Lake Storage Gen2, HDFS o S3. Esta herramienta usa trabajos de MapReduce en un clúster de Hadoop (por ejemplo, HDInsight) para realizar un escalado horizontal en todos los nodos. Distcp se considera la forma más rápida de trasladar macrodatos sin necesidad de dispositivos de compresión de red especiales. Distcp también proporciona una opción para actualizar solo los valores delta entre dos ubicaciones y controla los reintentos automáticos así como el escalado dinámico de los procesos. Este enfoque es increíblemente eficaz cuando se trata de replicar elementos tales como tablas de Hive o Spark, que pueden incluir muchos archivos grandes en un único directorio, y solo desea copiar los datos modificados. Por estos motivos, Distcp es la herramienta más recomendada para copiar datos entre almacenes de macrodatos.

Los flujos de trabajo de Apache Oozie pueden desencadenar trabajos de copia mediante desencadenadores de frecuencia o de datos, así como trabajos de cron de Linux. Para trabajos de replicación intensivos, se recomienda poner en marcha un clúster de HDInsight Hadoop independiente que se pueda optimizar y escalar específicamente para los trabajos de copia. Esto garantiza que los trabajos de copia no interferirán con trabajos críticos. Si se ejecuta la replicación en una frecuencia lo bastante amplia, se puede incluso desconectar el clúster entre cada trabajo. Si se realiza la conmutación por error a una región secundaria, asegúrese de que también se pone en marcha otro clúster en la región secundaria para replicar los nuevos datos otra vez en la cuenta principal de Data Lake Storage Gen2 una vez que se vuelva a conectar. Para obtener ejemplos del uso de Distcp, vea [Uso de Distcp para copiar datos entre Azure Storage Blob y Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Uso de Azure Data Factory para programar trabajos de copia

Azure Data Factory también se puede utilizar para programar trabajos de copia mediante una actividad de copia e incluso se puede configurar una frecuencia para esta actividad mediante el Asistente para copia. Tenga en cuenta que Azure Data Factory tiene un límite de unidades de movimiento de datos en la nube (DMU) que, en última instancia, limita el rendimiento y los procesos para cargas de trabajo con datos grandes. Además, en la actualidad Azure Data Factory no ofrece la actualización de valores delta entre cuentas de Data Lake Storage Gen2, por lo que directorios como las tablas de Hive necesitarán una copia completa para su replicación. Consulte el [artículo sobre factoría de datos](../../data-factory/load-azure-data-lake-storage-gen2.md) para obtener más información sobre cómo copiar con Data Factory.

## <a name="monitoring-considerations"></a>Consideraciones sobre supervisión

Data Lake Storage Gen2 proporciona métricas en Azure Portal en la cuenta de Data Lake Storage Gen2 y en Azure Monitor. La disponibilidad de Data Lake Storage Gen2 se muestra en Azure Portal. Para obtener la disponibilidad más actualizada de una cuenta de Data Lake Storage Gen2, deberá ejecutar sus propias pruebas sintéticas para validar la disponibilidad. Otras métricas como el uso de almacenamiento total, las solicitudes de escritura/lectura y las entradas/salidas están disponibles para que las usen las aplicaciones de supervisión y también pueden desencadenar alertas cuando se superan los umbrales (por ejemplo, la latencia promedio o el número de errores por minuto).

## <a name="directory-layout-considerations"></a>Consideraciones sobre el diseño del directorio

Cuando se colocan los datos en una instancia de Data Lake Store, es importante planear previamente la estructura de los datos para que se pueda emplear eficazmente la seguridad, el particionamiento y el procesamiento. Muchas de las recomendaciones siguientes son aplicables a todas las cargas de trabajo de macrodatos. Cada carga de trabajo tiene distintos requisitos sobre cómo los datos se consumen pero, a continuación, se muestran algunos diseños habituales a tener en cuenta al trabajar con escenarios de IoT y de lotes.

### <a name="iot-structure"></a>Estructura de IoT

En las cargas de trabajo de IoT, puede haber una gran cantidad de datos que se colocan en el almacén de datos que abarca varios productos, dispositivos, organizaciones y clientes. Es importante planear previamente el diseño del directorio para la organización, la seguridad y un procesamiento eficaz de los datos para los consumidores de nivel inferior. Una plantilla general a tener en cuenta podría tener el siguiente diseño:

*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*

Por ejemplo, la telemetría de aterrizaje de un motor de un avión del Reino Unido podría ser parecida a la estructura siguiente:

*UK/Planes/BA1293/Engine1/2017/08/11/12/*

Existe un motivo importante para poner la fecha al final de la estructura de directorios. Si desea bloquear determinadas regiones o asuntos a usuarios o grupos, puede hacerlo fácilmente con los permisos POSIX. En caso contrario, si fuera necesario aplicar restricciones a un determinado grupo de seguridad para que solo vea los datos del Reino Unido o de determinados aviones con la estructura de fechas delante, se requeriría un permiso independiente para varios directorios bajo el directorio de cada hora. Además, el hecho de tener la estructura de fecha delante, aumentaría exponencialmente el número de directorios a medida que transcurriera el tiempo.

### <a name="batch-jobs-structure"></a>Estructura de trabajos por lotes

Desde una perspectiva general, un enfoque utilizado habitualmente en el procesamiento por lotes es situar los datos en un directorio "in". Posteriormente, una vez procesados los datos, coloque los nuevos datos en un directorio "out" para que los consuman los procesos de nivel inferior. Esta estructura de directorio se ve a veces con trabajos que requieren el procesamiento de archivos individuales pero que no requieren un procesamiento paralelo masivo en grandes conjuntos de datos. Al igual que en la estructura de IoT recomendada anteriormente, una buena estructura de directorios dispone de directorios de nivel primario para cosas como las regiones o los asuntos (por ejemplo, organización, producto o productor). Esta estructura ayuda a proteger los datos en la organización y a mejorar la administración de los datos de las cargas de trabajo. Además, tenga en cuenta la posibilidad de usar la fecha y la hora en la estructura para permitir una mejor organización, búsquedas filtradas, seguridad y automatización del procesamiento. El nivel de granularidad de la estructura de fecha viene determinado por el intervalo en el que los datos se cargan o procesan como, por ejemplo, cada hora, cada día o incluso mensualmente.

En algunas ocasiones, el procesamiento de archivos es incorrecto debido a datos dañados o a formatos imprevistos. En tales casos, podría resultar útil que la estructura de directorios tuviera una carpeta **/bad** a la que mover los archivos para una mayor inspección. El trabajo por lotes también puede controlar el informe o notificación de estos archivos *incorrectos* para una posterior intervención manual. Tenga en cuenta la siguiente estructura de plantilla:

*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*

Por ejemplo, una empresa de marketing recibe a diario extractos de datos de actualizaciones de los clientes de Norteamérica. Podría tener el aspecto del siguiente fragmento de código antes y después del procesamiento:

*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\
*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*

En el caso habitual de datos por lotes que se procesan directamente en bases de datos como Hive o instancias tradicionales de SQL Database, no es necesaria una carpeta **/in** o **/out** puesto que la salida ya va a una carpeta independiente de la tabla de Hive o a una base de datos externa. Por ejemplo, los extractos diarios de los clientes se colocarían en sus respectivas carpetas y la orquestación de Azure Data Factory, Apache Oozie o Apache Airflow desencadenaría un trabajo diario de Hive o Spark que procesaría y escribiría los datos en una tabla de Hive.
