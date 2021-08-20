---
title: OJ Sales Simulated
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos OJ Sales Simulated en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 8f835e12bbfd17474fd2b928809394c93c19eae5
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039062"
---
# <a name="oj-sales-simulated"></a>OJ Sales Simulated 

Este conjunto de datos se deriva del conjunto de datos OJ de Dominick e incluye datos simulados adicionales para entrenar de manera simultánea miles de modelos en Azure Machine Learning.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Los datos contienen las ventas semanales de zumo de naranja a lo largo de 121 semanas. Hay 3991 tiendas incluidas y 3 marcas de zumo de naranja por tienda, por lo que se pueden entrenar 11 973 modelos.

[Vea la descripción del conjunto de datos original](http://www.cs.unitn.it/~taufer/QMMA/L10-OJ-Data.html) o [descargue el conjunto de datos](http://www.cs.unitn.it/~taufer/Data/oj.csv).

## <a name="columns"></a>Columnas

| Nombre | Tipo de datos | Único | Valores (ejemplo) | Descripción |
|-|-|-|-|-|
| Anuncio | int |  | 1 | Valor que indica si durante la semana ha habido anuncios de ese zumo de naranja. 0: sin anuncios. 1: anuncios. |
| Marca | string |  | dominicks tropicana | Marca del zumo de naranja |
| Price | double |  | 2.6 2.09 | Precio del zumo de naranja (en USD) |
| Quantity | int |  | 10939 11638 | Cantidad de zumo de naranja vendida durante esa semana |
| Ingresos | double |  | 38438.4 36036.0 | Ingresos por las ventas de zumo de naranja en esa semana (en USD) |
| Tienda | int |  | 2658 1396 | Número de la tienda donde se vendió el zumo de naranja |
| WeekStarting | timestamp |  | 1990-08-09 00:00:00 1992-02-20 00:00:00 | Fecha que indica la semana a la que se refieren las ventas |

## <a name="preview"></a>Vista previa

| WeekStarting | Tienda | Marca | Quantity | Anuncio | Price | Ingresos |
|-|-|-|-|-|-|-|
| 10/1/1992 12:00:00 AM | 3571 | minute.maid | 13247 | 1 | 2,42 | 32057.74 |
| 10/1/1992 12:00:00 AM | 2999 | minute.maid | 18461 | 1 | 2.69 | 49660.09 |
| 10/1/1992 12:00:00 AM | 1198 | minute.maid | 13222 | 1 | 2.64 | 34906.08 |
| 10/1/1992 12:00:00 AM | 3916 | minute.maid | 12923 | 1 | 2.45 | 31661.35 |
| 10/1/1992 12:00:00 AM | 1688 | minute.maid | 9380 | 1 | 2.46 | 23074.8 |
| 10/1/1992 12:00:00 AM | 1040 | minute.maid | 18841 | 1 | 2.31 | 43522.71 |
| 10/1/1992 12:00:00 AM | 1938 | minute.maid | 14202 | 1 | 2.19 | 31102.38 |
| 10/1/1992 12:00:00 AM | 2405 | minute.maid | 16326 | 1 | 2,05 | 33468.3 |
| 10/1/1992 12:00:00 AM | 1972 | minute.maid | 16380 | 1 | 2,12 | 34725.6 |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated)** .

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_config()
```

```python
from azureml.opendatasets import OjSalesSimulated
```

### <a name="read-data-from-azure-open-datasets"></a>Lectura de datos desde Azure Open Datasets

```python
# Create a Data Directory in local path
import os

oj_sales_path = "oj_sales_data"

if not os.path.exists(oj_sales_path):
    os.mkdir(oj_sales_path)
```

```python
# Pull all of the data
oj_sales_files = OjSalesSimulated.get_file_dataset()

# or pull a subset of the data
oj_sales_files = OjSalesSimulated.get_file_dataset(num_files=10)
```

```python
oj_sales_files.download(oj_sales_path, overwrite=True)
```

### <a name="upload-the-individual-datasets-to-blob-storage"></a>Carga de conjuntos de datos individuales a Blob Storage
Los datos se cargan en el blob y FileDataset se crea a partir de esta carpeta de archivos csv.  

```python
target_path = 'oj_sales_data'

datastore.upload(src_dir = oj_sales_path,
                target_path = target_path,
                overwrite = True, 
                show_progress = True)
```

### <a name="create-the-file-dataset"></a>Creación del conjunto de datos de archivo 
Es necesario definir la ruta de los datos para crear el objeto [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset). 


```python
from azureml.core.dataset import Dataset

ds_name = 'oj_data'
path_on_datastore = datastore.path(target_path + '/')

input_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

### <a name="register-the-file-dataset-to-the-workspace"></a>Registro del conjunto de datos de archivo en el área de trabajo 
El objetivo es registrar el conjunto de datos en el área de trabajo para poder llamarlo como entrada en la canalización para la previsión. 


```python
registered_ds = input_ds.register(ws, ds_name, create_new_version=True)
named_ds = registered_ds.as_named_input(ds_name)
```

<!-- nbend -->

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
# Download or mount OJ Sales raw files Azure Machine Learning file datasets.
# This works only for Linux based compute. See https://docs.microsoft.com/en-us/azure/machine-learning/service/how-to-create-register-datasets to learn more about datasets.

from azureml.opendatasets import OjSalesSimulated

ojss_file = OjSalesSimulated.get_file_dataset()
ojss_file
```

```
ojss_file.to_path()
```

```
# Download files to local storage
import os
import tempfile

mount_point = tempfile.mkdtemp()
ojss_file.download(mount_point, overwrite=True)
```

```
# Mount files. Useful when training job will run on a remote compute.
import gzip
import struct
import pandas as pd
import numpy as np

# load compressed OJ Sales Simulated gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        gz.read(4)
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return pd.DataFrame(res)
```

```
import sys
mount_point = tempfile.mkdtemp()
print(mount_point)
print(os.path.exists(mount_point))
print(os.listdir(mount_point))

if sys.platform == 'linux':
  print("start mounting....")
  with ojss_file.mount(mount_point):
    print(os.listdir(mount_point))  
    train_images_df = load_data(os.path.join(mount_point, 'train-tabular-oj-ubyte.gz'))
    print(train_images_df.info())
```

<!-- nbend -->

---

## <a name="next-steps"></a>Pasos siguientes

Consulte el resto de los conjuntos de datos en el [catálogo de Open Datasets](dataset-catalog.md).