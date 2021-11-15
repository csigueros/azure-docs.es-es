---
title: Migración del clúster a una versión más reciente
titleSuffix: Azure HDInsight
description: Conozca las directrices para migrar el clúster de Azure HDInsight a una versión más reciente.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 6fa655092de1c3e103381ccd58bf840b70a3809e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249768"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migración del clúster de HDInsight a una versión más reciente

Para aprovechar las ventajas de las características más recientes de HDInsight, se recomienda que los clústeres de HDInsight se migren regularmente a la versión más reciente. HDInsight no admite actualizaciones locales en las que un clúster existente se actualiza a una versión más reciente del componente. Debe crear un nuevo clúster con la versión de plataforma y componente deseada y, a continuación, migrar las aplicaciones para que usen el nuevo clúster. Siga las directrices que aparecen a continuación para migrar las versiones del clúster de HDInsight.

> [!NOTE]  
> Para más información sobre las versiones admitidas de HDInsight, consulte [Versiones de los componentes de HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Tareas de migración

El flujo de trabajo para actualizar el clúster de HDInsight es el siguiente.
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="Diagrama de flujo de trabajo de actualización de HDInsight" border="false":::

1. Lea cada sección de este documento para entender los cambios que pueden ser necesarios al actualizar el clúster de HDInsight.
2. Cree un clúster como entorno de control de calidad o de pruebas. Para más información sobre cómo crear un clúster, consulte [Más información sobre cómo crear clústeres de HDInsight basados en Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Copie los trabajos, los orígenes de datos y los receptores existentes en el nuevo entorno.
4. Realice pruebas de validación para asegurarse de que los trabajos funcionan como se esperaba en el nuevo clúster.

Cuando haya comprobado que todo funciona según lo esperado, programe el tiempo de inactividad para la migración. Durante este tiempo de inactividad, realice las acciones siguientes:

1. Haga copia de seguridad de todos los datos transitorios almacenados localmente en los nodos del clúster. Por ejemplo, si tiene datos que se almacenan directamente en un nodo principal.
1. [Elimine el clúster existente](./hdinsight-delete-cluster.md).
1. Cree un clúster en la misma subred de red virtual con la versión de HDI más reciente (o compatible) con el mismo almacén de datos predeterminado que usaba el clúster anterior. Esto permitirá que el nuevo clúster siga trabajando con los datos de producción existentes.
1. Importe los datos transitorios cuya copia de seguridad realizó.
1. Inicie trabajos o continúe el procesamiento con el nuevo clúster.

## <a name="workload-specific-guidance"></a>Orientación específica de la carga de trabajo

En los siguientes documentos se proporcionan instrucciones sobre cómo migrar cargas de trabajo específicas:

* [Migración de HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migración de Kafka](./kafka/migrate-versions.md)
* [Migración de Hive/Interactive Query](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

Para más información sobre la copia de seguridad y restauración de bases de datos, consulte [Recuperación de una base de datos de Azure SQL Database mediante copias de seguridad de datos automatizadas](../azure-sql/database/recovery-using-backups.md).

## <a name="upgrade-scenarios"></a>Actualización de los escenarios

Como se mencionó anteriormente, Microsoft recomienda que los clústeres de HDInsight se migren periódicamente a la versión más reciente con el fin de aprovechar las características y correcciones nuevas. Consulte esta lista de motivos por los que es posible que solicitemos eliminar un clúster y volver a implementarlo:

* La versión del clúster esta [Retirada](hdinsight-retired-versions.md) o en [Soporte técnico Basic](hdinsight-36-component-versioning.md), y usted tiene un problema de clúster que se resolvería con una versión más reciente.
* Se determina que la causa principal de un problema de clúster está relacionada con una máquina virtual de tamaño insuficiente. [Consulte la configuración de nodo recomendada de Microsoft](hdinsight-supported-node-configuration.md#all-supported-regions-except-brazil-south-and-japan-west).
* Un cliente abre un caso de soporte técnico y el equipo de ingeniería de Microsoft determina que el problema ya se corrigió en una versión más reciente del clúster.
* Una base de datos de metastore predeterminada (Ambari, Hive, Oozie, Ranger) alcanzó su límite de uso. Microsoft le pedirá que vuelva a crear el clúster con una base de datos de [metastore personalizada](hdinsight-use-external-metadata-stores.md#custom-metastore).
* La causa principal de un problema de clúster se debe a una **operación no admitida**. Entre las operaciones no admitidas comunes se encuentran las siguientes:
     * **Mover o agregar un servicio en Ambari**. Al ver información en los servicios de clúster en Ambari, una de las acciones disponibles en el menú Acciones del servicio es **Mover [nombre del servicio]** . Otra acción es **Agregar [nombre del servicio]** . No se admite ninguna de estas opciones.
     * **El paquete de Python está dañado**. Los clústeres de HDInsight dependen de los entornos integrados de Python: Python 2.7 y Python 3.5. La instalación directa de paquetes personalizados en esos entornos integrados predeterminados puede producir cambios inesperados en la versión de la biblioteca e interrumpir el clúster. Aprenda a [instalar de forma segura los paquetes externos personalizados de Python](./spark/apache-spark-python-package-installation.md#safely-install-external-python-packages) para las aplicaciones Spark.
     * **Software de terceros**. Los clientes tienen la capacidad de instalar software de terceros en los clústeres de HDInsight, pero le recomendamos volver a crear el clúster si interrumpe la funcionalidad existente.
     * **Varias cargas de trabajo en el mismo clúster**. En HDInsight 4.0, Hive Warehouse Connector necesita clústeres independientes para las cargas de trabajo de Spark y de Interactive Query. [Siga estos pasos para configurar ambos clústeres en Azure HDInsight](interactive-query/apache-hive-warehouse-connector.md). De manera similar, la integración de [Spark con HBASE](hdinsight-using-spark-query-hbase.md) requiere dos clústeres distintos.
     * **Cambio de la contraseña de la base de datos personalizada de Ambari**. La contraseña de la base de datos de Ambari se establece al crear el clúster y actualmente no hay ningún mecanismo para actualizarla. Si un cliente implementa el clúster con una [base de datos personalizada de Ambari](hdinsight-custom-ambari-db.md), tendrá la capacidad de cambiar la contraseña de la base de datos en la BD de SQL, pero no hay cómo actualizar esta contraseña para un clúster de HDInsight en ejecución.

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre cómo crear clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Conexión a HDInsight mediante SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Administración de un clúster basado en Linux mediante Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Notas de la versión de HDInsight](./hdinsight-version-release.md)
