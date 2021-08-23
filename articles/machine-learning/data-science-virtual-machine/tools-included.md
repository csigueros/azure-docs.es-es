---
title: Herramientas incluidas en Azure Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Una lista de las herramientas incluidas en las imágenes de DSVM de Windows y Ubuntu
keywords: herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/12/2021
ms.custom: contperf-fy20q4
ms.openlocfilehash: 63269512a33ce2743d1082001525030c85bc8a4a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110462894"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>¿Qué herramientas se incluyen en Azure Data Science Virtual Machine?

Data Science Virtual Machine es una forma fácil de explorar los datos y realizar aprendizaje automático en la nube. Las instancias de Data Science Virtual Machine están preconfiguradas con el sistema operativo completo, los parches de seguridad, los controladores y el software de desarrollo y ciencia de datos más populares. Puede elegir el entorno de hardware, que abarca desde instancias de menor costo centradas en la CPU hasta máquinas muy eficaces con varias GPU, almacenamiento de NVMe y grandes cantidades de memoria. En el caso de las instancias con varias GPU, se instalan todos los controladores, todos los marcos de aprendizaje automático tienen la versión correspondiente a la compatibilidad de GPU y la aceleración está habilitada en todo el software de aplicación que admita las GPU.

Data Science Virtual Machine incluye las herramientas de ciencia de datos más útiles preinstaladas. 

## <a name="build-deep-learning-and-machine-learning-solutions"></a>Creación de aprendizaje profundo y soluciones de aprendizaje automático

