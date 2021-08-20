---
title: 'Tutorial: Uso de FSSPEC para leer y escribir datos de ADLS en un grupo de Spark de Synapse Analytics'
description: Tutorial sobre cómo usar FSSPEC para cargas de trabajo de aprendizaje automático a fin de leer y escribir datos de ADLS en grupos de Spark dedicados.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 06/07/2021
author: AjAgr
ms.author: ajagarw
ms.openlocfilehash: ecd0b923939ab1da724b46272dbaf60c44389d3d
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904313"
---
# <a name="tutorial-use-fsspec-to-readwrite-adls-data-in-synapse-analytics-spark-pool"></a>Tutorial: Uso de FSSPEC para leer y escribir datos de ADLS en un grupo de Spark de Synapse Analytics

Aprenda a usar La especificación del sistema de archivos (FSSPEC) para leer y escribir datos en Azure Data Lake Storage (ADLS) mediante un servicio vinculado en un grupo de Spark dedicado en Azure Synapse Analytics.

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

   :::image type="content" source="media/tutorial-spark-pool-filesystem-spec/create-adls-linked-service.png" alt-text="Creación del servicio vinculado mediante la clave de acceso de Azure Data Lake Storage Gen2.":::


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

   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key)

   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name 
   adls_account_key = '' #Provide exact ADLS account key 
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name,    account_key=adls_account_key, mode="wt")
   
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
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key)
   
   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>) 
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key, mode="wt") 
   
   with fsspec_handle.open() as f:
       data.to_csv(f) 
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Funcionalidades de aprendizaje automático en Azure Synapse Analytics](what-is-machine-learning.md)