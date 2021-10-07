---
title: Transformación de datos con cuadernos de Databricks
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo procesar o transformar datos mediante la ejecución de un cuaderno de Databricks en las canalizaciones de Synapse Analytics y Azure Data Factory.
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 9f82c1f2e39261ba4ba1072f5da9f807f23a180e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798472"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformación de datos mediante la ejecución de blocs de notas de Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La actividad de cuadernos de Azure Databricks en una [canalización](concepts-pipelines-activities.md) ejecuta un cuaderno de Databricks en el área de trabajo de Azure Databricks. Este artículo se basa en el artículo sobre [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.  Azure Databricks es una plataforma administrada para ejecutar Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definición de la actividad Notebook de Databricks

Esta es la definición de JSON de ejemplo de una actividad Notebook de Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Propiedades de la actividad Notebook de Databricks

En la siguiente tabla se describen las propiedades JSON que se usan en la definición de JSON:

|Propiedad|Descripción|Obligatorio|
|---|---|---|
|name|Nombre de la actividad en la canalización.|Sí|
|description|Texto que describe para qué se usa la actividad.|No|
|type|Para la actividad Notebook de Databricks, el tipo de actividad es DatabricksNotebook.|Sí|
|linkedServiceName|Nombre del servicio vinculado de Databricks en el que se ejecuta el bloc de notas de Databricks. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos).|Sí|
|notebookPath|La ruta de acceso absoluta del cuaderno que se va a ejecutar en el área de trabajo de Databricks. Esta ruta de acceso debe comenzar con una barra diagonal.|Sí|
|baseParameters|Una matriz de pares de clave y valor. Se pueden utilizar parámetros base para cada ejecución de actividad. Si el cuaderno toma un parámetro que no se ha especificado, se usará el valor predeterminado del cuaderno. Encuentre más información sobre los parámetros en los [cuadernos de Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|No|
|libraries|Lista de bibliotecas para instalar en el clúster que ejecutará el trabajo. Puede ser una matriz de \<string, object>.|No|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas compatibles con las actividades de Databricks

En la definición de la actividad de Databricks anterior, se especifican estos tiposd e biblioteca: *jar*, *egg*, *whl*, *maven*, *pypi*, *cran*.

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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

Para más detalles, consulte la [documentación de Databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) sobre los tipos de bibliotecas.

## <a name="passing-parameters-between-notebooks-and-pipelines"></a>Pasar parámetros entre cuadernos y canalizaciones

Puede pasar parámetros a cuadernos mediante la propiedad *baseParameters* que se encuentra en la actividad de Databricks.

En ciertos casos, es posible que necesite devolver algunos valores del cuaderno al servicio, ya que se pueden usar en el flujo de control (esto es, para realizar comprobaciones condicionales) del servicio; también se pueden consumir mediante actividades de bajada (el límite de tamaño es de 2 MB).

1. En el cuaderno, puede llamar a [dbutils.notebook.exit("returnValue")](/azure/databricks/notebooks/notebook-workflows#notebook-workflows-exit) y el valor de "returnValue" correspondiente se devolverá al servicio.

2. Puede usar la salida del servicio mediante una expresión como `@{activity('databricks notebook activity name').output.runOutput}`. 

   > [!IMPORTANT]
   > Si va a pasar un objeto JSON, puede obtener los valores anexando los nombres de propiedad. Ejemplo: `@{activity('databricks notebook activity name').output.runOutput.PropertyName}`

## <a name="how-to-upload-a-library-in-databricks"></a>Carga de una biblioteca en Databricks

### <a name="you-can-use-the-workspace-ui"></a>Puede usar la interfaz de usuario del área de trabajo:

1. [Uso de la interfaz de usuario del área de trabajo de Databricks](/azure/databricks/libraries/#create-a-library)

2. Para obtener la ruta de acceso de dbfs de la biblioteca que se agregó mediante la interfaz de usuario, puede usar la [CLI de Databricks](/azure/databricks/dev-tools/cli/#install-the-cli).

   Habitualmente, las bibliotecas de Jar se almacenan en dbfs:/FileStore/jars mientras se usa la interfaz de usuario. Puede enumerar todo mediante la CLI: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>O bien, puede usar la CLI de Databricks:

1. Siga [Copia de la biblioteca mediante la CLI de Databricks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Uso de la CLI de Databricks [(pasos de instalación)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Por ejemplo, para copiar un archivo JAR en dbfs: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
