---
title: Transformación de datos con Jar en Databricks
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo procesar o transformar datos mediante la ejecución de una instancia de Jar en Databricks dentro de una canalización de Azure Data Factory.
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: d713decf0a82b5e501b7c2065a358e8ccc754d8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638015"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformación de datos mediante la ejecución de una actividad de Jar en Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La actividad de Jar en Azure Databricks en una [canalización de Data Factory](concepts-pipelines-activities.md) ejecuta un archivo Jar de Spark en el clúster de Azure Databricks. Este artículo se basa en el artículo sobre [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.  Azure Databricks es una plataforma administrada para ejecutar Apache Spark.

Si desea una introducción y demostración de once minutos de esta característica, vea el siguiente vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definición de la actividad de Jar en Databricks

Esta es la definición de JSON de ejemplo de una actividad de Jar en Databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Propiedades de la actividad de Jar en Databricks

En la siguiente tabla se describen las propiedades JSON que se usan en la definición de JSON:

|Propiedad|Descripción|Obligatorio|
|:--|---|:-:|
|name|Nombre de la actividad en la canalización.|Sí|
|description|Texto que describe para qué se usa la actividad.|No|
|type|En el caso de la actividad de Jar en Databricks, el tipo de actividad es DatabricksSparkJar.|Sí|
|linkedServiceName|Nombre del servicio vinculado de Databricks en el que se ejecuta la actividad de Jar. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos).|Sí|
|mainClassName|Nombre completo de la clase que incluye el método principal que se va a ejecutar. Esta clase debe estar contenida en un archivo JAR que se proporciona como una biblioteca. Un archivo JAR puede contener varias clases. Cada una de las clases puede contener un método main.|Sí|
|parámetros|Parámetros que se pasarán al método principal. Esta propiedad es una matriz de cadenas.|No|
|libraries|Lista de bibliotecas para instalar en el clúster que ejecutará el trabajo. Puede ser una cadena de <cadena, objeto>|Sí (al menos una con el método mainClassName)|

> [!NOTE]
> **Problema conocido**: cuando se usa el mismo [clúster interactivo](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) para ejecutar actividades simultáneas de Jar en Databricks (sin reiniciar el clúster), se produce un problema conocido en Databricks por el que los parámetros "in" de la primera actividad se utilizarán también en las siguientes actividades. El resultado son parámetros incorrectos que se pasan a los trabajos posteriores. Para mitigar esto, use un [clúster de trabajo](compute-linked-services.md#example---using-new-job-cluster-in-databricks) en su lugar.

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas compatibles con las actividades de Databricks

En la definición de la actividad de Databricks anterior, especificó estos tipos de biblioteca: `jar`, `egg`, `maven`, `pypi` y `cran`.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Para más información, consulte la [documentación de Databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) sobre los tipos de bibliotecas.

## <a name="how-to-upload-a-library-in-databricks"></a>Carga de una biblioteca en Databricks

### <a name="you-can-use-the-workspace-ui"></a>Puede usar la interfaz de usuario del área de trabajo:

1. [Uso de la interfaz de usuario del área de trabajo de Databricks](/azure/databricks/libraries/#create-a-library)

2. Para obtener la ruta de acceso de dbfs de la biblioteca que se agregó mediante la interfaz de usuario, puede usar la [CLI de Databricks](/azure/databricks/dev-tools/cli/#install-the-cli).

   Habitualmente, las bibliotecas de Jar se almacenan en dbfs:/FileStore/jars mientras se usa la interfaz de usuario. Puede enumerar todo mediante la CLI: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>O bien, puede usar la CLI de Databricks:

1. Siga [Copia de la biblioteca mediante la CLI de Databricks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Uso de la CLI de Databricks [(pasos de instalación)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Por ejemplo, para copiar un archivo JAR en dbfs: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Pasos siguientes

Si desea una introducción y demostración de once minutos de esta característica, vea el [vídeo](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player).
