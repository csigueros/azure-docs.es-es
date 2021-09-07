---
title: Notas de la versión de Azure HDInsight.
description: Notas más recientes de la versión de Azure HDInsight. Obtenga sugerencias de desarrollo y detalles sobre Hadoop, Spark, Hive, etc.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: cf726033a90e256a2d41a3249e237f5eb91aee84
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732915"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de la versión de Azure HDInsight

Este artículo proporciona información sobre las **últimas** actualizaciones de versión de Azure HDInsight. Para más información sobre versiones anteriores, vea [Notas de la versión (en archivo) de los componentes de Hadoop en Azure HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumen

Azure HDInsight es uno de los servicios más populares entre los clientes de empresa para el análisis de código abierto en Azure.

Si quiere suscribirse a las notas de la versión, vea las versiones de [este repositorio de GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-07272021"></a>Fecha de lanzamiento: 27/07/2021

Esta versión se aplica a HDInsight 3.6 y HDInsight 4.0. La versión de HDInsight se pone a disposición de todas las regiones durante varios días. Esta fecha de lanzamiento indica la fecha de lanzamiento de la primera región. Si no ve los cambios siguientes, espere unos días a que la versión se active en su región.

Las versiones del sistema operativo de esta versión son:
- HDInsight 3.6: Ubuntu 16.04.7 LTS
- HDInsight 4.0: Ubuntu 18.04.5 LTS

## <a name="new-features"></a>Nuevas características
### <a name="new-azure-monitor-integration-experience-preview"></a>Nueva experiencia de integración de Azure Monitor (versión preliminar)
La nueva experiencia de integración de Azure Monitor estará disponible en versión preliminar en Este de EE. UU. y Oeste de Europa con esta versión. Obtenga más información sobre la nueva experiencia de Azure Monitor [aquí](./log-analytics-migration.md#migrate-to-the-new-azure-monitor-integration).

## <a name="deprecation"></a>Desuso
### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Soporte técnico Basic para HDInsight 3.6 a partir del 1 de julio de 2021
A partir del 1 de julio de 1, 2021, Microsoft ofrece [soporte técnico Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) para determinados tipos de clúster de HDInsight 3.6. El plan de soporte técnico Basic estará disponible hasta el 3 de abril de 2022. La inscripción al soporte técnico Basic se lleva a cabo automáticamente a partir del 1 de julio de 2021. No es necesario realizar ninguna acción para participar. Consulte [nuestra documentación](hdinsight-36-component-versioning.md) sobre los tipos de clúster que se incluyen en soporte técnico Basic. 

No se recomienda crear nuevas soluciones en HDInsight 3.6, inmovilice los cambios en entornos existentes de 3.6. Se recomienda [migrar los clústeres a HDInsight 4.0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). Más información sobre [las novedades de HDInsight 4.0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="behavior-changes"></a>Cambios de comportamiento
### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>HDInsight Interactive Query solo admite el escalado automático basado en programación
A medida que los escenarios de los clientes se vuelven más maduros y diversos, vamos identificando algunas limitaciones con la escala automática basada en la carga de Interactive Query (LLAP). Estas limitaciones se deben a la naturaleza de la dinámica de las consultas de LLAP, problemas de precisión en la predicción de carga futura y problemas en la redistribución de tareas del programador LLAP. Debido a estas limitaciones, es posible que los usuarios vean que sus consultas se ejecutan más lentamente en clústeres LLAP cuando el escalado automático está habilitado. El impacto en el rendimiento puede superar las ventajas relativas al costo de la escalabilidad automática.

A partir de julio de 2021, la carga de trabajo de Interactive Query en HDInsight solo admite la escalabilidad automática basada en programación. Ya no puede habilitar el escalado automático basado en la carga en clústeres Interactive Query nuevos. Los clústeres en ejecución existentes pueden seguir ejecutándose con las limitaciones conocidas descritas anteriormente. 

Microsoft recomienda pasar a un escalado automático basado en programación para LLAP.  Puede analizar el patrón de uso actual del clúster a través del panel de Grafana Hive. Para más información, consulte [Escalado automático de clústeres de Azure HDInsight](hdinsight-autoscale-clusters.md). 

## <a name="upcoming-changes"></a>Próximos cambios
En las próximas versiones, se realizarán los siguientes cambios.

### <a name="built-in-llap-component-in-esp-spark-cluster-will-be-removed"></a>Se quitará el componente LLAP integrado en el clúster ESP Spark
El clúster HDInsight 4.0 ESP Spark tiene componentes LLAP integrados que se ejecutan en ambos nodos principales. Los componentes de LLAP del clúster ESP Spark se agregaron originalmente para HDInsight 3.6 ESP Spark, pero no tiene ningún caso de usuario real para HDInsight 4.0 ESP Spark. En la próxima versión programada en septiembre de 2021, HDInsight quitará el componente LLAP integrado del clúster HDInsight 4.0 ESP Spark. Este cambio le ayudará a descargar la carga de trabajo del nodo principal y a evitar confusiones entre los tipos de clúster ESP Spark y ESP Interactive Hive.

## <a name="new-region"></a>Nueva región
- Oeste de EE. UU. 3
- JIO del Oeste de la India
- Centro de Australia

## <a name="component-version-change"></a>Cambio de versión de componentes
La siguiente versión de componente ha cambiado con esta versión:
- Versión de ORC de 1.5.1 a 1.5.9

En [este documento](./hdinsight-component-versioning.md) puede encontrar las versiones actuales de los componentes para HDInsight 4.0 y HDInsight 3.6.

## <a name="back-ported-jiras"></a>Instancias de JIRA portadas a una versión anterior
Estas son las instancias de Apache JIRA de esta versión portadas a una versión anterior:

| Característica afectada    |   JIRA de Apache                                                      |
|---------------------|--------------------------------------------------------------------|
| Fecha / marca de tiempo    | [HIVE-25104](https://issues.apache.org/jira/browse/HIVE-25104)     |
|                     | [HIVE-24074](https://issues.apache.org/jira/browse/HIVE-24074)     |
|                     | [HIVE-22840](https://issues.apache.org/jira/browse/HIVE-22840)     |
|                     | [HIVE-22589](https://issues.apache.org/jira/browse/HIVE-22589)     |
|                     | [HIVE-22405](https://issues.apache.org/jira/browse/HIVE-22405)     |
|                     | [HIVE-21729](https://issues.apache.org/jira/browse/HIVE-21729)     |
|                     | [HIVE-21291](https://issues.apache.org/jira/browse/HIVE-21291)     |
|                     | [HIVE-21290](https://issues.apache.org/jira/browse/HIVE-21290)     |
| UDF                 | [HIVE-25268](https://issues.apache.org/jira/browse/HIVE-25268)     |
|                     | [HIVE-25093](https://issues.apache.org/jira/browse/HIVE-25093)     |
|                     | [HIVE-22099](https://issues.apache.org/jira/browse/HIVE-22099)     |
|                     | [HIVE-24113](https://issues.apache.org/jira/browse/HIVE-24113)     |
|                     | [HIVE-22170](https://issues.apache.org/jira/browse/HIVE-22170)     |
|                     | [HIVE-22331](https://issues.apache.org/jira/browse/HIVE-22331)     |
| ORC                 | [HIVE-21991](https://issues.apache.org/jira/browse/HIVE-21991)     |
|                     | [HIVE-21815](https://issues.apache.org/jira/browse/HIVE-21815)     |
|                     | [HIVE-21862](https://issues.apache.org/jira/browse/HIVE-21862)     |
| Esquema de tabla        | [HIVE-20437](https://issues.apache.org/jira/browse/HIVE-20437)     |
|                     | [HIVE-22941](https://issues.apache.org/jira/browse/HIVE-22941)     |
|                     | [HIVE-21784](https://issues.apache.org/jira/browse/HIVE-21784)     |
|                     | [HIVE-21714](https://issues.apache.org/jira/browse/HIVE-21714)     |
|                     | [HIVE-18702](https://issues.apache.org/jira/browse/HIVE-18702)     |
|                     | [HIVE-21799](https://issues.apache.org/jira/browse/HIVE-21799)     |
|                     | [HIVE-21296](https://issues.apache.org/jira/browse/HIVE-21296)     |
| Administración de cargas de trabajo | [HIVE-24201](https://issues.apache.org/jira/browse/HIVE-24201)     |
| Compactación          | [HIVE-24882](https://issues.apache.org/jira/browse/HIVE-24882)     |
|                     | [HIVE-23058](https://issues.apache.org/jira/browse/HIVE-23058)     |
|                     | [HIVE-23046](https://issues.apache.org/jira/browse/HIVE-23046)     |
| Vista materializada   | [HIVE-22566](https://issues.apache.org/jira/browse/HIVE-22566)     |