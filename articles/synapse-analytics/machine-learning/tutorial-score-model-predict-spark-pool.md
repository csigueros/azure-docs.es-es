---
title: 'Tutorial: puntuación de modelos de Machine Learning con PREDICT en grupos de Apache Spark sin servidor'
description: Obtenga información sobre cómo usar la funcionalidad PREDICT en grupos de Apache Spark sin servidor para predecir puntuaciones a través de modelos de Machine Learning.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 45338fa4aed963d2172af25fec680e2017c98e88
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549895"
---
# <a name="tutorial-score-machine-learning-models-with-predict-in-serverless-apache-spark-pools"></a>Tutorial: puntuación de modelos de Machine Learning con PREDICT en grupos de Apache Spark sin servidor

Obtenga información sobre cómo usar la funcionalidad PREDICT en grupos de Apache Spark sin servidor en Azure Synapse Analytics para la predicción de puntuación. Puede usar un modelo entrenado registrado en Azure Machine Learning (AML) o en la instancia predeterminada de Azure Data Lake Storage (ADLS) en el área de trabajo de Synapse.

PREDICT en un cuaderno de Synapse PySpark proporciona la capacidad de puntuar modelos de Machine Learning mediante el lenguaje SQL, las funciones definidas por el usuario (UDF) o transformadores. Con PREDICT, puede traer los modelos de Machine Learning existentes entrenados fuera de Synapse y registrados en Azure Data Lake Storage Gen2 o Azure Machine Learning, para puntuar los datos históricos dentro de los límites seguros de Azure Synapse Analytics. La función PREDICT toma un modelo y datos como entradas. Esta característica elimina el paso de sacar datos valiosos de Synapse para realizar la puntuación. El objetivo es empoderar a los consumidores de modelos para que infieran fácilmente los modelos de Machine Learning en Synapse, así como colaborar sin problemas con los productores de modelos que trabajan con el marco adecuado para su tarea.


En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Predecir puntuaciones de datos en un grupo de Apache Spark sin servidor mediante modelos de aprendizaje automático que se entrenan fuera de Synapse y se registran en Azure Machine Learning o Azure Data Lake Storage Gen2.

