---
title: 'Tutorial: Uso de FSSPEC para leer y escribir datos de ADLS en un grupo de Apache Spark sin servidor en Synapse Analytics'
description: Tutorial sobre cómo usar FSSPEC en un cuaderno de PySpark para leer y escribir datos de ADLS en un grupo de Apache Spark sin servidor.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 742aeca5df76558b6dcd81ceb184029f807a1a65
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030450"
---
# <a name="tutorial-use-fsspec-to-readwrite-adls-data-in-serverless-apache-spark-pool-in-synapse-analytics"></a>Tutorial: Uso de FSSPEC para leer y escribir datos de ADLS en un grupo de Apache Spark sin servidor en Synapse Analytics

Aprenda a usar Filesystem Spec (FSSPEC) para leer y escribir datos en Azure Data Lake Storage (ADLS) mediante un servicio vinculado de un grupo de Apache Spark sin servidor en Azure Synapse Analytics.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Leer y escribir datos de ADLS en una sesión de Spark dedicada.

Si no tiene una suscripción a Azure, [cree una cuenta gratuita antes de empezar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Requisitos previos

- Necesitará un [área de trabajo de Azure Synapse Analytics](../get-started-create-workspace.md) con una cuenta de almacenamiento de Azure Data Lake Storage Gen2 que esté configurada como almacenamiento predeterminado. Asegúrese de que es el *colaborador de datos de Storage Blob* en el sistema de archivos de Data Lake Storage Gen2 con el que trabaja.
- Grupo de Spark en el área de trabajo de Azure Synapse Analytics. Para más información, consulte el artículo sobre [creación de un grupo de Spark en Azure Synapse](../get-started-analyze-spark.md).

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-linked-services"></a>Crear servicios vinculados

En Azure Synapse Analytics, un servicio vinculado es donde se define la información de conexión a otros servicios. En esta sección, agregará un servicio vinculado de Azure Synapse Analytics y Azure Data Lake Storage Gen2.

1. Abra Azure Synapse Studio y seleccione la pestaña **Administrar**.
1. En **Conexiones externas**, seleccione **Servicios vinculados**.
1. Para agregar un servicio vinculado, seleccione **Nuevo**.
1. Seleccione el icono de Azure Data Lake Storage Gen2 de la lista y seleccione **Continuar**.
1. Especifique las credenciales de autenticación. La clave de cuenta es el tipo de autenticación admitido actualmente. Seleccione **Probar conexión** para comprobar que las credenciales son correctas. Seleccione **Crear**.

   :::image type="content" source="media/tutorial-spark-pool-filesystem-spec/create-adls-linked-service.png" alt-text="Captura de pantalla de la creación de un servicio vinculado mediante una clave de acceso de ADLS Gen2.":::


## <a name="readwrite-data-using-storage-account-name-and-key"></a>Lectura y escritura de datos mediante el nombre y la clave de la cuenta de almacenamiento

FSSPEC puede leer y escribir datos de ADLS mediante la especificación directa del nombre y la clave de la cuenta de almacenamiento.

1. En Synapse Studio, abra **Data** > **Linked** > **Azure Data Lake Storage Gen2** (Datos > Vinculados > Azure Data Lake Storage Gen2). Cargue los datos en la cuenta de almacenamiento predeterminada.

1. Ejecute el código siguiente:

   > [!NOTE]
   > Antes de ejecutar este script, actualice la dirección URL del archivo y el nombre y la clave de almacenamiento de ADLS Gen2.

   ```PYSPARK
   # To read data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name
   adls_account_key = '' #Provide exact ADLS account key

   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key)

   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name 
   adls_account_key = '' #Provide exact ADLS account key 
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   
   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key, mode="wt")
   
   with fsspec_handle.open() as f:
    data.to_csv(f)
   ```

## <a name="readwrite-data-using-linked-service"></a>Lectura y escritura de datos mediante un servicio vinculado

FSSPEC puede leer y escribir datos de ADLS mediante la especificación del nombre del servicio vinculado.


1. En Synapse Studio, abra **Data** > **Linked** > **Azure Data Lake Storage Gen2** (Datos > Vinculados > Azure Data Lake Storage Gen2). Cargue los datos en la cuenta de almacenamiento predeterminada.

1. Ejecute el código siguiente:

   > [!NOTE]
   > Antes de ejecutar este script, actualice la dirección URL del archivo, el nombre del servicio vinculado y el nombre de ADLS Gen2.

   ```PYSPARK
   # To read data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>)
   
   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key)
   
   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>) 
   
   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key, mode="wt") 
   
   with fsspec_handle.open() as f:
       data.to_csv(f) 
   ```

## <a name="upload-file-from-local-file-system-to-default-adls-storage-account-of-synapse-workspace"></a>Cargue el archivo desde el sistema de archivos local en la cuenta de almacenamiento predeterminada de ADLS del área de trabajo de Synapse

FSSPEC puede cargar un archivo desde el sistema de archivos local en una cuenta de almacenamiento de ADLS predeterminada del área de trabajo de Synapse.


Ejecute el código siguiente:

   > [!NOTE]
   > Actualice la dirección URL del archivo en este script antes de ejecutarlo.

   ```PYSPARK
   # Import libraries
   import fsspec
   import os
   
   # Set variables
   local_file_name = "<local_file_name>"
   ADLS_Store_Path = "abfs[s]://<filesystemname>@<account name>.dfs.windows.cor.net/"+local_file_name
   
   # Generate local file for testing 
   with open(local_file_name, mode='w') as f:
       for i in range(1000):
           f.write("Testing local file functionality\n")
   print("Created: " + local_file_name)

   # Upload local file to ADLS 
   fs = fsspec.filesystem('abfs[s]')
   fs.upload(local_file_name, ADLS_Store_Path)
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Pandas en Synapse Analytics con grupo de Apache Spark sin servidor](tutorial-use-pandas-spark-pool.md)
- [Azure Synapse Analytics](../index.yml)
- [Documentación oficial de FSSPEC](https://filesystem-spec.readthedocs.io/en/latest/)
