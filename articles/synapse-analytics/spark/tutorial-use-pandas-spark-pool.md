---
title: 'Tutorial: Uso de Pandas para leer y escribir datos de ADLS en un grupo de Apache Spark sin servidor en Synapse Analytics'
description: Tutorial sobre cómo usar Pandas en un cuaderno de PySpark para leer y escribir datos de ADLS en un grupo de Apache Spark sin servidor.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4f977ad6a6cc38a269eedbe3e107b166bd74a4f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030449"
---
# <a name="tutorial-use-pandas-to-readwrite-azure-data-lake-storage-gen2-data-in-serverless-apache-spark-pool-in-synapse-analytics"></a>Tutorial: Uso de Pandas para leer y escribir datos de Azure Data Lake Storage Gen2 en un grupo de Apache Spark sin servidor en Synapse Analytics

Aprenda a usar Pandas para leer y escribir datos en Azure Data Lake Storage Gen2 (ADLS) mediante un grupo de Apache Spark sin servidor en Azure Synapse Analytics. En los ejemplos de este tutorial se muestra cómo leer datos CSV con Pandas en Synapse, así como archivos de Excel y Parquet.

En este tutorial, aprenderá a:

   > [!div class="checklist"]
   > - Leer y escribir datos de ADLS Gen2 mediante Pandas en una sesión de Spark.

Si no tiene una suscripción a Azure, [cree una cuenta gratuita antes de empezar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerrequisitos

- [Área de trabajo de Azure Synapse Analytics](../get-started-create-workspace.md) con una cuenta de almacenamiento de Azure Data Lake Storage Gen2 que esté configurada como almacenamiento predeterminado (o almacenamiento principal). Asegúrese de que es el *colaborador de datos de Storage Blob* en el sistema de archivos de Data Lake Storage Gen2 con el que trabaja.
- Un grupo de Apache Spark sin servidor en el área de trabajo de Azure Synapse Analytics. Para más información, consulte el artículo sobre [creación de un grupo de Spark en Azure Synapse](../get-started-analyze-spark.md).
- Configure la cuenta [secundaria de Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md) (que no es la predeterminada del área de trabajo de Synapse). Asegúrese de que es el *colaborador de datos de Storage Blob* en el sistema de archivos de Data Lake Storage Gen2 con el que trabaja.
- Cree servicios vinculados: en Azure Synapse Analytics, un servicio vinculado define la información de conexión al servicio. En este tutorial, agregará un servicio vinculado de Azure Synapse Analytics y Azure Data Lake Storage Gen2.

   1. Abra Azure Synapse Studio y seleccione la pestaña **Administrar**.
   1. En **Conexiones externas**, seleccione **Servicios vinculados**.
   1. Para agregar un servicio vinculado, seleccione **Nuevo**.
   1. Seleccione el icono de Azure Data Lake Storage Gen2 de la lista y seleccione **Continuar**.
   1. Especifique las credenciales de autenticación. La clave de cuenta, la entidad de servicio (SP), las credenciales y la identidad de servicio administrada (MSI) son actualmente tipos de autenticación admitidos. Asegúrese de que el rol *Colaborador de datos de Storage Blob* esté asignado en el almacenamiento para SP y MSI antes de elegirlo para la autenticación. **Pruebe la conexión** para ver si las credenciales sean correctas. Seleccione **Crear**.

   :::image type="content" source="media/tutorial-use-pandas-spark-pool/create-adls-linked-service.png" alt-text="Captura de pantalla de la creación de un servicio vinculado mediante una clave de acceso de ADLS Gen2.":::


> [!NOTE]
> - La característica de Pandas se admite en el grupo de Apache Spark para **Python 3.8** y **Spark3** sin servidor de Azure Synapse Analytics. 
> - Compatibilidad disponible con las siguientes versiones: **pandas 1.2.3, fsspec 2021.10.0, adlfs 0.7.7**
> - Debe tener funcionalidades para admitir tanto el **URI de Azure Data Lake Storage Gen2** (abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path) como la **dirección URL corta de FSSPEC** (abfs[s]://container_name/file_path).


## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).


## <a name="readwrite-data-to-default-adls-storage-account-of-synapse-workspace"></a>Lectura y escritura de datos en la cuenta de almacenamiento de ADLS predeterminada del área de trabajo de Synapse

Pandas puede leer y escribir datos de ADLS especificando directamente la ruta de acceso del archivo.

Ejecute el código siguiente:

   > [!NOTE]
   > Actualice la dirección URL del archivo en este script antes de ejecutarlo.

   ```PYSPARK
      #Read data file from URI of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://container_name/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path')
   ```

## <a name="readwrite-data-using-secondary-adls-account"></a>Lectura y escritura de datos mediante una cuenta secundaria de ADLS

Pandas puede leer y escribir datos de cuentas secundarias de ADLS:
   - mediante el servicio vinculado (con opciones de autenticación: clave de cuenta de almacenamiento, entidad de servicio, identidad de servicio administrada y credenciales);
   - mediante opciones de almacenamiento para pasar directamente el identificador de cliente y el secreto; la clave SAS, la clave de la cuenta de almacenamiento y la cadena de conexión.

**Uso del servicio vinculado**

Ejecute el código siguiente:

   > [!NOTE]
   > Actualice la dirección URL del archivo y el nombre del servicio vinculado de este script antes de ejecutarlo.

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'linked_service' : 'linked_service_name'})
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options =       {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'linked_service' :    'linked_service_name'})
   ```


**Uso de opciones de almacenamiento** para pasar directamente el identificador y el secreto de cliente, la clave SAS, la clave de la cuenta de almacenamiento y la cadena de conexión.

Ejecute el código siguiente:

   > [!NOTE]
   > Actualice la dirección URL del archivo y storage_options en este script antes de ejecutarlo.

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```


## <a name="example-to-readwrite-parquet-file"></a>Ejemplo de archivo Parquet de lectura y escritura 

Ejecute el código siguiente:

   > [!NOTE]
   > Actualice la dirección URL del archivo en este script antes de ejecutarlo.

   ```PYSPARK
      import pandas
      
      #read parquet file
      df = pandas.read_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
      print(df)
      
      #write parquet file
      df.to_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
   ```

## <a name="example-to-readwrite-excel-file"></a>Ejemplo de archivo de Excel de lectura y escritura 

Ejecute el código siguiente:

   > [!NOTE]
   > Actualice la dirección URL del archivo en este script antes de ejecutarlo.

   ```PYSPARK
      import pandas
      
      #read excel file
      df = pandas.read_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   excel_file_path')
      print(df)
      
      #write excel file
      df.to_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/excel_file_path')
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Azure Synapse Analytics](../index.yml)
