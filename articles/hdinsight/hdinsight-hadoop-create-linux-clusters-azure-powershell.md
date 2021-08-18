---
title: Creación de clústeres de Apache Hadoop con PowerShell en Azure HDInsight
description: Aprenda a crear clústeres de Apache Hadoop, Apache HBase, Apache Storm o Apache Spark en Linux para HDInsight con Azure PowerShell.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 218acd1c376743faaee8cd1f96ef867adf33b5f0
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282356"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Crear clústeres basados en Linux en HDInsight con Azure PowerShell

[!INCLUDE [selector](includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell es un eficaz entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Microsoft Azure. En este documento se ofrece información sobre cómo crear un clúster de HDInsight basado en Linux mediante Azure PowerShell. También se incluye un script de ejemplo.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Módulo Az de [Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="create-cluster"></a>Crear clúster

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

Para crear un clúster de HDInsight con Azure PowerShell, debe realizar los procedimientos siguientes:

* Creación de un grupo de recursos de Azure
* Creación de una cuenta de Azure Storage
* Creación de un contenedor de blobs de Azure
* Creación de un clúster de HDInsight

> [!NOTE]
> El uso de PowerShell para crear un clúster de HDInsight con Azure Data Lake Storage Gen2 no se admite actualmente.

El script siguiente muestra cómo crear un nuevo clúster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Los valores que se especifican para el inicio de sesión de clúster se usan para crear la cuenta de usuario de Hadoop del clúster. Use esta cuenta para conectarse a servicios hospedados en el clúster como interfaces de usuario web o API de REST.

Los valores que se especifican para el usuario SSH se usan para crear el usuario SSH para el clúster. Use esta cuenta para iniciar una sesión remota de SSH en el clúster y ejecutar trabajos. Para más información, vea el documento [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Si tiene pensado usar más de 32 nodos de trabajo (en el momento de crear el clúster o cambiando el tamaño del clúster después de su creación), también debe especificar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
> Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Un clúster puede tardar hasta 20 minutos en crearse.

## <a name="create-cluster-configuration-object"></a>Creación de un clúster: objeto de configuración

También puede crear un objeto de configuración de HDInsight mediante el cmdlet [`New-AzHDInsightClusterConfig`](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig). A continuación, puede modificar este objeto de configuración con el fin de habilitar otras opciones de configuración para el clúster. Por último, utilice el parámetro `-Config` del cmdlet [`New-AzHDInsightCluster`](/powershell/module/az.hdinsight/new-azhdinsightcluster) para emplear la configuración.

## <a name="customize-clusters"></a>Personalización de los clústeres

* Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya ha creado correctamente un clúster de HDInsight, use los siguientes recursos para aprender a trabajar con él.

### <a name="apache-hadoop-clusters"></a>Clústeres de Apache Hadoop

* [Uso de Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Uso de MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clústeres de Apache HBase

* [Introducción a Apache HBase en HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para Apache HBase en HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](storm/apache-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clústeres de Apache Spark

* [Crear una aplicación independiente con Scala](spark/apache-spark-create-standalone-application.md)
* [Ejecución de trabajos de forma remota en un clúster de Apache Spark mediante Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark con BI: Análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)