| Herramienta | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | Notas de uso |
|--|:-:|:-:|:-:|
| [CUDA, cuDNN, NVIDIA Driver](https://developer.nvidia.com/cuda-toolkit) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [CUDA, cuDNN y controlador NVIDIA en DSVM](./dsvm-tools-deep-learning-frameworks.md#cuda-cudnn-nvidia-driver) |
| [Horovod](https://github.com/horovod/horovod) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [Horovod en DSVM](./dsvm-tools-deep-learning-frameworks.md#horovod) |
| [NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [nvidia-smi en DSVM](./dsvm-tools-deep-learning-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| [PyTorch](https://pytorch.org) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [PyTorch en DSVM](./dsvm-tools-deep-learning-frameworks.md#pytorch) |
| [TensorFlow](https://www.tensorflow.org) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [TensorFlow en DSVM](./dsvm-tools-deep-learning-frameworks.md#tensorflow) |
| Integración con [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R y Python) | <span class='green-check'>&#9989;</span></br> (Python SDK, ejemplos) | <span class='green-check'>&#9989;</span></br> (Python/R SDK,CLI, ejemplos) | [SDK de Azure ML](./dsvm-tools-data-science.md#azure-machine-learning-sdk-for-python) |
| [XGBoost](https://github.com/dmlc/xgboost) | <span class='green-check'>&#9989;</span></br> (Compatibilidad con CUDA) | <span class='green-check'>&#9989;</span></br> (Compatibilidad con CUDA) | [XGBoost en DSVM](./dsvm-tools-data-science.md#xgboost) |
| [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> | [Vowpal Wabbit en DSVM](./dsvm-tools-data-science.md#vowpal-wabbit) |
| [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | <span class='red-x'>&#10060;</span> | <span class='red-x'>&#10060;</span> |  |
| LightGBM | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (Compatibilidad con GPU, MPI) |  |
| H2O | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| CatBoost | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Intel MKL | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| OpenCV | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Dlib | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Docker | <span class='green-check'>&#9989;</span> <br/> (Solo contenedores de Windows) | <span class='green-check'>&#9989;</span> |  |
| Nccl | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Rattle | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| ONNX Runtime | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |



## <a name="store-retrieve-and-manipulate-data"></a>Almacenamiento, recuperación y manipulación de datos

| Herramienta | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | Notas de uso |
|--|-:|:-:|:-:|
| Bases de datos relacionales | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [SQL Server en DSVM](./dsvm-tools-data-platforms.md#sql-server-developer-edition) |
| Herramientas de base de datos | SQL Server Management Studio<br/> SQL Server Integration Services<br/> [bcp, sqlcmd](/sql/tools/command-prompt-utility-reference-database-engine) | [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (herramienta de consulta), <br /> bcp, sqlcmd <br /> Controladores ODBC/JDBC |  |
| [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [CLI de Azure](/cli/azure) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [AzCopy](../../storage/common/storage-use-azcopy-v10.md) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> | [AzCopy en DSVM](./dsvm-tools-ingestion.md#azcopy) |
| [Controlador FUSE de blob](https://github.com/Azure/azure-storage-fuse) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [blobfuse en DSVM](./dsvm-tools-ingestion.md#blobfuse) |
| [Herramienta de migración de datos de Azure Cosmos DB](../../cosmos-db/import-data.md) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [Cosmos DB en DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| Herramientas de línea de comandos de Unix o Linux | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Apache Spark 3.1 (independiente) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> |  |

## <a name="program-in-python-r-julia-and-nodejs"></a>Programa en Python, R, Julia y Node.js

| Herramienta | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | Notas de uso |
|--|:-:|:-:|:-:|
| [CRAN-R](https://cran.r-project.org/) con paquetes populares preinstalados | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| [Anaconda Python](https://www.continuum.io/) con paquetes populares preinstalados | <span class='green-check'>&#9989;</span><br/> (Miniconda) | <span class='green-check'>&#9989;</span></br> (Miniconda) |  |
| [Julia (Julialang)](https://julialang.org/) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterHub (servidor de cuadernos multiusuario) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterLab (servidor de cuadernos multiusuario) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| Node.js | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| [Servidor de Jupyter Notebook](https://jupyter.org/) con los siguientes kernels: | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span> | [Ejemplos de Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) |
| &nbsp;&nbsp;&nbsp;&nbsp; R |  |  | [Ejemplos de Jupyter R](./dsvm-samples-and-walkthroughs.md#r-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Python |  |  | [Ejemplos de Python Jupyter](./dsvm-samples-and-walkthroughs.md#python-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Julia |  |  | [Ejemplos de Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; PySpark |  |  | [Ejemplos de pySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml) |

**DSVM de Ubuntu 18.04 y DSVM de Windows Server 2019** tienen los siguientes kernels de Jupyter:</br> 
* Python 3.8: predeterminado</br>  
* Python 3.8: PyTorch</br>  
* Python 3.8: TensorFlow</br>  
* Python 3.6: AzureML, TensorFlow</br>  
* Python 3.6: AzureML, PyTorch</br>  
* Python 3.6: AzureML, AutoML</br>  
* R</br>  
* Python 3.7: Spark (local)</br>  
* Julia 1.2.0</br>  
* R Spark: HDInsight</br>  
* Scala Spark: HDInsight</br>  
* Python 3 Spark: HDInsight</br>  

**DSVM de Ubuntu 18.04 y DSVM de Windows Server 2019** tienen los siguientes entornos de Conda:</br> 
* py38_default  </br>
* py38_tensorflow </br> 
* py38_pytorch  </br>
* azureml_py36_tensorflow  </br>
* azureml_py36_pytorch  </br>
* azureml_py36_automl  </br>


## <a name="use-your-preferred-editor-or-ide"></a>Uso del editor o IDE preferidos

| Herramienta | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | Notas de uso |
|--|:-:|:-:|:-:|
| [Notepad++](https://notepad-plus-plus.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Nano](https://www.nano-editor.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Visual Studio 2019 Community Edition](https://www.visualstudio.com/community/) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [Visual Studio en DSVM](dsvm-tools-development.md#visual-studio-community-edition) |
| [Visual Studio Code](https://code.visualstudio.com/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [Visual Studio Code en DSVM](./dsvm-tools-development.md#visual-studio-code) |
| [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [RStudio Desktop en DSVM](./dsvm-tools-development.md#rstudio-desktop) |
| [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) <br/> (deshabilitado de forma predeterminada) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [PyCharm en DSVM](./dsvm-tools-development.md#pycharm) |
| [IntelliJ IDEA](https://www.jetbrains.com/idea/) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [Vim](https://www.vim.org) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Git](https://git-scm.com/) y Git Bash | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [OpenJDK](https://openjdk.java.net) 11 | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| .NET Framework | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| SDK de Azure | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |

## <a name="organize--present-results"></a>Organización y presentación de resultados

| Herramienta | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | Notas de uso |
|--|:-:|:-:|:-:|
| [Microsoft 365](https://www.microsoft.com/microsoft-365) (Word, Excel, PowerPoint) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Microsoft Teams](https://www.microsoft.com/microsoft-teams) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Power BI Desktop](https://powerbi.microsoft.com/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Explorador Microsoft Edge | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