Si no tiene una suscripción a Azure, [cree una cuenta gratuita antes de empezar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Requisitos previos

- Necesitará un [área de trabajo de Azure Synapse Analytics](../get-started-create-workspace.md) con una cuenta de almacenamiento de Azure Data Lake Storage Gen2 que esté configurada como almacenamiento predeterminado. Asegúrese de que es el *colaborador de datos de Storage Blob* en el sistema de archivos de Data Lake Storage Gen2 con el que trabaja.
- Un grupo de Apache Spark sin servidor en el área de trabajo de Azure Synapse Analytics. Para más información, consulte el artículo sobre [creación de un grupo de Spark en Azure Synapse](../get-started-analyze-spark.md).
- El área de trabajo de Azure Machine Learning es necesaria si desea entrenar o registrar el modelo en Azure Machine Learning. Para obtener más detalles, consulte [Administración de áreas de trabajo de Azure Machine Learning en el portal o con el SDK de Python](../../machine-learning/how-to-manage-workspace.md).
- Si el modelo está registrado en Azure Machine Learning, necesita un servicio vinculado. En Azure Synapse Analytics, un servicio vinculado define la información de conexión al servicio. En este tutorial, agregará un servicio vinculado de Azure Synapse Analytics y de Azure Machine Learning. Para obtener más información, consulte [Creación de un servicio vinculado de Azure Machine Learning en Synapse](quickstart-integrate-azure-machine-learning.md).
- La funcionalidad PREDICT requiere que ya tenga un modelo entrenado que esté registrado en Azure Machine Learning o cargado en Azure Data Lake Storage Gen2.

> [!NOTE]
> - La característica PREDICT se admite en el grupo de Apache Spark para **Spark3** sin servidor de Azure Synapse Analytics. **Python 3.8** es la versión recomendada para la creación y el entrenamiento de modelos. 
> - PREDICT admite la mayoría de los paquetes de modelos de aprendizaje automático en formato **MLflow**: **TensorFlow, ONNX, PyTorch, SkLearn y pyfunc** se admiten en esta versión preliminar.
> - PREDICT admite el origen de los modelos **AML y ADLS**. Aquí, la cuenta de ADLS hace referencia a la **cuenta predeterminada de ADLS del área de trabajo de Synapse**. 


## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).


## <a name="use-predict-for-mlflow-packaged-models"></a>Uso de PREDICT para los modelos empaquetados de MLFLOW

Asegúrese de que se cumplen todos los requisitos previos antes de seguir estos pasos para usar PREDICT.

1. **Importar bibliotecas:** importe las bibliotecas siguientes para usar PREDICT en la sesión de Spark.

   ```python
      #Import libraries
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   ```

2. **Establecer parámetros mediante variables:** la ruta de acceso de datos de Synapse ADLS y el URI del modelo deben establecerse mediante variables de entrada. También debe definir el entorno de ejecución que es "mlflow" y el tipo de datos de la devolución de salida del modelo. Tenga en cuenta que todos los tipos de datos que se admiten en PySpark también se admiten a través de PREDICT.

   > [!NOTE]
   > Antes de ejecutar este script, actualícelo con el URI de un archivo de datos de ADLS Gen2 junto con el tipo de datos devuelto de salida del modelo y el URI de ADLS/AML para el archivo modelo.

   ```python
      #Set input data path
      DATA_FILE = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<file path>"
   
      #Set model URI
          #Set AML URI, if trained model is registered in AML
             AML_MODEL_URI = "<aml model uri>" #In URI ":x" signifies model version in AML. You can   choose which model version you want to run. If ":x" is not provided then by default   latest version will be picked.
   
          #Set ADLS URI, if trained model is uploaded in ADLS
             ADLS_MODEL_URI = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<model   mlflow folder path>"
   
      #Define model return type
      RETURN_TYPES = "<data_type>" # for ex: int, float etc. PySpark data types are supported
   
      #Define model runtime. This supports only mlflow
      RUNTIME = "mlflow"
   ```

3. **Formas de autenticarse en el área de trabajo de AML:** si el modelo se almacena en la cuenta predeterminada de ADLS del área de trabajo de Synapse, no necesita ninguna configuración de autenticación adicional. Si el modelo está registrado en Azure Machine Learning, puede elegir cualquiera de las dos formas de autenticación admitidas siguientes.

   > [!NOTE]
   > Actualice la información de inquilino, cliente, suscripción, grupo de recursos, área de trabajo de AML y servicio vinculado en este script antes de ejecutarlo.

   - **A través de la entidad de servicio:** puede usar el secreto y el identificador de cliente de la entidad de servicio directamente para autenticarse en el área de trabajo de AML. La entidad de servicio debe tener acceso de "Colaborador" al área de trabajo de AML.

      ```python
         #AML workspace authentication using service principal
         AZURE_TENANT_ID = "<tenant_id>"
         AZURE_CLIENT_ID = "<client_id>"
         AZURE_CLIENT_SECRET = "<client_secret>"
  
         AML_SUBSCRIPTION_ID = "<subscription_id>"
         AML_RESOURCE_GROUP = "<resource_group_name>"
         AML_WORKSPACE_NAME = "<aml_workspace_name>"
  
         svc_pr = ServicePrincipalAuthentication( 
             tenant_id=AZURE_TENANT_ID,
             service_principal_id=AZURE_CLIENT_ID,
             service_principal_password=AZURE_CLIENT_SECRET
         )
  
         ws = Workspace(
             workspace_name = AML_WORKSPACE_NAME,
             subscription_id = AML_SUBSCRIPTION_ID,
             resource_group = AML_RESOURCE_GROUP,
             auth=svc_pr
         )
      ```

   - **A través del servicio vinculado:** puede usar el servicio vinculado para autenticarse en el área de trabajo de AML. El servicio vinculado puede usar la "entidad de servicio" o la "identidad de servicio administrada (MSI)" del área de trabajo de Synapse para la autenticación. "Entidad de servicio" o "Identidad de servicio administrada (MSI)" debe tener acceso de "Colaborador" al área de trabajo de AML.

      ```python
         #AML workspace authentication using linked service
         from notebookutils.mssparkutils import azureML
         ws = azureML.getWorkspace("<linked_service_name>") #   "<linked_service_name>" is the linked service name, not AML workspace name. Also, linked   service supports MSI and service principal both
      ```

4. **Habilite PREDICT en la sesión de Spark:** establezca la configuración de Spark de `spark.synapse.ml.predict.enabled` a `true`para habilitar la biblioteca.

   ```python
      #Enable SynapseML predict
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

5. **Enlazar modelo en la sesión de Spark:** enlace el modelo con las entradas necesarias para que se pueda hacer referencia al modelo en la sesión de Spark. Defina también alias para que pueda usar el mismo alias en la llamada de PREDICT.

   > [!NOTE]
   > Actualice el alias del modelo y el URI del modelo en este script antes de ejecutarlo.

   ```python
      #Bind model within Spark session
       model = pcontext.bind_model(
        return_types=RETURN_TYPES, 
        runtime=RUNTIME, 
        model_alias="<random_alias_name>", #This alias will be used in PREDICT call to refer  this   model
        model_uri=ADLS_MODEL_URI, #In case of AML, it will be AML_MODEL_URI
        aml_workspace=ws #This is only for AML. In case of ADLS, this parameter can be removed
        ).register()
   ```

6. **Leer datos de ADLS:** lea datos de ADLS. Cree un dataframe de Spark y una vista sobre la trama de datos.

   > [!NOTE]
   > Actualice el nombre de la vista en este script antes de ejecutarlo.

   ```python
      #Read data from ADLS
      df = spark.read \
       .format("csv") \
       .option("header", "true") \
       .csv(DATA_FILE,
           inferSchema=True)
      df.createOrReplaceTempView('<view_name>')
   ```

7. **Generar puntuación mediante PREDICT:** puede llamar a PREDICT de tres maneras: mediante la API de SQL de Spark, mediante la función de definición de usuario (UDF) y la API de Transformer. A continuación, se muestran algunos ejemplos.

   > [!NOTE]
   > Actualice el nombre del alias del modelo, el nombre de vista y el nombre de la columna de entrada del modelo separado por una coma en este script antes de ejecutarlo. Las columnas de entrada del modelo separadas por comas son las mismas que las que se usan al entrenar el modelo.

   ```python
      #Call PREDICT using Spark SQL API
   
      predictions = spark.sql(
                     """
                         SELECT PREDICT('<random_alias_name>',
                                   <comma_separated_model_input_column_name>) AS predict 
                         FROM <view_name>
                     """
                 ).show()
   ```

   ```python
      #Call PREDICT using user defined function (UDF)
   
      df = df[<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
   
      df.withColumn("PREDICT",model.udf(lit("<random_alias_name>"),*df.columns)).show()
   ```

   ```python
      #Call PREDICT using Transformer API
      
      columns = [<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
      
      tranformer = model.create_transformer().setInputCols(columns).setOutputCol("PREDICT")
   
      tranformer.transform(df).show()
   ```

## <a name="sklearn-example-using-predict"></a>Ejemplo de Sklearn con PREDICT

1. Importe bibliotecas y lea el conjunto de datos de entrenamiento de ADLS.

   ```python
      # Import libraries and read training dataset from ADLS
      
      import fsspec
      import pandas
      from fsspec.core import split_protocol
      
      adls_account_name = 'xyz' #Provide exact ADLS account name
      adls_account_key = 'xyz' #Provide exact ADLS account key
      
      fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>',      account_name=adls_account_name, account_key=adls_account_key)
      
      with fsspec_handle.open() as f:
          train_df = pandas.read_csv(f)
   ```

1. Entrene el modelo y genere artefactos de mlflow.

   ```python
      # Train model and generate mlflow artifacts
   
      import os
      import shutil
      import mlflow
      import json
      from mlflow.utils import model_utils
      import numpy as np
      import pandas as pd
      from sklearn.linear_model import LinearRegression
      
      
      class LinearRegressionModel():
        _ARGS_FILENAME = 'args.json'
        FEATURES_KEY = 'features'
        TARGETS_KEY = 'targets'
        TARGETS_PRED_KEY = 'targets_pred'
      
        def __init__(self, fit_intercept, nb_input_features=9, nb_output_features=1):
          self.fit_intercept = fit_intercept
          self.nb_input_features = nb_input_features
          self.nb_output_features = nb_output_features
      
        def get_args(self):
          args = {
              'nb_input_features': self.nb_input_features,
              'nb_output_features': self.nb_output_features,
              'fit_intercept': self.fit_intercept
          }
          return args
      
        def create_model(self):
          self.model = LinearRegression(fit_intercept=self.fit_intercept)
      
        def train(self, dataset):
      
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
      
          targets = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.TARGETS_KEY])], axis=0)
      
      
          self.model.fit(features, targets)
      
        def predict(self, dataset):
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
          targets_pred = self.model.predict(features)
          return targets_pred
      
        def save(self, path):
          if os.path.exists(path):
            shutil.rmtree(path)
      
          # save the sklearn model with mlflow
          mlflow.sklearn.save_model(self.model, path)
      
          # save args
          self._save_args(path)
      
        def _save_args(self, path):
          args_filename = os.path.join(path, LinearRegressionModel._ARGS_FILENAME)
          with open(args_filename, 'w') as f:
            args = self.get_args()
            json.dump(args, f)
      
      
      def train(train_df, output_model_path):
        print(f"Start to train LinearRegressionModel.")
      
        # Initialize input dataset
        dataset = train_df.to_numpy()
        datasets = {}
        datasets['targets'] = dataset[:, -1]
        datasets['features'] = dataset[:, :9]
      
        # Initialize model class obj
        model_class = LinearRegressionModel(fit_intercept=10)
        with mlflow.start_run(nested=True) as run:
          model_class.create_model()
          model_class.train(datasets)
          model_class.save(output_model_path)
          print(model_class.predict(datasets))
      
      
      train(train_df, './artifacts/output')
   ```

1. Almacene los artefactos MLFLOW del modelo en ADLS o regístrese en AML.

   ```python
      # Store model MLFLOW artifacts in ADLS
      
      STORAGE_PATH = 'abfs[s]://<container>/<path-to-store-folder>'
      
      protocol, _ = split_protocol(STORAGE_PATH)
      print (protocol)
   
      storage_options = {
          'account_name': adls_account_name,
          'account_key': adls_account_key
      }
      fs = fsspec.filesystem(protocol, **storage_options)
      fs.put(
          './artifacts/output',
          STORAGE_PATH, 
          recursive=True, overwrite=True)
   ```

   ```python
      # Register model MLFLOW artifacts in AML
      
      from azureml.core import Workspace, Model
      from azureml.core.authentication import ServicePrincipalAuthentication
      
      AZURE_TENANT_ID = "xyz"
      AZURE_CLIENT_ID = "xyz"
      AZURE_CLIENT_SECRET = "xyz"
      
      AML_SUBSCRIPTION_ID = "xyz"
      AML_RESOURCE_GROUP = "xyz"
      AML_WORKSPACE_NAME = "xyz"
      
      svc_pr = ServicePrincipalAuthentication( 
          tenant_id=AZURE_TENANT_ID,
          service_principal_id=AZURE_CLIENT_ID,
          service_principal_password=AZURE_CLIENT_SECRET
      )
      
      ws = Workspace(
          workspace_name = AML_WORKSPACE_NAME,
          subscription_id = AML_SUBSCRIPTION_ID,
          resource_group = AML_RESOURCE_GROUP,
          auth=svc_pr
      )
      
      model = Model.register(
          model_path="./artifacts/output",
          model_name="xyz",
          workspace=ws,
      )
   ```

1. Establezca los parámetros necesarios mediante variables.

   ```python
      # If using ADLS uploaded model
   
      import pandas as pd
      from pyspark.sql import SparkSession
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      ADLS_MODEL_URI_SKLEARN = "abfss://xyz@xyz.dfs.core.windows.net/mlflow/sklearn/     e2e_linear_regression/"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

   ```python
      # If using AML registered model
   
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      AML_MODEL_URI_SKLEARN = "aml://xyz"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

1. Habilite la funcionalidad PREDICT de SynapseML en la sesión de Spark.

   ```python
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

1. Enlace del modelo en la sesión de Spark.

   ```python
      # If using ADLS uploaded model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=ADLS_MODEL_URI_SKLEARN,
       ).register()
   ```

   ```python
      # If using AML registered model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=AML_MODEL_URI_SKLEARN,
       aml_workspace=ws
       ).register()
   ```

1. Carga de datos de prueba desde ADLS.

   ```python
      # Load data from ADLS
   
      df = spark.read \
          .format("csv") \
          .option("header", "true") \
          .csv(DATA_FILE,
              inferSchema=True)
      df = df.select(df.columns[:9])
      df.createOrReplaceTempView('data')
      df.show(10)
   ```

1. Llame a PREDICT para generar la puntuación.

   ```python
      # Call PREDICT
      
      predictions = spark.sql(
                        """
                            SELECT PREDICT('sklearn_linear_regression', *) AS predict FROM data
                        """
                    ).show()
   ```


## <a name="next-steps"></a>Pasos siguientes

- [Funcionalidades de aprendizaje automático en Azure Synapse Analytics](what-is-machine-learning.md)
