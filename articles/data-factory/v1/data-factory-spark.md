---
title: Invocar programas Spark desde Azure Data Factory
description: Obtenga información sobre cómo invocar programas Spark desde Azure Data Factory mediante la actividad MapReduce.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 73e20cfcdd0c56f2136b4ce658f7d2d2bed53022
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397317"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Invocación de programas Spark desde canalizaciones de Azure Data Factory

> [!div class="op_single_selector" title1="Actividades de transformación"]
> * [Actividad de Hive](data-factory-hive-activity.md)
> * [Actividad de Pig](data-factory-pig-activity.md)
> * [Actividad de MapReduce](data-factory-map-reduce.md)
> * [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Actividad de Spark](data-factory-spark.md)
> * [Actividad de ejecución por lotes de ML Studio (clásico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Actividad de actualización de recurso de ML Studio (clásico)](data-factory-azure-ml-update-resource-activity.md)
> * [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [Actividad U-SQL de Data Lake Analytics](data-factory-usql-activity.md)
> * [Actividad personalizada de .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Azure Data Factory, que está disponible con carácter general. Si usa la versión actual del servicio Data Factory, consulte el artículo acerca de la [transformación de datos mediante la actividad Apache Spark en Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introducción
La actividad de Spark es una de las [actividades de transformación de datos](data-factory-data-transformation-activities.md) compatibles con Data Factory. Esta actividad ejecuta el programa Spark especificado en el clúster de Spark en Azure HDInsight.

> [!IMPORTANT]
> - La actividad de Spark no admite clústeres de HDInsight Spark que usan una instancia de Azure Data Lake Store como almacenamiento principal.
> - La actividad de Spark admite solo los clústeres de HDInsight Spark existentes (los suyos propios). No admite un servicio vinculado de HDInsight a petición.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Tutorial: Crear una canalización con la actividad de Spark
Estos son los pasos habituales para crear una canalización de Data Factory con una actividad de Spark:

* Creación de una factoría de datos.
* Creación de un servicio vinculado de Azure Storage para vincular el almacenamiento asociado con el clúster de HDInsight Spark a la instancia de Data Factory.
* Creación de un servicio vinculado de HDInsight para vincular el clúster de Spark en HDInsight con la instancia de Data Factory.
* Creación de un conjunto de datos que haga referencia al servicio vinculado de Azure Storage. Actualmente, debe especificar un conjunto de datos de salida para una actividad incluso si no se produce ninguna salida.
* Creación de una canalización con la actividad de Spark que haga referencia al servicio vinculado de HDInsight que ha creado. La actividad se configura con el conjunto de datos que creó en el paso anterior como un conjunto de datos de salida. El conjunto de datos de salida es lo que impulsa la programación (cada hora o cada día). Por lo tanto, debe especificar el conjunto de datos de salida aunque la actividad no produzca realmente una salida.

### <a name="prerequisites"></a>Prerrequisitos
1. Cree una cuenta de almacenamiento de uso general siguiendo las instrucciones de [Crear una cuenta de almacenamiento](../../storage/common/storage-account-create.md).

1. Cree un clúster de Spark en HDInsight siguiendo las instrucciones del tutorial: [Creación de un clúster de Spark en HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Asocie la cuenta de almacenamiento creada en el paso 1 con este clúster.

1. Descargue y revise el archivo de script de Python **test.py** ubicado en [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Cargue **test.py** en la carpeta **pyFiles** del contenedor **adfspark** de Blob Storage. Cree el contenedor y la carpeta si no existen.

### <a name="create-a-data-factory"></a>Crear una factoría de datos
Para crear una factoría de datos, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione **Nuevo** > **Data + Analytics** > **Data Factory**.

1. En la hoja **Nueva factoría de datos**, en **Nombre**, escriba **SparkDF**.

   > [!IMPORTANT]
   > El nombre de la instancia de Azure Data Factory debe ser único de forma global. Si aparece el error "El nombre SparkDF de factoría de datos no está disponible", cambie dicho nombre. Por ejemplo, use yournameSparkDFdate y vuelva a crear la factoría de datos. Para más información acerca de las reglas de nomenclatura, consulte [Azure Data Factory: Reglas de nomenclatura](data-factory-naming-rules.md).

1. En **Suscripción**, seleccione la suscripción de Azure en la que desea que se cree la factoría de datos.

1. Seleccione un grupo de recursos de Azure existente o cree uno nuevo.

1. Seleccione la casilla **Anclar al panel**.

1. Seleccione **Crear**.

   > [!IMPORTANT]
   > Para crear instancias de Data Factory, es preciso ser miembro del rol [Colaborador de Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) en el nivel de grupo de recursos o suscripción.

1. Verá que la factoría de datos se creará en el panel de Azure Portal.

1. Tras crear la factoría de datos, se ve la página de la **factoría de datos**, que muestra su contenido. Si no ve la página de la **factoría de datos**, seleccione el icono de la factoría de datos en el panel.

    ![Hoja de la Factoría de datos](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Crear servicios vinculados
En este paso, creará dos servicios vinculados. Un servicio vincula su clúster de Spark a su factoría de datos y el otro vincula el almacenamiento a la factoría de datos.

#### <a name="create-a-storage-linked-service"></a>Creación de un servicio vinculado de Storage
En este paso, vinculará su cuenta de almacenamiento con su factoría de datos. Un conjunto de datos creado en un paso más adelante en este tutorial hace referencia a este servicio vinculado. El servicio vinculado de HDInsight que se define en el paso siguiente también hace referencia a este servicio vinculado.

1. En la hoja **Factoría de datos**, haga clic en el icono **Crear e implementar**. Aparece Data Factory Editor.

1. Seleccione **Nuevo almacén de datos** y elija **Azure Storage**.

   ![Nuevo almacén de datos](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. El script JSON que usa para crear un servicio vinculado de Storage aparece en el editor.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Reemplace **nombre de cuenta** y **clave de cuenta** por el nombre y la clave de acceso de su cuenta de almacenamiento. Para aprender a obtener la clave de acceso de almacenamiento, consulte [Administración de claves de acceso de la cuenta de almacenamiento](../../storage/common/storage-account-keys-manage.md).

1. Para implementar el servicio vinculado, seleccione **Implementar** en la barra de comandos. Después de que el servicio vinculado se haya implementado correctamente, la ventana Borrador-1 desaparece. **AzureStorageLinkedService** aparece en la vista de árbol de la izquierda.

#### <a name="create-an-hdinsight-linked-service"></a>Creación de un servicio vinculado de HDInsight
En este paso, se crea un servicio vinculado de HDInsight para vincular el clúster de HDInsight Spark con la factoría de datos. El clúster de HDInsight se usa para ejecutar el programa Spark especificado en la actividad de Spark de la canalización en este ejemplo.

1. En Data Factory Editor, seleccione **Más** > **Nuevo proceso** > **Clúster de HDInsight**.

    ![Creación del servicio vinculado de HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Copie y pegue el fragmento de código siguiente en la ventana Borrador-1. En el editor de JSON, realice los siguientes pasos:

    1. Especifique el identificador URI del clúster de HDInsight Spark. Por ejemplo: `https://<sparkclustername>.azurehdinsight.net/`.

    1. Especifique el nombre del usuario que tiene acceso al clúster de Spark.

    1. Especifique la contraseña del usuario.

    1. Especifique el servicio vinculado de Storage asociado con el clúster de HDInsight Spark. En este ejemplo, es AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - La actividad de Spark no admite clústeres de HDInsight Spark que usan una instancia de Azure Data Lake Store como almacenamiento principal.
    > - La actividad de Spark admite solo los clústeres de HDInsight Spark existentes (los suyos propios). No admite un servicio vinculado de HDInsight a petición.

    Para más información sobre el servicio vinculado de HDInsight, consulte [Servicio vinculado de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Para implementar el servicio vinculado, seleccione **Implementar** en la barra de comandos.

### <a name="create-the-output-dataset"></a>Creación del conjunto de datos de salida
El conjunto de datos de salida es lo que impulsa la programación (cada hora o cada día). Por lo tanto, debe especificar el conjunto de datos de salida para la actividad de Spark en la canalización aunque la actividad no produzca realmente una salida. Es opcional especificar un conjunto de datos de entrada para la actividad.

1. En Data Factory Editor, seleccione **More** > **New dataset** > **Azure Blob Storage** (Más > Nuevo conjunto de datos > Azure Blob Storage).

1. Copie y pegue el fragmento de código siguiente en la ventana Borrador-1. El fragmento de código JSON define un conjunto de datos denominado **OutputDataset**. Además, especifica que los resultados se almacenan en el contenedor de blobs llamado **adfspark** y en la carpeta llamada **pyFiles/output**. Como se mencionó anteriormente, este conjunto de datos es un conjunto de datos ficticio. El programa Spark de este ejemplo no genera ninguna salida. La sección **availability** especifica que el conjunto de datos de salida se genera diariamente.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Para implementar el conjunto de datos, haga clic en **Deploy** (Implementar) en la barra de comandos.


### <a name="create-a-pipeline"></a>Crear una canalización
En este paso, crea una canalización con una actividad de HDInsightSpark. Actualmente, el conjunto de datos de salida es lo que controla la programación, por lo que debe crear un conjunto de datos de salida aunque la actividad no genere ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada. Por lo tanto, no se especifica ningún conjunto de datos de entrada en este ejemplo.

1. En Data Factory Editor, seleccione **More** > **New pipeline** (Nueva canalización).

1. Reemplace el script en la ventana Draft-1 con el siguiente script:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```

    Tenga en cuenta los siguientes puntos:

    1. La propiedad de **tipo** se establece en **HDInsightSpark**.

    1. La propiedad **rootPath** se establece en **adfspark\\pyFiles**, donde adfspark es el contenedor de blobs y pyFiles es la carpeta de archivos en ese contenedor. En este ejemplo, la instancia de Blob Storage es la que está asociada con el clúster de Spark. Puede cargar el archivo en una cuenta de almacenamiento diferente. Si lo hace, cree un servicio vinculado de Storage para vincular esa cuenta de almacenamiento con la factoría de datos. A continuación, especifique el nombre del servicio vinculado como un valor de la propiedad **sparkJobLinkedService**. Para más información sobre esta y otras propiedades que admite la actividad de Spark, consulte [Propiedades de la actividad de Spark](#spark-activity-properties).

    1. La propiedad **entryFilePath** se establece en **test.py**, que es el archivo de Python.

    1. La propiedad **getDebugInfo** está establecida en **Siempre**, lo que significa que siempre se generan archivos de registro (acierto o error).

       > [!IMPORTANT]
       > Se recomienda que no establezca esta propiedad como `Always` en un entorno de producción, a menos que esté solucionando un problema.

    1. La sección de **salida** tiene un conjunto de datos de salida. Debe especificar un conjunto de datos de salida, incluso si el programa de Spark no genera ninguna salida. El conjunto de datos de salida impulsa la programación de la canalización (cada hora o cada día).

    Para más información sobre las propiedades que admite la actividad de Spark, consulte la sección [Propiedades de la actividad de Spark](#spark-activity-properties).

1. Seleccione **Deploy** (Implementar) en la barra de comandos para implementar la canalización.

### <a name="monitor-a-pipeline"></a>Supervisión de una canalización
1. En la hoja **Factoría de datos**, seleccione **Supervisión y administración** para iniciar la aplicación de supervisión en otra pestaña.

    ![Icono Supervisión y administración](media/data-factory-spark/monitor-and-manage-tile.png)

1. Cambie el filtro **Start time** (Hora de inicio) en la parte superior por **2/1/2017** y seleccione **Apply** (Aplicar).

1. Solo se mostrará una ventana de actividad, ya que solo hay un día entre las horas de inicio (2017-02-01) y fin (2017-02-02) de la canalización. Confirme que el estado del segmento de datos es **Ready** (Listo).

    ![Supervisar la canalización](media/data-factory-spark/monitor-and-manage-app.png)

1. En la lista de **ventanas de actividad**, seleccione una ejecución de actividad para ver información sobre ella. Si se produce un error, puede ver detalles sobre él en el panel derecho.

### <a name="verify-the-results"></a>Verificación de los resultados

1. Inicie Jupyter Notebook para el clúster de HDInsight Spark desde `https://CLUSTERNAME.azurehdinsight.net/jupyter`. También puede abrir un panel del clúster de HDInsight Spark y después iniciar Jupyter Notebook.

1. Seleccione **Nuevo** > **PySpark** para iniciar una nueva instancia de Notebook.

    ![Nuevo Jupyter Notebook](media/data-factory-spark/jupyter-new-book.png)

1. Ejecute el siguiente comando; para ello, copie y pegue el texto y presione Mayús + Entrar al final de la segunda instrucción:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Confirme que ve los datos de la tabla hvac.

    ![Resultados de la consulta Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Para obtener instrucciones detalladas, consulte la sección [Ejecución de una consulta de Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Solución de problemas
Puesto que establece getDebugInfo en **Siempre**, aparece una subcarpeta de registro en la carpeta pyFiles del contenedor de blobs. El archivo de registro en la carpeta de registro proporciona información adicional. Este archivo de registro es especialmente útil cuando se produce un error. En un entorno de producción, podría ser recomendable establecerlo en **Error**.

Para solucionar problemas, realice los pasos siguientes:


1. Ir a `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplicación de IU de YARN](media/data-factory-spark/yarnui-application.png)

1. Seleccione **Registros** para uno de los intentos de ejecución.

    ![Página de aplicación](media/data-factory-spark/yarn-applications.png)

1. Verá información adicional sobre el error en la página de registro:

    ![Error de registro](media/data-factory-spark/yarnui-application-error.png)

En las secciones siguientes se proporciona información sobre las entidades de factoría de datos para usar un clúster de Spark y una actividad de Spark en su factoría de datos.

## <a name="spark-activity-properties"></a>Propiedades de la actividad de Spark
Esta es la definición JSON de ejemplo de una canalización con una actividad de Spark:

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

En la siguiente tabla se describen las propiedades JSON que se usan en la definición de JSON.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| name | Nombre de la actividad en la canalización. | Sí |
| description | Texto que describe para qué se usa la actividad. | No |
| type | Esta propiedad debe establecerse en HDInsightSpark. | Sí |
| linkedServiceName | Nombre del servicio vinculado de HDInsight en el que se ejecuta el programa de Spark. | Sí |
| rootPath | Contenedor de blobs y carpeta que contiene el archivo de Spark. El nombre de archivo distingue entre mayúsculas y minúsculas. | Sí |
| entryFilePath | Ruta de acceso relativa a la carpeta raíz del código o el paquete de Spark. | Sí |
| className | Clase principal de Spark o Java de la aplicación. | No |
| argumentos | Lista de argumentos de línea de comandos del programa de Spark. | No |
| proxyUser | Cuenta de usuario de suplantación para ejecutar el programa de Spark. | No |
| sparkConfig | Especifique valores para las propiedades de configuración de Spark indicadas en el tema [Spark configuration: Application properties](https://spark.apache.org/docs/latest/configuration.html#available-properties) (Configuración de Spark: Propiedades de aplicación). | No |
| getDebugInfo | Especifica si se copian los archivos de registro de Spark en el almacenamiento que usa el clúster de HDInsight que especifica sparkJobLinkedService. Los valores permitidos son Ninguno, Siempre o Error. El valor predeterminado es Ninguno. | No |
| sparkJobLinkedService | El servicio vinculado de Storage que contiene los registros, las dependencias y los archivos de trabajos de Spark. Si no especifica un valor para esta propiedad, se usa el almacenamiento asociado con el clúster de HDInsight. | No |

## <a name="folder-structure"></a>Estructura de carpetas
La actividad de Spark no es compatible con un script en línea, al contrario que las actividades de Pig y Hive. Los trabajos de Spark también son más ampliable que los de Pig y Hive. En los trabajos de Spark, puede proporcionar varias dependencias como paquetes JAR (ubicados en la CLASSPATH de Java), archivos de Python (ubicados en la ruta PYTHONPATH) y cualquier otro archivo.

Cree la siguiente estructura de carpetas en la instancia de Blob Storage a la que hace referencia el servicio vinculado de HDInsight. Luego, cargue los archivos dependientes en las subcarpetas adecuadas de la carpeta raíz que representa **entryFilePath**. Por ejemplo, cargue los archivos de Python en la subcarpeta pyFiles y los archivos JAR en la subcarpeta jars de la carpeta raíz. En el entorno de tiempo de ejecución, el servicio Data Factory espera la siguiente estructura de carpetas en la instancia de Blob Storage:

| Path | Descripción | Obligatorio | Tipo |
| ---- | ----------- | -------- | ---- |
| . | Ruta de acceso raíz del trabajo de Spark en el servicio vinculado de almacenamiento. | Sí | Carpeta |
| &lt;Definida por el usuario&gt; | Ruta de acceso que apunta al archivo de entrada del trabajo de Spark. | Sí | Archivo |
| ./jars | Todos los archivos de esta carpeta se cargan y se colocan en la ruta CLASSPATH de Java del clúster. | No | Carpeta |
| ./pyFiles | Todos los archivos de esta carpeta se cargan y se colocan en la ruta PYTHONPATH del clúster. | No | Carpeta |
| ./files | Todos los archivos de esta carpeta se cargan y se colocan en el directorio de trabajo del ejecutor. | No | Carpeta |
| ./archives | Todos los archivos de esta carpeta están sin comprimir. | No | Carpeta |
| ./logs | Carpeta donde se almacenan los registros del clúster de Spark.| No | Carpeta |

Este es un ejemplo de un almacenamiento que contiene dos archivos de trabajos de Spark en la instancia de Blob Storage a la que hace referencia el servicio vinculado de HDInsight:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
