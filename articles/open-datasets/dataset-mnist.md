---
title: Base de datos MNIST de dígitos manuscritos
titleSuffix: Azure Open Datasets
description: Aprenda a usar la base de datos MNIST del conjunto de datos de dígitos manuscritos en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1935c8702002fe00fd99de7d6b095d95ecce8475
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039075"
---
# <a name="the-mnist-database-of-handwritten-digits"></a>Base de datos MNIST de dígitos manuscritos

La base de datos MNIST de dígitos manuscritos tiene un conjunto de entrenamiento de 60 000 ejemplos y un conjunto de prueba de 10 000 ejemplos. Los dígitos tienen un tamaño normalizado y están centrados en una imagen de tamaño fijo.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

La fuente de este conjunto de datos es la [BASE DE DATOS MNIST de dígitos manuscritos](http://yann.lecun.com/exdb/mnist/). Es un subconjunto de la base de datos [Formularios y Caracteres Impresos Manualmente de NIST](https://www.nist.gov/srd/nist-special-database-19) que publica el [Instituto Nacional de Estándares y Tecnología](https://www.nist.gov/) (NIST, por sus siglas en inglés).


## <a name="storage-location"></a>Ubicación de almacenamiento

- Cuenta de blob: azureopendatastorage
- Nombre del contenedor: mnist

Hay cuatro archivos disponibles en el contenedor directamente:
  - train-images-idx3-ubyte.gz: training set images (9,912,422 bytes)
  - train-labels-idx1-ubyte.gz: training set labels (28,881 bytes)
  - t10k-images-idx3-ubyte.gz: test set images (1,648,877 bytes)
  - t10k-labels-idx1-ubyte.gz: test set labels (4,542 bytes)

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=mnist -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=mnist)** .

### <a name="load-mnist-into-a-data-frame-using-azure-machine-learning-tabular-datasets"></a>Cargue MNIST en una trama de datos mediante conjuntos de datos tabulares de Azure Machine Learning. 
Para más información sobre los conjuntos de datos de Azure Machine Learning, consulte [Creación de conjuntos de datos de Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md). 

#### <a name="get-complete-dataset-into-a-data-frame"></a>Obtención de un conjunto de datos completo en una trama de datos

```python
from azureml.opendatasets import MNIST

mnist = MNIST.get_tabular_dataset()
mnist_df = mnist.to_pandas_dataframe()
mnist_df.info()
```

#### <a name="get-train-and-test-data-frames"></a>Obtención de tramas de datos de entrenamiento y prueba

```python
mnist_train = MNIST.get_tabular_dataset(dataset_filter='train')
mnist_train_df = mnist_train.to_pandas_dataframe()
X_train = mnist_train_df.drop("label", axis=1).astype(int).values/255.0
y_train = mnist_train_df.filter(items=["label"]).astype(int).values

mnist_test = MNIST.get_tabular_dataset(dataset_filter='test')
mnist_test_df = mnist_test.to_pandas_dataframe()
X_test = mnist_test_df.drop("label", axis=1).astype(int).values/255.0
y_test = mnist_test_df.filter(items=["label"]).astype(int).values
```

#### <a name="plot-some-images-of-the-digits"></a>Trazado de imágenes de los dígitos 

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

### <a name="download-or-mount-mnist-raw-files-azure-machine-learning-file-datasets"></a>Descargue o monte archivos sin formato de MNIST de conjuntos de datos de archivos de Azure Machine Learning. 
Esto solo funciona para el proceso basado en Linux. Para más información sobre los conjuntos de datos de Azure Machine Learning, consulte [Creación de conjuntos de datos de Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md). 

```python
mnist_file = MNIST.get_file_dataset()
mnist_file
```

```python
mnist_file.to_path()
```

#### <a name="download-files-to-local-storage"></a>Descarga de archivos en el almacenamiento local

```python
import os
import tempfile

data_folder = tempfile.mkdtemp()
data_paths = mnist_file.download(data_folder, overwrite=True)
data_paths
```

#### <a name="mount-files-useful-when-training-job-will-run-on-a-remote-compute"></a>Monte los archivos. Resulta útil cuando el trabajo de entrenamiento se va a ejecutar en un proceso remoto.

```python
import gzip
import struct
import pandas as pd
import numpy as np

# load compressed MNIST gz files and return pandas dataframe of numpy arrays
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

```python
import sys
mount_point = tempfile.mkdtemp()
print(mount_point)
print(os.path.exists(mount_point))

if sys.platform == 'linux':
  print("start mounting....")
  with mnist_file.mount(mount_point):
    print("list dir...")
    print(os.listdir(mount_point))
    print("get the dataframe info of mounted data...")
    train_images_df = load_data(next(path for path in data_paths if path.endswith("train-images-idx3-ubyte.gz")))
    print(train_images_df.info())
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=mnist -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=mnist)** .

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
```

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/t10k-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/t10k-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-labels.gz'))
```

```python
import gzip
import struct

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res
```

```python
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(
    data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(
    data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

<!-- nbend -->

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=mnist -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=mnist)** .

### <a name="load-mnist-into-a-data-frame-using-azure-machine-learning-tabular-datasets"></a>Cargue MNIST en una trama de datos mediante conjuntos de datos tabulares de Azure Machine Learning. 
Para más información sobre los conjuntos de datos de Azure Machine Learning, consulte [Creación de conjuntos de datos de Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md). 

#### <a name="get-complete-dataset-into-a-data-frame"></a>Obtención de un conjunto de datos completo en una trama de datos

```python
# This is a package in preview.
from azureml.opendatasets import MNIST

mnist = MNIST.get_tabular_dataset()
mnist_df = mnist.to_spark_dataframe()
```

```python
display(mnist_df.limit(5))
```

### <a name="download-or-mount-mnist-raw-files-azure-machine-learning-file-datasets"></a>Descargue o monte archivos sin formato de MNIST de conjuntos de datos de archivos de Azure Machine Learning. 
Esto solo funciona para el proceso basado en Linux. Para más información sobre los conjuntos de datos de Azure Machine Learning, consulte [Creación de conjuntos de datos de Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md). 

```python
mnist_file = MNIST.get_file_dataset()
mnist_file
```

```python
mnist_file.to_path()
```

#### <a name="download-files-to-local-storage"></a>Descarga de archivos en el almacenamiento local

```python
import os
import tempfile

mount_point = tempfile.mkdtemp()
mnist_file.download(mount_point, overwrite=True)
```

#### <a name="mount-files-useful-when-training-job-will-run-on-a-remote-compute"></a>Monte los archivos. Resulta útil cuando el trabajo de entrenamiento se va a ejecutar en un proceso remoto.

```python
import gzip
import struct
import pandas as pd
import numpy as np

# load compressed MNIST gz files and return numpy arrays
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

```python
import sys
mount_point = tempfile.mkdtemp()
print(mount_point)
print(os.path.exists(mount_point))
print(os.listdir(mount_point))

if sys.platform == 'linux':
  print("start mounting....")
  with mnist_file.mount(mount_point):
    print(context.mount_point )
    print(os.listdir(mount_point))  
    train_images_df = load_data(os.path.join(mount_point, 'train-images-idx3-ubyte.gz'))
    print(train_images_df.info())
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

---

## <a name="next-steps"></a>Pasos siguientes

Consulte el resto de los conjuntos de datos en el [catálogo de Open Datasets](dataset-catalog.md).