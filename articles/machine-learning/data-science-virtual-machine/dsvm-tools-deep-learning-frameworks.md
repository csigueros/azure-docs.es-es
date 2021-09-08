---
title: Marcos de aprendizaje profundo y AI
titleSuffix: Azure Data Science Virtual Machine
description: Disponibilidad de los marcos de aprendizaje profundo y las herramientas en Azure Data Science Virtual Machine.
keywords: herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: michalmar
ms.author: mimarusa
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: a757151d17456c7ee9646bc0730a51f34088b255
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729195"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Marcos de aprendizaje profundo e inteligencia artificial para Azure Data Science VM
La lista a continuación enumera los marcos de aprendizaje profundo en DSVM.


## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, NVIDIA Driver](https://developer.nvidia.com/cuda-toolkit)

| Category | Value |
|--|--|
| Versiones compatibles | 11 |
| Ediciones de DSVM admitidas | Windows Server 2019<br>Ubuntu 18.04 |
| ¿Cómo se configura/instala en DSVM? | _nvidia-smi_ está disponible en la ruta de acceso del sistema. |
| Cómo ejecutarlo | Abra un símbolo del sistema (en Windows) o un terminal (en Linux) y ejecute _nvidia-smi_. |
## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Category | Value |
| ------------- | ------------- |
| Versiones compatibles | 0.21.3|
| Ediciones de DSVM admitidas      | Ubuntu 18.04 |
| ¿Cómo se configura/instala en DSVM?  | Horovod se instala en Python 3.5 |
| Cómo ejecutarlo      | Active el entorno correcto en el terminal y, luego, ejecute Python. |


## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| Category | Value |
|--|--|
| Versiones compatibles |  |
| Ediciones de DSVM admitidas | Windows Server 2019<br>Ubuntu 18.04 |
| ¿Para qué sirven? | Herramienta NVIDIA para consultar la actividad GPU |
| ¿Cómo se configura/instala en DSVM? | `nvidia-smi` está en la ruta de acceso del sistema. |
| Cómo ejecutarlo | En una máquina virtual **con GPU**, abra un símbolo del sistema (en Windows) o un terminal (en Linux), y ejecute `nvidia-smi`. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Category | Value |
|--|--|
| Versiones compatibles | 1.9.0 (Ubuntu 18.04, Windows 2019) |
| Ediciones de DSVM admitidas | Windows Server 2019<br>Ubuntu 18.04 |
| ¿Cómo se configura/instala en DSVM? | Instalado en Python, los entornos de Conda "py38_default", "py38_pytorch" |
| Cómo ejecutarlo | Terminal: active el entorno correcto y ejecute Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): conéctese y abra el directorio de PyTorch para obtener ejemplos. |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Category | Value |
|--|--|
| Versiones compatibles | 2.5 |
| Ediciones de DSVM admitidas | Windows Server 2019<br>Ubuntu 18.04 |
| ¿Cómo se configura/instala en DSVM? | Instalado en Python, los entornos de Conda "py38_default", "py38_tensorflow" |
| Cómo ejecutarlo | Terminal: active el entorno correcto y ejecute Python. <br/> *Jupyter: conéctese a [Jupyter](provision-vm.md) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine) y abra el directorio TensorFlow para acceder a los ejemplos. |
