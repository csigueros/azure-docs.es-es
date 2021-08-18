---
title: Creación de Azure HDInsight - Azure Data Lake Storage Gen2 - CLI de Azure
description: Obtenga información sobre cómo usar Azure Data Lake Storage Gen2 con clústeres de Azure HDInsight mediante la CLI de Azure.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: 4adc7fffb04ac917c680993c43ef334a5df80582
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112280484"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Creación de un clúster con Data Lake Storage Gen2 mediante la CLI de Azure

Para crear un clúster de HDInsight que use Data Lake Storage Gen2 para el almacenamiento, siga estos pasos.

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con Azure Data Lake Storage Gen2, consulte la [sección de introducción](hdinsight-hadoop-use-data-lake-storage-gen2.md). 
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para ejecutar los ejemplos de script de la CLI, tiene tres opciones:
    - Usar [Azure Cloud Shell](../cloud-shell/overview.md) desde Azure Portal (consulte la sección siguiente).
    - Usar Azure Cloud Shell integrado a través del botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
    - [Instale la versión más reciente de la CLI de Azure](/cli/azure/install-azure-cli) (2.0.13 o posterior) si prefiere usar una consola de CLI local. Inicie sesión en Azure con `az login`, usando una cuenta asociada a la suscripción de Azure en la que desearía implementar la CLI de Azure de la identidad administrada asignada por el usuario. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

Puede [descargar un archivo de plantilla de muestra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) y [descargar un archivo de parámetros de muestra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de usar la plantilla y el fragmento de código de la CLI de Azure que aparecen a continuación, reemplace estos marcadores de posición por sus valores correctos:

| Marcador de posición | Descripción |
|---|---|
| `<SUBSCRIPTION_ID>` | Identificador de la suscripción de Azure |
| `<RESOURCEGROUPNAME>` | El grupo de recursos donde quiere crear el nuevo clúster y la cuenta de almacenamiento. |
| `<MANAGEDIDENTITYNAME>` | El nombre de la identidad administrada a la que se concederán permisos en la cuenta de almacenamiento con Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | La nueva cuenta de almacenamiento con Azure Data Lake Storage Gen2 que se creará. |
| `<FILESYSTEMNAME>`  | Nombre del sistema de archivos que este clúster debe usar en la cuenta de almacenamiento. |
| `<CLUSTERNAME>` | El nombre del clúster de HDInsight. |
| `<PASSWORD>` | La contraseña elegida para iniciar sesión en el clúster mediante SSH y el panel de Ambari. |

El siguiente fragmento de código realiza los pasos iniciales siguientes:

1. Inicia sesión en la cuenta de Azure.
1. Establece la suscripción activa donde se realizarán las operaciones de creación.
1. Crea un nuevo grupo de recursos para las nuevas actividades de implementación.
1. Crea una identidad administrada asignada por el usuario.
1. Agrega una extensión a la CLI de Azure para usar las características para Data Lake Storage Gen2.
1. Crea una nueva cuenta de almacenamiento con Data Lake Storage Gen2 mediante el uso de la marca `--hierarchical-namespace true`.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Después, inicie sesión en el portal. Agregue la nueva identidad administrada asignada por el usuario al rol **Colaborador de datos de blobs de almacenamiento** en la cuenta de almacenamiento. Este paso se describe en el paso 3, en [Uso de Azure Portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

 > [!IMPORTANT]
 > Asegúrese de que la cuenta de almacenamiento tenga la identidad asignada por el usuario con permisos de rol **Colaborador de datos de blobs de almacenamiento**; de lo contrario, se producirá un error en la creación del clúster.

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Después de completar el artículo, puede eliminar el clúster. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no se esté usando. También se le cobrará por un clúster de HDInsight aunque no se esté usando. Como en muchas ocasiones los cargos por el clúster son mucho más elevados que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no se usen.

Escriba todos o algunos de los siguientes comandos para quitar recursos:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

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
