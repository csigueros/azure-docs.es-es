---
title: Archivo de inclusión
description: Archivo de inclusión
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 08/27/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: e144756e65c0ae4d202333cd2972d9c5a1e3fdc8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740726"
---
Azure Machine Learning requiere acceso entrante y saliente a la red pública de Internet. En las tablas siguientes se proporciona información general sobre qué acceso es necesario el acceso y para qué lo es. El __protocolo__ de todos los elementos es __TCP__. En el caso de las etiquetas de servicio que terminan en `.region`, reemplace `region` por la región de Azure que contiene el área de trabajo. Por ejemplo, `Storage.westus`:

| Dirección | Puertos | Etiqueta de servicio | Propósito |
| ----- |:-----:| ----- | ----- |
| Entrada | 29876-29877 | BatchNodeManagement | Crear, actualizar y eliminar una instancia y un clúster de proceso de Azure Machine Learning. |
| Entrada | 44224 | AzureMachineLearning | Crear, actualizar y eliminar una instancia de proceso de Azure Machine Learning. |
| Salida | * | AzureActiveDirectory | Efectuar la autenticación mediante Azure AD. |
| Salida | 443 | AzureMachineLearning | Usar los servicios de Azure Machine Learning. |
| Salida | 443 | AzureResourceManager | Crear recursos de Azure con Azure Machine Learning. |
| Salida | 443 | Storage.region | Acceder a los datos almacenados en la cuenta de Azure Storage para el servicio Azure Batch. |
| Salida | 443 | AzureFrontDoor.FrontEnd</br>* No es necesario en Azure China. | Establecer un punto de entrada global para [Estudio de Azure Machine Learning](https://ml.azure.com). | 
| Salida | 443 | ContainerRegistry.region | Acceder a las imágenes de Docker proporcionadas por Microsoft. |
| Salida | 443 | MicrosoftContainerRegistry.region | Acceder a las imágenes de Docker proporcionadas por Microsoft. Configurar el enrutador de Azure Machine Learning para Azure Kubernetes Service. |
| Salida | 443 | Keyvault.region | Acceda al almacén de claves del servicio Azure Batch. Solo se necesita si el área de trabajo se creó con la marca [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) habilitada. |

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

Al usar Azure Kubernetes Service (AKS) con Azure Machine Learning, habilite el tráfico siguiente a la red virtual de AKS:

* Requisitos generales de entrada y salida para AKS, tal como se describe en el artículo [Restricción del tráfico de salida en Azure Kubernetes Service](../articles/aks/limit-egress-traffic.md).
* __Tráfico de salida__ a mcr.microsoft.com.
* Al implementar un modelo en un clúster de AKS, use las instrucciones del artículo [Implementación de modelos de ML en Azure Kubernetes Service](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md#connectivity).