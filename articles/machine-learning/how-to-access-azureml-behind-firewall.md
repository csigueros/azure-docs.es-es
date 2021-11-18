---
title: Configuración del tráfico de red entrante y saliente
titleSuffix: Azure Machine Learning
description: Cómo configurar el tráfico de red entrante y saliente necesario cuando se usa un área de trabajo de Azure Machine Learning segura.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/05/2021
ms.custom: devx-track-python, ignite-fall-2021
ms.openlocfilehash: d566f40bfeef4e49e85cacb8183509b512d52715
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725021"
---
# <a name="configure-inbound-and-outbound-network-traffic"></a>Configuración del tráfico de red entrante y saliente

En este artículo, obtendrá información sobre los requisitos de comunicación de red al proteger el área de trabajo de Azure Machine Learning en una red virtual (VNet). Incluye el procedimiento de configuración de Azure Firewall para controlar el acceso al área de trabajo de Azure Machine Learning y a la red pública de Internet. Para obtener más información sobre la protección de Azure Machine Learning, consulte [Seguridad de empresa para Azure Machine Learning](concept-enterprise-security.md).

> [!NOTE]
> La información de este artículo se aplica al área de trabajo de Azure Machine Learning configurada con un punto de conexión privado.

> [!TIP]
> Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:
>
> * [Información general sobre redes virtuales](how-to-network-security-overview.md)
> * [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
> * [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
> * [Protección del entorno de inferencia](how-to-secure-inferencing-vnet.md)
> * [Habilitación de la función de Studio](how-to-enable-studio-virtual-network.md)
> * [Uso de un DNS personalizado](how-to-custom-dns.md)

## <a name="well-known-ports"></a>Puertos conocidos

Los siguientes son puertos conocidos que usan los servicios enumerados en este artículo. Si en este artículo se usa un intervalo de puertos que no aparece en esta sección, es específico del servicio y es posible que no haya publicada información acerca de para qué se usa:


| Port | Descripción |
| ----- | ----- | 
| 80 | Tráfico web no seguro (HTTP) |
| 443 | Tráfico web seguro (HTTPS) |
| 445 | Tráfico SMB usado para acceder a recursos compartidos en Azure File Storage |
| 8787 | Se usa al conectarse a RStudio en una instancia de proceso |

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
    | AzureActiveDirectory | TCP | 80, 443 |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* No es necesario en Azure China. | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |
    | Keyvault.region | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.region solo es necesario para imágenes personalizadas de Docker. Esto incluye pequeñas modificaciones (como paquetes adicionales) en las imágenes base proporcionadas por Microsoft.
    > * MicrosoftContainerRegistry.region solo es necesario si planea usar las _imágenes de Docker predeterminadas proporcionadas por Microsoft_ y _habilitar las dependencias administradas por el usuario_.
    > * Keyvault.region solo es necesario si el área de trabajo se creó con la marca [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) habilitada.
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
    | **dc.applicationinsights.azure.com** | Se usa para recopilar métricas e información de diagnóstico cuando se trabaja con el equipo de Soporte técnico de Microsoft. |
    | **dc.applicationinsights.microsoft.com** | Se usa para recopilar métricas e información de diagnóstico cuando se trabaja con el equipo de Soporte técnico de Microsoft. |
    | **dc.services.visualstudio.com** | Se usa para recopilar métricas e información de diagnóstico cuando se trabaja con el equipo de Soporte técnico de Microsoft. | 
    

    En __Protocolo:Puerto__, seleccione usar __http, https__.

    Para obtener más información sobre la configuración de reglas de aplicación, consulte [Implementación y configuración de Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Para restringir el tráfico de salida para los modelos implementados en Azure Kubernetes Service (AKS), consulte los artículos [Control del tráfico de salida de los nodos de clúster en Azure Kubernetes Service (AKS)](../aks/limit-egress-traffic.md) e [Implementación de un modelo en un clúster de Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Service

Al usar Azure Kubernetes Service con Azure Machine Learning, se debe permitir el tráfico siguiente:

* Requisitos generales de entrada y salida para AKS, tal como se describe en el artículo [Restricción del tráfico de salida en Azure Kubernetes Service](../aks/limit-egress-traffic.md).
* __Tráfico de salida__ a mcr.microsoft.com.
* Al implementar un modelo en un clúster de AKS, use las instrucciones del artículo [Implementación de modelos de ML en Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

## <a name="other-firewalls"></a>Otros firewalls

La guía de esta sección es genérica, ya que cada firewall tiene su propia terminología y configuraciones específicas. Si tiene alguna duda, consulte la documentación del firewall que utiliza.

Si no está configurado correctamente, el firewall puede provocar problemas al usar el área de trabajo. Hay una serie de nombres de host que se usan en el área de trabajo de Azure Machine Learning. En las secciones siguientes se enumeran los hosts necesarios para Azure Machine Learning.

### <a name="microsoft-hosts"></a>Hosts de Microsoft

Los hosts de las siguientes tablas son propiedad de Microsoft y proporcionan servicios necesarios para que el área de trabajo funcione correctamente. Las tablas enumeran los hosts para las regiones de Azure público, Azure Government y Azure China 21Vianet.

> [!IMPORTANT]
> Azure Machine Learning usa cuentas de Azure Storage en su suscripción y en suscripciones administradas por Microsoft. Cuando corresponda, se usarán los siguientes términos para diferenciarlas en esta sección:
>
> * __Su almacenamiento__: las cuentas de Azure Storage de su suscripción, que se usan para almacenar sus datos y artefactos, como modelos, datos de entrenamiento, registros de entrenamiento y scripts de Python.
> * __Almacenamiento de Microsoft__: la instancia de proceso de Azure Machine Learning y los clústeres de proceso se basan en Azure Batch y deben acceder al almacenamiento ubicado en una suscripción de Microsoft. Este almacenamiento solo se usa para la administración de las instancias de proceso. Ninguno de sus datos se almacena aquí.

**Hosts generales de Azure**

# <a name="azure-public"></a>[Azure público](#tab/public)

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ---- | 
| Azure Active Directory | login.microsoftonline.com | TCP | 80, 443 |
| Azure portal | management.azure.com | TCP | 443 |
| Azure Resource Manager | management.azure.com | TCP | 443 |

# <a name="azure-government"></a>[Azure Government](#tab/gov)

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ---- |
| Azure Active Directory | login.microsoftonline.us | TCP | 80, 443 |
| Portal de Azure | management.azure.us | TCP | 443 |
| Azure Resource Manager | management.usgovcloudapi.net | TCP | 443 |

# <a name="azure-china-21vianet"></a>[Azure China 21Vianet](#tab/china)

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.chinacloudapi.cn | TCP | 80, 443 |
| Portal de Azure | management.azure.cn | TCP | 443 |
| Azure Resource Manager | management.chinacloudapi.cn | TCP | 443 |

---

**Hosts de Azure Machine Learning**

> [!IMPORTANT]
> En la tabla siguiente, reemplace `<storage>` por el nombre de la cuenta de almacenamiento predeterminada para el área de trabajo de Azure Machine Learning.

# <a name="azure-public"></a>[Azure público](#tab/public)

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | TCP | 443 |
| API |\*.azureml.ms | TCP | 443 |
| Cuaderno integrado | \*.notebooks.azure.net | TCP | 443 |
| Cuaderno integrado | \<storage\>.file.core.windows.net | TCP | 443, 445 |
| Cuaderno integrado | \<storage\>.dfs.core.windows.net | TCP | 443 |
| Cuaderno integrado | \<storage\>.blob.core.windows.net | TCP | 443 |
| Cuaderno integrado | graph.microsoft.com | TCP | 443 |
| Cuaderno integrado | \*.aznbcontent.net | TCP | 443 |

# <a name="azure-government"></a>[Azure Government](#tab/gov)

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.us | TCP | 443 |
| API | \*.ml.azure.us | TCP | 443 |
| Cuaderno integrado | \*.notebooks.usgovcloudapi.net | TCP | 443 |
| Cuaderno integrado | \<storage\>.file.core.usgovcloudapi.net | TCP | 443, 445 |
| Cuaderno integrado | \<storage\>.dfs.core.usgovcloudapi.net | TCP | 443 |
| Cuaderno integrado  | \<storage\>.blob.core.usgovcloudapi.net | TCP | 443 |
| Cuaderno integrado | graph.microsoft.us | TCP | 443 |
| Cuaderno integrado | \*.aznbcontent.net | TCP | 443 |

# <a name="azure-china-21vianet"></a>[Azure China 21Vianet](#tab/china)

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | studio.ml.azure.cn | TCP | 443 |
| API | \*.ml.azure.cn | TCP | 443 |
| Cuaderno integrado | \*.notebooks.chinacloudapi.cn | TCP | 443 |
| Cuaderno integrado | \<storage\>.file.core.chinacloudapi.cn | TCP | 443, 445 |
| Cuaderno integrado | \<storage\>.dfs.core.chinacloudapi.cn | TCP | 443 |
| Cuaderno integrado | \<storage\>.blob.core.chinacloudapi.cn | TCP | 443 |
| Cuaderno integrado | graph.chinacloudapi.cn | TCP | 443 |
| Cuaderno integrado | \*.aznbcontent.net | TCP | 443 |

---

**Hosts de instancia de proceso y clúster de proceso de Azure Machine Learning**

> [!TIP]
> * El host de __Azure Key Vault__ solo es necesario si el área de trabajo se creó con la marca [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) habilitada.
> * Los puertos 8787 y 18881 para la __instancia de proceso__ solo son necesarios cuando el área de trabajo de Azure Machine Learning tiene un punto de conexión privado.
> * En la tabla siguiente, reemplace `<storage>` por el nombre de la cuenta de almacenamiento predeterminada para el área de trabajo de Azure Machine Learning.

# <a name="azure-public"></a>[Azure público](#tab/public)

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ----- |
| Instancia/clúster de proceso | graph.windows.net | TCP | 443 |
| Instancia de proceso | \*.instances.azureml.net | TCP | 443 |
| Instancia de proceso | \*.instances.azureml.ms | TCP | 443, 8787, 18881 |
| Acceso al almacenamiento de Microsoft | \*.blob.core.windows.net | TCP | 443 |
| Acceso al almacenamiento de Microsoft | \*.table.core.windows.net | TCP | 443 |
| Acceso al almacenamiento de Microsoft | \*.queue.core.windows.net | TCP | 443 |
| Cuenta de almacenamiento | \<storage\>.file.core.windows.net | TCP | 443, 445 |
| Cuenta de almacenamiento | \<storage\>.blob.core.windows.net | TCP | 443 |
| Azure Key Vault | \*.vault.azure.net | TCP | 443 |

# <a name="azure-government"></a>[Azure Government](#tab/gov)

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ----- |
| Instancia/clúster de proceso | graph.windows.net | TCP | 443 |
| Instancia de proceso | \*.instances.azureml.us | TCP | 443 |
| Instancia de proceso | \*.instances.azureml.ms | TCP | 443, 8787, 18881 |
| Acceso al almacenamiento de Microsoft | \*.blob.core.usgovcloudapi.net | TCP | 443 |
| Acceso al almacenamiento de Microsoft | \*.table.core.usgovcloudapi.net | TCP | 443 |
| Acceso al almacenamiento de Microsoft | \*.queue.core.usgovcloudapi.net | TCP | 443 |
| Cuenta de almacenamiento | \<storage\>.file.core.usgovcloudapi.net | TCP | 443, 445 |
| Cuenta de almacenamiento | \<storage\>.blob.core.usgovcloudapi.net | TCP | 443 |
| Azure Key Vault | \*.vault.usgovcloudapi.net | TCP | 443 |

# <a name="azure-china-21vianet"></a>[Azure China 21Vianet](#tab/china)

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ----- |
| Instancia/clúster de proceso | graph.chinacloudapi.cn | TCP | 443 |
| Instancia de proceso |  \*.instances.azureml.cn | TCP | 443 |
| Instancia de proceso | \*.instances.azureml.ms | TCP | 443, 8787, 18881 |
| Acceso al almacenamiento de Microsoft | \*blob.core.chinacloudapi.cn | TCP | 443 |
| Acceso al almacenamiento de Microsoft | \*.table.core.chinacloudapi.cn | TCP | 443 |
| Acceso al almacenamiento de Microsoft | \*.queue.core.chinacloudapi.cn | TCP | 443 |
| Cuenta de almacenamiento | \<storage\>.file.core.chinacloudapi.cn | TCP | 443, 445 |
| Cuenta de almacenamiento | \<storage\>.blob.core.chinacloudapi.cn | TCP | 443 |
| Azure Key Vault | \*.vault.azure.cn | TCP | 443 |

---

**Imágenes de Docker que mantiene Azure Machine Learning**

| **Requerido para** | **Hosts** | **Protocolo** | **Puertos** |
| ----- | ----- | ----- | ----- |
| Registro de contenedor de Microsoft | mcr.microsoft.com | TCP | 443 |
| Imágenes precompiladas de Azure Machine Learning | viennaglobal.azurecr.io | TCP | 443 |

> [!TIP]
> * __Azure Container Registry__ es necesario para cualquier imagen personalizada de Docker. Esto incluye pequeñas modificaciones (como paquetes adicionales) en las imágenes base proporcionadas por Microsoft.
> * __Microsoft Container Registry__ solo es necesario si planea usar las _imágenes de Docker predeterminadas proporcionadas por Microsoft_ y _habilitar las dependencias administradas por el usuario_.
> * Si tiene previsto usar la identidad federada, siga el artículo [Procedimientos recomendados para proteger los Servicios de federación de Active Directory (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs).

Además, use la información de la sección [Configuración de entrada ](#inbound-configuration) para agregar direcciones IP para `BatchNodeManagement` y `AzureMachineLearning`.

Para obtener información sobre la restricción del acceso a los modelos implementados en AKS, consulte [Control del tráfico de salida de los nodos de clúster en Azure Kubernetes Service (AKS)](../aks/limit-egress-traffic.md).

**Soporte técnico y diagnóstico**

Para que el equipo de Soporte técnico de Microsoft pueda diagnosticar los problemas que se puedan encontrar con el área de trabajo, debe permitir el tráfico saliente a los hosts siguientes:

* **dc.applicationinsights.azure.com**
* **dc.applicationinsights.microsoft.com**
* **dc.services.visualstudio.com**

Para obtener una lista de las direcciones IP de esos hosts, consulte [Direcciones IP usadas por Azure Monitor](../azure-monitor/app/ip-addresses.md).

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

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes habilitado para Azure Arc <a id="arc-kubernetes"></a>

Los clústeres de Kubernetes habilitados para Azure Arc dependen de las conexiones de Azure Arc. Asegúrese de cumplir los [requisitos de red de Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md?tabs=azure-cli#meet-network-requirements).

Los hosts de esta sección se usan para implementar la extensión de Azure Machine Learning en clústeres de Kubernetes y enviar cargas de trabajo de entrenamiento e inferencia a los clústeres.

**Implementación de la extensión de Azure Machine Learning**

Habilite el acceso saliente a los siguientes puntos de conexión al implementar la extensión de Azure Machine Learning en el clúster.

| Punto de conexión de destino| Port | Uso |
|--|--|--|
|  *.data.mcr.microsoft.com| https:443 | Obligatorio para el almacenamiento de MCR respaldado por Azure Content Delivery Network (CDN). |
| quay.io, *.quay.io | https:443 | Registro de quay.io, necesario para extraer imágenes de contenedor para los componentes de la extensión de AML. |
| gcr.io| https:443 | Repositorio de Google Cloud, necesario para extraer imágenes de contenedor para los componentes de la extensión de AML. |
| storage.googleapis.com | https:443 | Google Cloud Storage, donde se hospedan imágenes de GCR. |
| registry-1.docker.io, production.cloudflare.docker.com  | https:443 | Registro de Docker Hub, necesario para extraer imágenes de contenedor para los componentes de la extensión de AML. |
| auth.docker.io| https:443 | Autenticación del repositorio de Docker, necesaria para acceder al registro de Docker Hub. |
| *.kusto.windows.net, *.table.core.windows.net, *.queue.core.windows.net | https:443 | Necesario para cargar y analizar registros del sistema en Kusto. |

**Solo cargas de trabajo de entrenamiento**

Habilite el acceso saliente a los siguientes puntos de conexión para enviar cargas de trabajo de entrenamiento al clúster.

| Punto de conexión de destino| Port | Uso |
|--|--|--|
| pypi.org | https:443 | Índice de paquetes de Python, para instalar paquetes pip usados para inicializar el entorno de trabajo. |
| archive.ubuntu.com, security.ubuntu.com, ppa.launchpad.net | http:80 | Esta dirección permite al contenedor de inicialización descargar las actualizaciones y los parches de seguridad necesarios. |

**Cargas de trabajo de entrenamiento e inferencia**

Además de los puntos de conexión para las cargas de trabajo de entrenamiento, habilite el acceso saliente para que los siguientes puntos de conexión envíen cargas de trabajo de entrenamiento e inferencia.

| Punto de conexión de destino| Port | Uso |
|--|--|--|
| *.azurecr.io | https:443 | Azure Container Registry, necesario para extraer imágenes de contenedor a fin de hospedar trabajos de entrenamiento o inferencia.|
| *.blob.core.windows.net | https:443 | Azure Blob Storage, necesario para capturar scripts de proyectos de aprendizaje automático, imágenes de contenedor y registros y métricas de trabajo. |
| *.workspace.\<region\>.api.azureml.ms, \<region\>.experiments.azureml.net, \<region\>.api.azureml.ms | https:443 | API de servicio de Azure Machine Learning, necesaria para comunicarse con AML. |

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
* [Protección del entorno de inferencia](how-to-secure-inferencing-vnet.md)
* [Habilitación de la función de Studio](how-to-enable-studio-virtual-network.md)
* [Uso de un DNS personalizado](how-to-custom-dns.md)

Para más información sobre la configuración de Azure Firewall, consulte [Implementación y configuración de Azure Firewall mediante Azure Portal](../firewall/tutorial-firewall-deploy-portal.md).