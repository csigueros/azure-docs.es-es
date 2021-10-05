---
title: Create Azure HDInsight - Azure Data Lake Storage Gen2 - Portal
description: Obtenga información sobre cómo usar Azure Data Lake Storage Gen2 con clústeres de Azure HDInsight mediante el portal.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/07/2021
ms.openlocfilehash: 2014f0cde340b0070b8c8e5e7fbbd7c2ab6667fa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797579"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Creación de un clúster con Data Lake Storage Gen2 mediante Azure Portal

El Portal de Azure es una herramienta de administración basada en web para servicios y recursos hospedados en la nube de Microsoft Azure. En este artículo aprenderá a crear clústeres de Azure HDInsight basados en Linux mediante el portal. Puede encontrar más detalles en [Creación de clústeres de HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para crear un clúster de HDInsight que use Data Lake Storage Gen2 para el almacenamiento, siga estos pasos para configurar una cuenta de almacenamiento que tenga un espacio de nombres jerárquico.

## <a name="create-a-user-assigned-managed-identity"></a>Crear una identidad administrada asignada por el usuario

Cree una identidad administrada asignada por el usuario si todavía no tiene una.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la parte superior izquierda, haga clic en **Crear un recurso**.
1. En el cuadro de búsqueda, escriba **usuario asignado** y haga clic en **Identidad administrada asignada por el usuario**.
1. Haga clic en **Crear**.
1. Escriba un nombre para la identidad administrada y seleccione la suscripción, el grupo de recursos y la ubicación correctos.
1. Haga clic en **Crear**.

Para obtener más información sobre cómo funcionan las identidades administradas en Azure HDInsight, vea [Identidades administradas en Azure HDInsight](hdinsight-managed-identities.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="Creación de una identidad administrada asignada por el usuario":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Creación de una cuenta de almacenamiento para su uso con Data Lake Storage Gen2

Cree una cuenta de almacenamiento para su uso con Azure Data Lake Storage Gen2.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la parte superior izquierda, haga clic en **Crear un recurso**.
1. En el cuadro de búsqueda, escriba **almacenamiento** y haga clic en **Cuenta de almacenamiento**.
1. Haga clic en **Crear**.
1. En la pantalla **Crear la cuenta de almacenamiento**:
    1. Seleccione la suscripción y el grupo de recursos correctos.
    1. Escriba un nombre para la cuenta de almacenamiento con Data Lake Storage Gen2.
    1. Haga clic en la pestaña **Avanzado**.
    1. Haga clic en **Habilitado** junto a **Espacio de nombres jerárquico** en **Data Lake Storage Gen2**.
    1. Haga clic en **Revisar + crear**.
    1. Haga clic en **Crear**

Para obtener más información sobre otras opciones durante la creación de la cuenta de almacenamiento, consulte [Inicio rápido: Creación de una cuenta de almacenamiento para Azure Data Lake Storage Gen2](../storage/blobs/create-data-lake-storage-account.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="Captura de pantalla en la que se muestra la creación de una cuenta de almacenamiento en Azure Portal":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Configuración de permisos para la identidad administrada en Data Lake Storage Gen2

Asigne la identidad administrada al rol **Propietario de datos de Storage Blob** en la cuenta de almacenamiento.

1. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento.
1. Seleccione la cuenta de almacenamiento y, después, seleccione **Control de acceso (IAM)** para mostrar la configuración del control de acceso de la cuenta. Seleccione la pestaña **Asignaciones de roles** para ver la lista de asignaciones de roles.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="Captura de pantalla que muestra la configuración de control de acceso del almacenamiento":::

1. Seleccione el botón **+ Agregar asignación de roles** para agregar un rol nuevo.
1. En la ventana **Agregar asignación de roles**, seleccione el rol **Propietario de datos de Storage Blob**. Luego, seleccione la suscripción que tiene la identidad administrada y la cuenta de almacenamiento. A continuación, busque la identidad administrada asignada por el usuario que creó anteriormente. Por último, seleccione la identidad administrada y esta se mostrará en **Miembros seleccionados**.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="Captura de pantalla que muestra cómo asignar un rol de Azure":::

1. Seleccione **Guardar**. La identidad asignada por el usuario que seleccionó ahora aparece en el rol seleccionado.
1. Una vez completada la configuración inicial, puede crear un clúster a través del portal. El clúster debe estar en la misma región de Azure que la cuenta de almacenamiento. En la pestaña **Almacenamiento** del menú de creación del clúster, seleccione las siguientes opciones:

    * En **Tipo de almacenamiento principal**, haga clic en **Azure Data Lake Storage Gen2**.
    * En **Cuenta de almacenamiento principal**, busque y seleccione la cuenta de almacenamiento recién creada con Data Lake Storage Gen2.

    * En **Identidad**, seleccione la identidad administrada asignada por el usuario recién creada.

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="Configuración de almacenamiento para usar Data Lake Storage Gen2 con Azure HDInsight":::

    > [!NOTE]
    > * Para agregar una cuenta secundaria con Data Lake Storage Gen2 en el nivel de cuenta de almacenamiento, basta con asignar la identidad administrada que ha creado anteriormente a la nueva de Data Lake Storage Gen2 que quiere agregar. Tenga en cuenta que no se admite la adición de una cuenta de almacenamiento secundaria de Data Lake Storage Gen2 mediante la hoja "Cuentas de almacenamiento adicionales" en HDInsight.
    > * Puede habilitar RA-GRS o RA-ZRS en la cuenta de Azure Blob Storage que usa HDInsight. Sin embargo, no se admite la creación de un clúster en el punto de conexión secundario RA-GRS o RA-ZRS.
    > * HDInsight no admite la configuración de Data Lake Storage Gen2 como almacenamiento con redundancia de zona geográfica con acceso de lectura (RA-GZRS) o almacenamiento con redundancia de zona geográfica (GZRS).

## <a name="delete-the-cluster"></a>Eliminación del clúster

Consulte [Eliminación de un clúster de HDInsight con el explorador, PowerShell o la CLI de Azure](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Pasos siguientes

Ha creado correctamente un clúster de HDInsight. Ahora puede aprender a trabajar con el clúster.

### <a name="apache-spark-clusters"></a>Clústeres de Apache Spark

* [Personalización de clústeres de HDInsight basados en Linux mediante acciones de script](hdinsight-hadoop-customize-cluster-linux.md)
* [Crear una aplicación independiente con Scala](spark/apache-spark-create-standalone-application.md)
* [Ejecución de trabajos de forma remota en un clúster de Apache Spark mediante Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark con BI: Análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Clústeres de Apache Hadoop

* [Uso de Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Uso de MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clústeres de Apache HBase

* [Introducción a Apache HBase en HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para Apache HBase en HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
