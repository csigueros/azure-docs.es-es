---
title: Uso de flujos de datos para procesar datos de modelos de aprendizaje automático automatizado (AutoML)
description: Aprenda a usar flujos de datos de Azure Data Factory para procesar los datos de los modelos de aprendizaje automático automatizado (AutoML).
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.subservice: tutorials
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: a652ac797739323530dee169987a135c8abdf0f8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638958"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Procesamiento de datos de modelos de aprendizaje automático automatizado mediante flujos de datos

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Los proyectos de aprendizaje automático adoptan el aprendizaje automático automatizado (AutoML) para entrenar, ajustar y obtener los mejores modelos automáticamente mediante las métricas de destino que especifica para la clasificación, la regresión y la previsión de series temporales.

Uno de los desafíos de AutoML es que los datos sin procesar de un almacenamiento de datos o una base de datos transaccional serían un conjunto de datos enorme, posiblemente de 10 GB. Un conjunto de datos grande requiere más tiempo para entrenar modelos, por lo que se recomienda optimizar el procesamiento de los datos antes de entrenar los modelos de Azure Machine Learning. En este tutorial se explica cómo usar Azure Data Factory para particionar un conjunto de datos en archivos de AutoML para un conjunto de datos de Machine Learning.

El proyecto de AutoML incluye los tres escenarios de procesamiento de datos siguientes:

* Particione los datos grandes en archivos de AutoML antes de entrenar los modelos.

     El [marco de datos de Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) se usa normalmente para procesar los datos antes de entrenar los modelos. La trama de datos de Pandas funciona bien para tamaños de datos inferiores a 1 GB, pero si los datos tienen un tamaño superior a 1 GB, una trama de datos de Pandas se ralentiza para procesar los datos. En ocasiones, incluso puede recibir un mensaje de error de memoria insuficiente. Se recomienda usar un formato de [archivo Parquet](https://parquet.apache.org/) para el aprendizaje automático porque es un formato de columnas binarias.
    
     Los flujos de datos de asignación de Data Factory son transformaciones de datos diseñadas visualmente que liberan a los ingenieros de datos de la escritura de código. Los flujos de datos de asignación son una manera eficaz de procesar datos grandes, ya que la canalización usa clústeres de Spark escalados horizontalmente.

* Divida el conjunto de datos de entrenamiento y el conjunto de datos de prueba.
    
    El conjunto de datos de entrenamiento se utilizará para un modelo de entrenamiento. El conjunto de datos de prueba se usará para evaluar los modelos en un proyecto de aprendizaje automático. La actividad de división condicional de flujos de datos de asignación dividiría los datos de entrenamiento y los datos de prueba.

* Quite los datos no cualificados.

    Es posible que quiera quitar los datos no cualificados, como un archivo Parquet con filas de ceros. En este tutorial, usaremos la actividad de agregado para obtener un recuento del número de filas. El recuento de filas será una condición para quitar datos no cualificados.

## <a name="preparation"></a>Preparación

Use la tabla de Azure SQL Database siguiente.

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Conversión del formato de datos a Parquet

El flujo de datos siguiente convertirá una tabla de SQL Database a un formato de archivo Parquet:

- **Conjunto de datos de origen**: tabla de transacciones de Azure SQL Database.
- **Conjunto de datos de receptor**: almacenamiento de blobs con formato Parquet.

## <a name="remove-unqualified-data-based-on-row-count"></a>Eliminación de datos no cualificados según el recuento de filas

Supongamos que necesitamos quitar un recuento de filas inferior a dos.

1. Use la actividad de agregado para obtener un recuento del número de filas. Utilice **Agrupado por** en función de Col2 y **Agregados** con `count(1)` para el recuento de filas.

    ![Captura de pantalla que muestra la configuración de la actividad de agregado para obtener un recuento del número de filas.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Use la actividad Receptor y seleccione el **tipo de receptor** **Caché** en la pestaña **Receptor**. A continuación, seleccione la columna deseada en la lista desplegable **Columnas clave** de la pestaña **Configuración**.

    ![Captura de pantalla que muestra la configuración de la actividad CacheSink para obtener un recuento del número de filas de un receptor almacenado en caché.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Use la actividad Columna derivada para agregar una columna de recuento de filas en el flujo de origen. En la pestaña **Configuración de Columna derivada**, use la expresión `CacheSink#lookup` para obtener un recuento de filas de CacheSink.

    ![Captura de pantalla que muestra la configuración de la actividad Columna derivada para agregar un recuento del número de filas en origen1.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Use la actividad División condicional para quitar los datos no cualificados. En este ejemplo, el recuento de filas se basa en la columna Col2. La condición es quitar un recuento de filas menor que dos, por lo que se quitarán dos filas (Id.=2 e Id.=7). Los datos no cualificados se guardarían en un almacenamiento de blobs para la administración de datos.

    ![Captura de pantalla que muestra la configuración de la actividad División condicional para obtener datos mayores o iguales que dos.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Cree un nuevo origen para obtener un recuento de filas que se usará en el origen inicial en los pasos posteriores.
>    * Use CacheSink desde un punto de vista de rendimiento.

## <a name="split-training-data-and-test-data"></a>División de datos de entrenamiento y datos de prueba

Queremos dividir los datos de entrenamiento y los datos de prueba para cada partición. En este ejemplo, para el mismo valor de Col2, obtenga las dos primeras filas como datos de prueba y las filas restantes como datos de entrenamiento.

1. Use la actividad Ventana para agregar un número de columna o fila para cada partición. En la pestaña **Over** (Por encima), seleccione una columna para la partición. En este tutorial, se creará una partición para Col2. Asigne un orden en la pestaña **Ordenar**, que en este tutorial se basará en el identificador. Asigne un orden en la pestaña **Window columns** (Columnas de la ventana) para agregar una columna como un número de fila para cada partición.

    ![Captura de pantalla que muestra la configuración de la actividad Ventana para agregar una nueva columna que es el número de fila.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Utilice la actividad División condicional para dividir las dos primeras filas de cada partición en el conjunto de datos de prueba y el resto de las filas en el conjunto de datos de entrenamiento. En la pestaña **Configuración de División condicional**, utilice la expresión `lesserOrEqual(RowNum,2)` como condición.

    ![Captura de pantalla que muestra la configuración de la actividad División condicional para dividir el conjunto de datos actual en el conjunto de datos de entrenamiento y el conjunto de datos de prueba.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Partición de los conjuntos de datos de entrenamiento y prueba con el formato Parquet

Use la actividad Receptor en la pestaña **Optimizar** con **Unique value per partition** (Valor único por partición) para establecer una columna como clave de columna para la partición.

![Captura de pantalla que muestra la configuración de la actividad Receptor para establecer la partición del conjunto de datos de entrenamiento.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Echemos un vistazo a toda la lógica de la canalización.

![Captura de pantalla que muestra la lógica de toda la canalización.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Pasos siguientes

Compile el resto de la lógica del flujo de datos mediante [transformaciones](concepts-data-flow-overview.md) de flujo de datos de asignación.
