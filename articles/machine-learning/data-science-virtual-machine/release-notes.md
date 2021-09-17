---
title: Novedades de Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Notas de la versión para Azure Data Science Virtual Machine
author: michalmar
ms.service: data-science-vm
ms.author: mimarusa
ms.date: 07/16/2021
ms.topic: reference
ms.openlocfilehash: e256382b4d1e397be6d11a3660a4c4fe73eafb23
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778328"
---
# <a name="azure-data-science-virtual-machine-release-notes"></a>Notas de la versión de Azure Data Science Virtual Machine

En este artículo, obtendrá información sobre las versiones de Azure Data Science Virtual Machine. Para obtener una lista completa de las herramientas incluidas, junto con los números de versión, consulte [esta página](./tools-included.md).

Para obtener información acerca de errores conocidos y soluciones alternativas, consulte la [lista de problemas conocidos](reference-known-issues.md).


## <a name="2021-08-11"></a>11-08-2021

Nueva imagen para [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Versión 21.08.11

Cambios principales:

- Actualización de seguridad de Windows
- Actualización de Nvidia CuDNN a la versión 8.1.0
- Actualización de Jupyter Lab a la versión 3.0.16
- Se ha agregado MLFLow para el seguimiento de experimentos
- Mejora de la estabilidad y correcciones de errores menores 



## <a name="2021-07-12"></a>12/07/2021

Nueva imagen para [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Cambios principales:

- Se actualizó a PyTorch 1.9.0
- Se actualizó la CLI de Azure a 2.26.1
- Se actualizó la extensión Azure Machine Learning de la CLI de Azure a la versión 1.29.0
- Actualización de la versión 1.58.1 de VS Code
- Mejora de la estabilidad y correcciones de errores menores 


## <a name="2021-06-22"></a>22/06/2021

Nueva imagen para [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Versión: 21.06.22

Cambios principales:

- Se actualizó a PyTorch 1.9.0
- Se corrigió un error por el que Git no estaba disponible


## <a name="2021-06-01"></a>01-06-2021

Nueva imagen para [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Versión: 21.06.01

Los cambios principales son:

- Docker está habilitado de manera predeterminada
- JupyterHub usa JupyterLab de manera predeterminada
- Se actualizaron las versiones de Python para corregir [CVE-2020-15523](https://nvd.nist.gov/vuln/detail/CVE-2020-15523)
- Se actualizó IntelliJ IDEA a la versión 2021.1 para corregir [CVE-2021-25758](https://nvd.nist.gov/vuln/detail/CVE-2021-25758)
- Se actualizó PyCharm Community a 2021.1
- Se actualizó TensorFlow a la versión 2.5.0

<br/>
Se quitaron varios iconos del escritorio.

## <a name="2021-05-22"></a>22/05/2021

Nueva imagen para [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Versión: 21.05.22

Las actualizaciones de la versión seleccionada son:
- CUDA 11.1
- Python 3.8
- PyTorch 1.8.1
- TensorFlow 2.5.0
- Spark 3.1.1
- Java 11
- R 4.1.0
- Julia 1.0.5
- NodeJS 16.2.0
- Visual Studio Code 1.56.2 incluida la extensión de Azure ML
- PyCharm Community Edition 2021.1.1
- Jupyter Lab 2.2.6
- RStudio 1.4.1106
- Visual Studio Community Edition 2019 (versión 16.9.6)
- CLI de Azure 2.23.0
- Explorador de Storage 1.19.1
- AzCopy 10.10.0
- Power BI Desktop 2.93.641.0 de 64 bits (mayo de 2021)
- Azure Data Studio 1.28.0
- Explorador Microsoft Edge

<br/>
Se han quitado Firefox, Apache Drill y Microsoft Integration Runtime.

<br/>
Modo oscuro, iconos cambiados en el escritorio, cambio de fondo de pantalla.

## <a name="2021-05-12"></a>12/05/2021

Nueva imagen para [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Las actualizaciones de la versión seleccionada son:
- CUDA 11.3, cuDNN 8, NCCL2
- Python 3.8
- R 4.0.5
- Spark 3.1 incluido mmlspark, conectores a Blob Storage, lago de datos, CosmosDB
- Java 11 (OpenJDK)
- Jupyter Lab 3.0.14
- PyTorch 1.8.1 incluido torchaudio torchtext torchvision, torch-tb-profiler
- TensorFlow 2.4.1 incluido TensorBoard
- dask 2021.01.0
- VS.Code 1.56
- Azure Data Studio 1.22.1
- CLI de Azure 2.23.0
- Explorador de Azure Storage 1.19.1
- azcopy 10.10
- Explorador Microsoft Edge (beta)

<br/>
Se agregó Docker. Para guardar recursos, el servicio Docker no se inicia de forma predeterminada. Para iniciar el servicio Docker, ejecute los siguientes comandos de línea de comandos:

```
sudo systemctl start docker
```

> [!NOTE]
> Si la máquina tiene GPU, puede usar las GPU dentro de los contenedores. Para ello, debe agregar un parámetro `--gpus` al comando de Docker.
>
> Por ejemplo, la ejecución de 
>
> `sudo docker run --gpus all -it --rm -v local_dir:container_dir nvcr.io/nvidia/pytorch:18.04-py3`
>
> ejecutará un contenedor de Ubuntu 18.04 con PyTorch preinstalado y todas las GPU habilitadas. También hará que una carpeta local *local_dir* esté disponible en el contenedor en *container_dir*.
>


## <a name="2020-02-24"></a>2020-02-24

Ya están disponibles las imágenes de Data Science Virtual Machine para [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) y las imágenes de [Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

## <a name="2019-10-08"></a>2019-10-08

### <a name="updates-to-software-on-the-windows-dsvm"></a>Actualizaciones del software en Windows DSVM

- Azure Storage Explorer 1.10.1
- Power BI Desktop 2.73.55xx
- Firefox 69.0.2
- PyCharm 19.2.3
- RStudio 1.2.50xx

### <a name="default-browser-for-windows-updated"></a>Explorador predeterminado para Windows actualizado

Anteriormente, el explorador predeterminado se ha establecido en Internet Explorer. Ahora se solicitará a los usuarios que elijan un explorador predeterminado cuando inicien sesión por primera vez.
