---
title: Archivo de inclusión
description: Archivo de inclusión
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 07/21/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: b0b46da23de802d9f26e53d7b3acc96f166e78d0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443400"
---
Azure Machine Learning requiere acceso entrante y saliente a la red pública de Internet. En las tablas siguientes se proporciona información general sobre qué acceso es necesario el acceso y para qué lo es. El __protocolo__ de todos los elementos es __TCP__. En el caso de las etiquetas de servicio que terminan en `.region`, reemplace `region` por la región de Azure que contiene el área de trabajo. Por ejemplo, `Storage.westus`:

| Dirección | Puertos | Etiqueta de servicio | Propósito |
| ----- |:-----:| ----- | ----- |
| Entrada | 29876-29877 | BatchNodeManagement | Instancia de proceso y clúster de proceso de Azure Machine Learning. |
| Entrada | 44224 | AzureMachineLearning | Instancia de proceso de Azure Machine Learning. |
| Salida | * | AzureActiveDirectory | Autenticación de Azure Active Directory. |
| Salida | 443 | AzureMachineLearning | Azure Machine Learning. |
| Salida | 443 | AzureResourceManager | Azure Resource Manager. |
| Salida | 443 | Storage.region | Cuenta de Azure Storage. |
| Salida | 443 | AzureFrontDoor.FrontEnd</br>* No es necesario en Azure China. | Azure Front Door. | 
| Salida | 443 | ContainerRegistry.region | Azure Container Registry. Solo es necesario cuando se usan imágenes de Docker personalizadas. Se incluyen pequeñas modificaciones (como paquetes adicionales) en las imágenes base que proporciona Microsoft. |
| Salida | 443 | MicrosoftContainerRegistry.region | Solo es necesario si se usan imágenes de Docker proporcionadas por Microsoft y se habilitan las dependencias administradas por el usuario. |

> [!TIP]
> Si necesita las direcciones IP, en lugar de etiquetas de servicio, use una de las siguientes opciones:
> * Descargue una lista de [intervalos de direcciones IP y etiquetas de servicio de Azure](https://www.microsoft.com/download/details.aspx?id=56519).
> * Use el comando [az network list-service-tags](/cli/azure/network#az_network_list_service_tags) de la CLI de Azure.
> * Use el comando [Get-AzNetworkServiceTag](/powershell/module/az.network/get-aznetworkservicetag) de Azure PowerShell.
> 
> Las direcciones IP pueden cambiar periódicamente.

También es posible que tenga que permitir el tráfico __saliente__ a Visual Studio Code y a sitios que no son de Microsoft para la instalación de los paquetes requeridos por el proyecto de aprendizaje automático. En la tabla siguiente se enumeran los repositorios usados habitualmente para el aprendizaje automático:

| Nombre de host | Propósito |
| ----- | ----- |
| **anaconda.com**</br>**\*.anaconda.com** | Se usa para instalar paquetes predeterminados. |
| **\*.anaconda.org** | Se usa para obtener datos del repositorio. |
| **pypi.org** | Se usa para enumerar las dependencias del índice predeterminado, si hay alguna, y el índice no se sobrescribe con la configuración del usuario. Si el índice se sobrescribe, también debe permitir **\*.pythonhosted.org**. |
| **cloud.r-project.org** | Se usar al instalar paquetes CRAN para el desarrollo en R. |
| **\*pytorch.org** | Se usa en algunos ejemplos basados en PyTorch. |
| **\*.tensorflow.org** | Se usa en algunos ejemplos basados en TensorFlow. |
| **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Se usa para recuperar bits de servidor de VS Code que se instalan en la instancia de proceso por medio de un script de instalación.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Se usa para recuperar bits de servidor de WebSocket que se instalan en la instancia de proceso. El servidor de WebSocket se usa para transmitir solicitudes desde el cliente de Visual Studio Code (aplicación de escritorio) al servidor de Visual Studio Code que se ejecuta en la instancia de proceso.|