---
title: Puntuación de modelos de Machine Learning con PREDICT
description: Aprenda a puntuar modelos de Machine Learning con la función PREDICT de T-SQL en el grupo de SQL dedicado.
services: synapse-analytics
author: rothja
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: jroth
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 11e5f2e3ea46794367247ef9a1b4a0a43f7d6c1e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438316"
---
# <a name="score-machine-learning-models-with-predict"></a>Puntuación de modelos de Machine Learning con PREDICT

El grupo de SQL dedicado proporciona la capacidad de puntuar modelos de Machine Learning mediante el conocido lenguaje T-SQL. Con [PREDICT](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest) de T-SQL, puede traer los modelos de Machine Learning existentes entrenados con datos históricos y puntuarlos dentro de los límites de seguridad del almacenamiento de datos. La función PREDICT toma un modelo [ONNX (Open Neural Network Exchange)](https://onnx.ai/) y los datos como entradas. Esta característica elimina el paso de sacar datos valiosos del almacén de datos para realizar la puntuación. Su objetivo es permitir a los profesionales de datos implementar de manera sencilla los modelos de Machine Learning con la conocida interfaz de T-SQL, así como colaborar sin problemas con los científicos de datos que trabajan con el marco adecuado para su tarea.

> [!NOTE]
> Esta funcionalidad no se admite actualmente en el grupo de SQL sin servidor.

La funcionalidad requiere que el modelo se entrene fuera de Synapse SQL. Después de compilar el modelo, cárguelo en el almacenamiento de datos y puntúelo con la sintaxis de PREDICT de T-SQL para obtener información a partir de los datos.

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>Entrenamiento del modelo

El grupo de SQL dedicado espera un modelo previamente entrenado. Tenga en cuenta los factores siguientes al entrenar un modelo de Machine Learning que se usa para realizar predicciones en el grupo de SQL dedicado.

- El grupo de SQL dedicado solo admite modelos con formato ONNX. ONNX es un formato de modelo de código abierto que permite intercambiar modelos entre varios marcos para habilitar la interoperabilidad. Puede convertir los modelos existentes al formato ONNX con marcos que lo admitan de forma nativa o que hayan convertido los paquetes disponibles. Por ejemplo, el paquete [sklearn-onnx](https://github.com/onnx/sklearn-onnx) convierte los modelos scikit-learn a ONNX. El [repositorio ONNX de GitHub](https://github.com/onnx/tutorials#converting-to-onnx-format) proporciona una lista de ejemplos y marcos compatibles.

   Si usa [ML automatizado](../../machine-learning/concept-automated-ml.md) para entrenamiento, asegúrese de establecer el parámetro *enable_onnx_compatible_models* en TRUE para generar un modelo con formato ONNX. [Automated Machine Learning Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) muestra un ejemplo de cómo usar ML automatizado para crear un modelo de Machine Learning con formato ONNX.

- Los tipos de datos siguientes se admiten para los datos de entrada:
    - int, bigint, real, float
    - char, varchar, nvarchar

- Los datos de puntuación deben tener el mismo formato que los datos de entrenamiento. Los tipos de datos complejos, como las matrices multidimensionales, no son compatibles con PREDICT. Por lo tanto, para el entrenamiento, asegúrese de que cada entrada del modelo corresponda a una sola columna de la tabla de puntuación en lugar de pasar una matriz única que contenga todas las entradas.

- Asegúrese de que los nombres y los tipos de datos de las entradas del modelo coinciden con los nombres de columna y los tipos de datos de los datos de predicción nuevos. La visualización de un modelo ONNX con diversas herramientas de código abierto disponibles en línea puede ayudarlo con la depuración.

## <a name="loading-the-model"></a>Carga del modelo

El modelo se almacena en una tabla de usuario del grupo de SQL dedicado como una cadena hexadecimal. Se pueden agregar columnas adicionales como Identificador y Descripción en la tabla del modelo para identificar el modelo. Use varbinary(max) como el tipo de datos de la columna del modelo. A continuación, se muestra un ejemplo de código para una tabla que se puede usar para almacenar modelos:

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

Una vez que el modelo se convierte en una cadena hexadecimal y se especifica la definición de la tabla, use el [comando COPY](/sql/t-sql/statements/copy-into-transact-sql?preserve-view=true&view=azure-sqldw-latest) o Polybase para cargar el modelo en la tabla del grupo de SQL dedicado. En el ejemplo de código siguiente se usa el comando Copy para cargar el modelo.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>Puntuación del modelo

Una vez que se cargan el modelo y los datos en el almacenamiento de datos, use la función **PREDICT de T-SQL** para puntuar el modelo. Asegúrese de que los datos de entrada nuevos están en el mismo formato que los datos de entrenamiento que se usan para generar el modelo. PREDICT de T-SQL toma dos entradas: el modelo y los nuevos datos de entrada de puntuación, y genera columnas nuevas para la salida. El modelo se puede especificar como una variable, un literal o una subconsulta escalar. Use [WITH common_table_expression](/sql/t-sql/queries/with-common-table-expression-transact-sql?preserve-view=true&view=azure-sqldw-latest) para especificar un conjunto de resultados con nombre para el parámetro de datos.

En el ejemplo siguiente se muestra una consulta de ejemplo que usa la función de predicción. Se crea una columna adicional con el nombre *Score* y el tipo de datos *float* que contiene los resultados de la predicción. Todas las columnas de datos de entrada, así como las columnas de predicción de salida, se pueden mostrar con la instrucción SELECT. Para más detalles, consulte [PREDICT (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest).

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d, RUNTIME = ONNX) WITH (Score float) AS p;
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la función PREDICT, consulte [PREDICT (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest).