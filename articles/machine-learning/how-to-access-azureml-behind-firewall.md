---
title: Configuración del tráfico de red entrante y saliente
titleSuffix: Azure Machine Learning
description: Cómo configurar el tráfico de red entrante y saliente necesario cuando se usa un área de trabajo de Azure Machine Learning segura.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 08/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 2bcc1a9fdd930a8c9dd85604528a276f9de8d6e8
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113112"
---
# <a name="configure-inbound-and-outbound-network-traffic"></a>Configuración del tráfico de red entrante y saliente

En este artículo, obtendrá información sobre los requisitos de comunicación de red al proteger el área de trabajo de Azure Machine Learning en una red virtual (VNet). Incluye el procedimiento de configuración de Azure Firewall para controlar el acceso al área de trabajo de Azure Machine Learning y a la red pública de Internet. Para obtener más información sobre la protección de Azure Machine Learning, consulte [Seguridad de empresa para Azure Machine Learning](concept-enterprise-security.md).

> [!NOTE]
> La información de este artículo se aplica al área de trabajo de Azure Machine Learning, con independencia de si usa un punto de conexión privado o un punto de conexión de servicio.

> [!TIP]
> Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:
>
> * [Información general sobre redes virtuales](how-to-network-security-overview.md)
> * [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
> * [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
> * [Protección de un entorno de inferencia](how-to-secure-inferencing-vnet.md)
> * [Habilitación de Azure Machine Learning Studio en una red virtual](how-to-enable-studio-virtual-network.md)
> * [Uso de un DNS personalizado](how-to-custom-dns.md)

## <a name="required-public-internet-access"></a>Acceso obligatorio a una red de Internet pública

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

## <a name="azure-firewall"></a>Azure Firewall

> [!IMPORTANT]
> Azure Firewall proporciona seguridad _para los recursos de Azure Virtual Network_. Algunos servicios de Azure, como las cuentas de Azure Storage, tienen su propia configuración de firewall, que se _aplica al punto de conexión público para esa instancia de servicio específica_. La información contenida en este documento es específica de Azure Firewall.
> 
> Para obtener información sobre la configuración del firewall de la instancia del servicio, consulte [Uso de Studio en una red virtual](how-to-enable-studio-virtual-network.md#firewall-settings).

* En el caso del tráfico de __entrada__ al clúster y la instancia de proceso de Azure Machine Learning, utilice [rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md) para omitir el firewall.

* En el caso del tráfico de __salida__, cree reglas de __red__ y de __aplicación__. 

Estas colecciones de reglas se describen con más detalle en [¿Cuáles son algunos de los conceptos de Azure Firewall?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)

### <a name="inbound-configuration"></a>Configuración de entrada

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

### <a name="outbound-configuration"></a>Configuración de salida

1. Agregue __reglas de red__ para permitir el tráfico __hacia__ y __desde__ las siguientes etiquetas de servicio:

    | Etiqueta de servicio | Protocolo | Port |
    | ----- |:-----:|:-----:|
    | AzureActiveDirectory | TCP | * |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* No es necesario en Azure China. | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.region solo es necesario para imágenes personalizadas de Docker. Esto incluye pequeñas modificaciones (como paquetes adicionales) en las imágenes base proporcionadas por Microsoft.
    > * MicrosoftContainerRegistry.region solo es necesario si planea usar las _imágenes de Docker predeterminadas proporcionadas por Microsoft_ y _habilitar las dependencias administradas por el usuario_.
    > * En el caso de las entradas que contienen `region`, reemplace este valor por la región de Azure que esté usando. Por ejemplo, `ContainerRegistry.westus`.

1. Agregue __reglas de aplicación__ para los siguientes hosts:

    > [!NOTE]
    > Esta no es una lista completa de los hosts necesarios para todos los recursos de Python en Internet, solo el que se usa con más frecuencia. Por ejemplo, si necesita acceder a un repositorio de GitHub o a otro host, debe identificar y agregar los hosts necesarios para ese escenario.

    | **Nombre de host** | **Propósito** |
    | ---- | ---- |
    | **graph.windows.net** | Se usa en instancias de proceso/clústeres de Azure Machine Learning. |
    | **anaconda.com**</br>**\*.anaconda.com** | Se usa para instalar paquetes predeterminados. |
    | **\*.anaconda.org** | Se usa para obtener datos del repositorio. |
    | **pypi.org** | Se usa para enumerar las dependencias del índice predeterminado, si hay alguna, y el índice no se sobrescribe con la configuración del usuario. Si el índice se sobrescribe, también debe permitir **\*.pythonhosted.org**. |
    | **cloud.r-project.org** | Se usar al instalar paquetes CRAN para el desarrollo en R. |
    | **\*pytorch.org** | Se usa en algunos ejemplos basados en PyTorch. |
    | **\*.tensorflow.org** | Se usa en algunos ejemplos basados en TensorFlow. |
    | **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Se usa para recuperar bits de servidor de VS Code que se instalan en la instancia de proceso por medio de un script de instalación.|
    | **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Se usa para recuperar bits de servidor de WebSocket que se instalan en la instancia de proceso. El servidor de WebSocket se usa para transmitir solicitudes desde el cliente de Visual Studio Code (aplicación de escritorio) al servidor de Visual Studio Code que se ejecuta en la instancia de proceso.|
    

    En __Protocolo:Puerto__, seleccione usar __http, https__.

    Para obtener más información sobre la configuración de reglas de aplicación, consulte [Implementación y configuración de Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Para restringir el tráfico de salida para los modelos implementados en Azure Kubernetes Service (AKS), consulte los artículos [Control del tráfico de salida de los nodos de clúster en Azure Kubernetes Service (AKS)](../aks/limit-egress-traffic.md) e [Implementación de un modelo en un clúster de Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Service

Al usar Azure Kubernetes Service con Azure Machine Learning, se debe permitir el tráfico siguiente:

* Requisitos generales de entrada y salida para AKS, tal como se describe en el artículo [Restricción del tráfico de salida en Azure Kubernetes Service](../aks/limit-egress-traffic.md).
* __Tráfico de salida__ a mcr.microsoft.com.
* Al implementar un modelo en un clúster de AKS, use las instrucciones del artículo [Implementación de modelos de ML en Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="diagnostics-for-support"></a>Diagnósticos de soporte técnico

Si necesita recopilar información de diagnóstico al trabajar con el soporte técnico de Microsoft, siga estos pasos:

1. Agregue una __regla de red__ para permitir el tráfico hacia la etiqueta `AzureMonitor` y desde esta.
1. Agregue __reglas de aplicación__ para los hosts siguientes. Seleccione __http, https__ como __Protocolo:Puerto__ para estos hosts:

    + **dc.applicationinsights.azure.com**
    + **dc.applicationinsights.microsoft.com**
    + **dc.services.visualstudio.com**

    Para obtener una lista de las direcciones IP para los hosts de Azure Monitor, vea [Direcciones IP usadas por Azure Monitor](../azure-monitor/app/ip-addresses.md).

## <a name="other-firewalls"></a>Otros firewalls

La guía de esta sección es genérica, ya que cada firewall tiene su propia terminología y configuraciones específicas. Si tiene alguna duda, consulte la documentación del firewall que utiliza.

Si no está configurado correctamente, el firewall puede provocar problemas al usar el área de trabajo. Hay una serie de nombres de host que se usan en el área de trabajo de Azure Machine Learning. En las secciones siguientes se enumeran los hosts necesarios para Azure Machine Learning.

### <a name="microsoft-hosts"></a>Hosts de Microsoft

Los hosts de las siguientes tablas son propiedad de Microsoft y proporcionan servicios necesarios para que el área de trabajo funcione correctamente. Las tablas enumeran los hosts para las regiones de Azure público, Azure Government y Azure China 21Vianet.

**Hosts generales de Azure**

| **Requerido para** | **Azure público** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Hosts de Azure Machine Learning**

> [!IMPORTANT]
> En la tabla siguiente, reemplace `<storage>` por el nombre de la cuenta de almacenamiento predeterminada para el área de trabajo de Azure Machine Learning.

| **Requerido para** | **Azure público** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Cuaderno integrado | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| Cuaderno integrado | \<storage\>.file.core.windows.net | \<storage\>.file.core.usgovcloudapi.net | \<storage\>.file.core.chinacloudapi.cn |
| Cuaderno integrado | \<storage\>.dfs.core.windows.net | \<storage\>.dfs.core.usgovcloudapi.net | \<storage\>.dfs.core.chinacloudapi.cn |
| Cuaderno integrado | \<storage\>.blob.core.windows.net | \<storage\>.blob.core.usgovcloudapi.net | \<storage\>.blob.core.chinacloudapi.cn |
| Cuaderno integrado | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Cuaderno integrado | \*.aznbcontent.net |  | |

**Hosts de instancia de proceso y clúster de proceso de Azure Machine Learning**

| **Requerido para** | **Azure público** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Instancia/clúster de proceso | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Instancia de proceso | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| Instancia de proceso | \*.instances.azureml.ms |  |  |
| Cuenta de Azure Storage | \*blob.core.windows.net</br>\*.table.core.windows.net</br>\*.queue.core.windows.net | \*.blob.core.usgovcloudapi.net</br>\*.table.core.usgovcloudapi.net</br>\*.queue.core.usgovcloudapi.net | \*blob.core.chinacloudapi.cn</br>\*.table.core.chinacloudapi.cn</br>\*.queue.core.chinacloudapi.cn |
| Azure Key Vault | \*.vault.azure.net | \*.vault.usgovcloudapi.net | \*.vault.azure.cn |

> [!IMPORTANT]
> El firewall debe permitir la comunicación con \*.instances.azureml.ms a través de los puertos __TCP__ __18881, 443 y 8787__.

**Imágenes de Docker que mantiene Azure Machine Learning**

| **Requerido para** | **Azure público** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Registro de contenedor de Microsoft | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Imágenes precompiladas de Azure Machine Learning | viennaglobal.azurecr.io | viennaglobal.azurecr.io | viennaglobal.azurecr.io |

> [!TIP]
> * __Azure Container Registry__ es necesario para cualquier imagen personalizada de Docker. Esto incluye pequeñas modificaciones (como paquetes adicionales) en las imágenes base proporcionadas por Microsoft.
> * __Microsoft Container Registry__ solo es necesario si planea usar las _imágenes de Docker predeterminadas proporcionadas por Microsoft_ y _habilitar las dependencias administradas por el usuario_.
> * Si tiene previsto usar la identidad federada, siga el artículo [Procedimientos recomendados para proteger los Servicios de federación de Active Directory (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs).

Además, use la información de la sección [Configuración de entrada ](#inbound-configuration) para agregar direcciones IP para `BatchNodeManagement` y `AzureMachineLearning`.

Para obtener información sobre la restricción del acceso a los modelos implementados en AKS, consulte [Control del tráfico de salida de los nodos de clúster en Azure Kubernetes Service (AKS)](../aks/limit-egress-traffic.md).

> [!TIP]
> Si trabaja con el servicio de soporte técnico de Microsoft para recopilar información de diagnóstico, debe permitir el tráfico saliente a las direcciones IP que usan los hosts de Azure Monitor. Para obtener una lista de las direcciones IP para los hosts de Azure Monitor, vea [Direcciones IP usadas por Azure Monitor](../azure-monitor/app/ip-addresses.md).

### <a name="python-hosts"></a>Hosts de Python

Los hosts de esta sección se usan para instalar paquetes de Python y son necesarios durante el desarrollo, el entrenamiento y la implementación. 

> [!NOTE]
> Esta no es una lista completa de los hosts necesarios para todos los recursos de Python en Internet, solo el que se usa con más frecuencia. Por ejemplo, si necesita acceder a un repositorio de GitHub o a otro host, debe identificar y agregar los hosts necesarios para ese escenario.

| **Nombre de host** | **Propósito** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | Se usa para instalar paquetes predeterminados. |
| **\*.anaconda.org** | Se usa para obtener datos del repositorio. |
| **pypi.org** | Se usa para enumerar las dependencias del índice predeterminado, si hay alguna, y el índice no se sobrescribe con la configuración del usuario. Si el índice se sobrescribe, también debe permitir **\*.pythonhosted.org**. |
| **\*pytorch.org** | Se usa en algunos ejemplos basados en PyTorch. |
| **\*.tensorflow.org** | Se usa en algunos ejemplos basados en TensorFlow. |

### <a name="r-hosts"></a>Hosts de R

Los hosts de esta sección se usan para instalar paquetes de R y son necesarios durante el desarrollo, el entrenamiento y la implementación.

> [!NOTE]
> Esta no es una lista completa de los hosts necesarios para todos los recursos de R en Internet, solo el que se usa con más frecuencia. Por ejemplo, si necesita acceder a un repositorio de GitHub o a otro host, debe identificar y agregar los hosts necesarios para ese escenario.

| **Nombre de host** | **Propósito** |
| ---- | ---- |
| **cloud.r-project.org** | Usado al instalar paquetes CRAN |

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Service

Al usar Azure Kubernetes Service con Azure Machine Learning, se debe permitir el tráfico siguiente:

* Requisitos generales de entrada y salida para AKS, tal como se describe en el artículo [Restricción del tráfico de salida en Azure Kubernetes Service](../aks/limit-egress-traffic.md).
* __Tráfico de salida__ a mcr.microsoft.com.
* Al implementar un modelo en un clúster de AKS, use las instrucciones del artículo [Implementación de modelos de ML en Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="visual-studio-code-hosts"></a>Hosts de Visual Studio Code

Los hosts de esta sección se usan para instalar paquetes de Visual Studio Code y establecer una conexión remota entre Visual Studio Code y las instancias de proceso del área de trabajo de Azure Machine Learning.

> [!NOTE]
> No es una lista completa de los hosts necesarios para todos los recursos de Visual Studio Code en Internet, solo de los que se usan con más frecuencia. Por ejemplo, si necesita acceder a un repositorio de GitHub o a otro host, debe identificar y agregar los hosts necesarios para ese escenario.

| **Nombre de host** | **Propósito** |
| ---- | ---- |
|  **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Se usa para recuperar bits de servidor de VS Code que se instalan en la instancia de proceso por medio de un script de instalación.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** |Se usa para recuperar bits de servidor de WebSocket que se instalan en la instancia de proceso. El servidor de WebSocket se usa para transmitir solicitudes desde el cliente de Visual Studio Code (aplicación de escritorio) al servidor de Visual Studio Code que se ejecuta en la instancia de proceso. |

## <a name="next-steps"></a>Pasos siguientes

Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:

* [Información general sobre redes virtuales](how-to-network-security-overview.md)
* [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
* [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
* [Protección de un entorno de inferencia](how-to-secure-inferencing-vnet.md)
* [Habilitación de Azure Machine Learning Studio en una red virtual](how-to-enable-studio-virtual-network.md)
* [Uso de un DNS personalizado](how-to-custom-dns.md)

Para más información sobre la configuración de Azure Firewall, consulte [Implementación y configuración de Azure Firewall mediante Azure Portal](../firewall/tutorial-firewall-deploy-portal.md).
