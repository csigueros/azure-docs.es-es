---
title: 'Componentes y versiones de Apache Hadoop: Azure HDInsight 3.6'
description: Obtenga información sobre los componentes y las versiones de Apache Hadoop disponibles en Azure HDInsight 3.6.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f9e040c596d9335dcf1027aa96ad671a77ac88c0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722299"
---
# <a name="hdinsight-36-component-versions"></a>Versiones de componentes de HDInsight 3.6

En este artículo, obtendrá información sobre los componentes y las versiones del entorno Apache Hadoop que se incluyen en Azure HDInsight 3.6.

## <a name="support-for-hdinsight-36"></a>Compatibilidad con HDInsight 3.6

A partir del 1 de julio de 2021, Microsoft ofrecerá soporte técnico Basic para determinados tipos de clúster de HDI 3.6.
En la tabla siguiente se muestra el período de soporte técnico de los tipos de clúster de HDInsight 3.6.

| Tipo de clúster                    | Versión del marco | Expiración del soporte técnico estándar       | Nueva fecha de expiración del soporte Basic | Fecha de retirada |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 30 de junio de 2021                     | 3 de abril de 2022                | 4 de abril de 2022 |
| HDInsight 3.6 Spark             | 2.3               | 30 de junio de 2021                     | 3 de abril de 2022                | 4 de abril de 2022 |
| HDInsight 3.6 Kafka             | 1,1               | 30 de junio de 2021                     | 3 de abril de 2022                | 4 de abril de 2022 |
| HDInsight 3.6 HBase             | 1,1               | 30 de junio de 2021                     | 3 de abril de 2022                | 4 de abril de 2022 |
| HDInsight 3.6 Interactive Query | 2.1               | 30 de junio de 2021                     | 3 de abril de 2022                | 4 de abril de 2022 |
| HDInsight 3.6 Storm             | 1,1               | 30 de junio de 2021                     | 3 de abril de 2022                | 4 de abril de 2022 |
| HDInsight 3.6 ML Services      | 9.3               | -                                 | -                            | 31 de diciembre de 2020 |
| HDInsight 3.6 Spark             | 2,2               | -                                 | -                            | 30 de junio de 2020 |
| HDInsight 3.6 Spark             | 2.1               | -                                 | -                            | 30 de junio de 2020 |
| HDInsight 3.6 Kafka             | 1,0               | -                                 | -                            | 30 de junio de 2020 |

## <a name="apache-components-available-with-hdinsight-version-36"></a>Componentes de Apache disponibles con la versión 3.6 de HDInsight

En la tabla siguiente se enumeran las versiones de los componentes de software de código abierto asociadas a HDInsight 3.6.

| Componente              | HDInsight 3.6 (predeterminado)     |
|------------------------|-----------------------------|
| Apache Hadoop y YARN | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache Pig             | 0.16.0                      |
| Apache Hive            | (2.1.0 en ESP Interactive Query) |
| Apache Tez Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| HBase Apache           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0+                      |
| Apache Phoenix         | 4.7.0                       |
| Spark de Apache           | 2.3.2                      |
| Apache Livy            | 0.4                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de clúster para Apache Hadoop, Spark, etc. en HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Paquete de seguridad de la empresa](./enterprise-security-package.md)
- [Trabajo en Apache Hadoop en HDInsight desde un equipo Windows](hdinsight-hadoop-windows-tools.md)
