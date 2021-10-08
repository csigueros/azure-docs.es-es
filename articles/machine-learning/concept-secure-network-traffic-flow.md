---
title: Protección del flujo de tráfico de red
titleSuffix: Azure Machine Learning
description: Obtenga información sobre el flujo del tráfico de red entre los componentes cuando el área de trabajo de Azure Machine Learning está en una red virtual protegida.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/22/2021
ms.openlocfilehash: 033bd5a96c75a1097438e01390ba800089d89d1d
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426932"
---
# <a name="network-traffic-flow-when-using-a-secured-workspace"></a>Flujo de tráfico de red al usar un área de trabajo protegida

Cuando el área de trabajo de Azure Machine Learning y los recursos asociados están protegidos en una instancia de Azure Virtual Network, cambia el tráfico de red entre los recursos. Sin una red virtual, el tráfico de red fluye a través de la red pública de Internet o en un centro de datos de Azure. Una vez que se introduce una red virtual (VNet), es posible que también desee proteger la seguridad de la red. Por ejemplo, el bloqueo de las comunicaciones de entrada y salida entre la red virtual y la red pública de Internet. Sin embargo, Azure Machine Learning requiere acceso a algunos recursos de la red pública de Internet. Por ejemplo, la administración de recursos de Azure se usa para implementaciones y operaciones de administración.

En este artículo se incluye el tráfico necesario hacia y desde la red pública de Internet. También se explica cómo fluye el tráfico de red entre el entorno de desarrollo de cliente y un área de trabajo de Azure Machine Learning protegida en los escenarios siguientes:

* Utilización del __Estudio__ de Azure Machine Learning para trabajar con:

    * El área de trabajo
    * AutoML
    * Diseñador
    * Conjuntos de datos y almacenes de datos

    > [!TIP]
    > El Estudio de Azure Machine Learning es una interfaz de usuario basada en web que se ejecuta parcialmente en el explorador web y realiza llamadas a servicios de Azure para realizar tareas como entrenar un modelo, usar el diseñador o ver conjuntos de datos. Algunas de estas llamadas usan un flujo de comunicación diferente al que está presente cuando utiliza el SDK, la CLI, la API de REST o VS Code.

* Utilización del __Estudio__ de Azure Machine Learning el __SDK__, la __CLI__ o la __API de REST__ para trabajar con:

    * Clústeres e instancias de proceso
    * Azure Kubernetes Service
    * Imágenes de Docker administradas por Azure Machine Learning

> [!TIP]
> Si un escenario o tarea no aparece aquí, debería funcionar igual con o sin un área de trabajo protegida.

## <a name="assumptions"></a>Supuestos

En este artículo se da por hecho la siguiente configuración:

* El área de trabajo de Azure Machine Learning usa un punto de conexión privado para comunicarse con la red virtual.
* La cuenta de Azure Storage, Key Vault y Container Registry que usa el área de trabajo también usan un punto de conexión privado para comunicarse con la red virtual.
* Las estaciones de trabajo cliente usan una puerta de enlace de VPN o ExpressRoute para acceder a la red virtual.

## <a name="inbound-and-outbound-requirements"></a>Requisitos de entrada y salida


| __Escenario__ | __Entrada necesaria__ | __Salida necesaria__ | __Configuración adicional__ | 
| ----- | ----- | ----- | ----- |
| [Acceso al área de trabajo desde el estudio](#scenario-access-workspace-from-studio) | NA | <ul><li>Azure Active Directory</li><li>Azure Front Door</li><li>Azure Machine Learning Service</li></ul> | Es posible que tenga que usar un servidor DNS personalizado. Para obtener más información, consulte [Uso de un área de trabajo con un servidor DNS personalizado](how-to-custom-dns.md). | 
| [Uso de AutoML, el diseñador, el conjunto de datos y el almacén de datos del estudio](#scenario-use-automl-designer-dataset-and-datastore-from-studio) | NA | NA | <ul><li>Configuración de la entidad de servicio del área de trabajo</li><li>Permitir el acceso desde servicios de Azure de confianza</li></ul>Para obtener más información, consulte [Protección de un área de trabajo en una red virtual](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts). | 
| [Uso de una instancia de proceso y un clúster de proceso](#scenario-use-compute-instance-and-compute-cluster) | <ul><li>Azure Machine Learning Service en el puerto 44224</li><li>Administración de Azure Batch en los puertos 29876-29877</li></ul> | <ul><li>Azure Active Directory</li><li>Azure Resource Manager</li><li>Azure Machine Learning Service</li><li>Cuenta de Azure Storage</li><li>Azure Key Vault</li></ul> | Si usa un firewall, cree rutas definidas por el usuario. Para obtener más información, consulte [Configuración del tráfico de entrada y salida](how-to-access-azureml-behind-firewall.md). | 
| [Uso de Azure Kubernetes Service](#scenario-use-azure-kubernetes-service) | NA | Para obtener información sobre la configuración de salida de AKS, consulte [Implementación en Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster). | Configure el equilibrador de carga interno. Para obtener más información, consulte [Implementación en Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster). | 
| [Uso de imágenes de Docker administradas por Azure Machine Learning](#scenario-use-docker-images-managed-by-azure-ml) | NA | <ul><li>Registro de contenedor de Microsoft</li><li>Registro de contenedor global de `viennaglobal.azurecr.io`</li></ul> | Si la instancia de Azure Container Registry del área de trabajo está detrás de la red virtual, configure el área de trabajo con el fin de que use un clúster de proceso para compilar imágenes. Para obtener más información, consulte [Protección de un área de trabajo en una red virtual](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr). | 


## <a name="scenario-access-workspace-from-studio"></a>Escenario: Acceso al área de trabajo desde el estudio

> [!NOTE]
> La información de esta sección es específica para el uso del área de trabajo desde el Estudio de Azure Machine Learning. Si usa el SDK de Azure Machine Learning, la API de REST, la CLI o Visual Studio Code, la información de esta sección no se aplica a usted.

Al acceder al área de trabajo desde el estudio, los flujos de tráfico de red son los siguientes:

* Para autenticarse en los recursos, se usa __Azure Active Directory__.
* Para las operaciones de implementación y administración, se usa __Azure Resource Manager__.
* Para las tareas específicas de Azure Machine Learning, se usa __Azure Machine Learning Service__.
* Para acceder al Estudio de Azure Machine Learning (https://ml.azure.com) ), se usa __Azure FrontDoor__.
* Para la mayoría de las operaciones de almacenamiento, el tráfico fluye a través del punto de conexión privado del almacenamiento predeterminado del área de trabajo. Las excepciones se abordan en la sección [Uso de AutoML, el diseñador, el conjunto de datos y el almacén de datos](#scenario-use-automl-designer-dataset-and-datastore-from-studio).
* También debe configurar una solución DNS que le permita resolver los nombres de los recursos de la red virtual. Para obtener más información, consulte [Uso de un área de trabajo con un servidor DNS personalizado](how-to-custom-dns.md).

:::image type="content" source="./media/concept-secure-network-traffic-flow/workspace-traffic-studio.png" alt-text="Diagrama del tráfico de red entre el cliente y el área de trabajo al usar el estudio":::

## <a name="scenario-use-automl-designer-dataset-and-datastore-from-studio"></a>Escenario: Uso de AutoML, el diseñador, el conjunto de datos y el almacén de datos del estudio

Las características del Estudio de Azure Machine Learning que se señalan a continuación usan la _generación de perfiles de datos_:

* Conjunto de datos: explore el conjunto de datos desde el estudio.
* Diseñador: visualice los datos de salida del módulo.
* AutoML: consulte un perfil o una vista previa de datos y elija una columna de destino.
* Etiquetado

La generación de perfiles de datos depende de si el servicio administrado de Azure Machine Learning puede acceder a la cuenta de Azure Storage predeterminada del área de trabajo. El servicio administrado _no existe en la red virtual_, por lo que no puede acceder directamente a la cuenta de almacenamiento de la red virtual. En su lugar, el área de trabajo usa una entidad de servicio para acceder al almacenamiento.

> [!TIP]
> Puede proporcionar una entidad de servicio al crear el área de trabajo. Si no lo hace, se crea una automáticamente y tendrá el mismo nombre que el área de trabajo.

Para permitir el acceso a la cuenta de almacenamiento, establezca la configuración __Permitir que los servicios de Azure de la lista de servicios de confianza accedan a esta cuenta de almacenamiento__. Esta configuración permite que el servicio administrado acceda al almacenamiento a través de la red del centro de datos de Azure. 

A continuación, agregue la entidad de servicio del área de trabajo al rol de __Lector__ en el punto de conexión privado de la cuenta de almacenamiento. Este rol se usa para comprobar la información del área de trabajo y la subred de almacenamiento. Si son iguales, se permite el acceso. Por último, la entidad de servicio también requiere acceso de __Colaborador de datos de blob__ para la cuenta de almacenamiento.

Para obtener más información, consulte la sección [Protección de un área de trabajo en una red virtual](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts) de la cuenta Azure Storage.

:::image type="content" source="./media/concept-secure-network-traffic-flow/storage-traffic-studio.png" alt-text="Diagrama del tráfico entre el cliente, la generación de perfiles de datos y el almacenamiento":::

## <a name="scenario-use-compute-instance-and-compute-cluster"></a>Escenario: Uso de una instancia de proceso y un clúster de proceso

La instancia y el clúster de proceso de Azure Machine Learning son servicios administrados hospedados por Microsoft. Se basan en el servicio Azure Batch. Aunque existen en un entorno administrado por Microsoft, también se insertan en la red virtual.

Al crear una instancia o un clúster de proceso, también se crean los siguientes recursos en la red virtual:

* Un grupo de seguridad de red con las reglas de salida necesarias. Estas reglas permiten el acceso __de entrada__ desde Azure Machine Learning (TCP en el puerto 44224) y el servicio Azure Batch (TCP en los puertos 29876-29877).

    > [!IMPORTANT]
    > Si usa un firewall para bloquear el acceso a Internet en la red virtual, debe configurar el firewall para permitir este tráfico. Por ejemplo, con Azure Firewall puede crear rutas definidas por el usuario. Para obtener más información, consulte [Uso de Azure Machine Learning con un firewall](how-to-access-azureml-behind-firewall.md#inbound-configuration).

* Un equilibrador de carga con una dirección IP pública.

Permita también el acceso __de salida__ a las siguientes etiquetas de servicio. Para cada etiqueta, reemplace `region` por la región de Azure de la instancia o el clúster de proceso:

* `Storage.region`: este acceso de salida se usa para conectarse a la cuenta de Azure Storage en la VNET administrada por el servicio Azure Batch.
* `Keyvault.region`: este acceso de salida se usa para conectarse a la cuenta de Azure Key Vault en la VNET administrada por el servicio Azure Batch.

El acceso a los datos desde la instancia o el clúster de proceso pasa a través del punto de conexión privado de la cuenta de Storage de la red virtual.

Si usa Visual Studio Code en una instancia de proceso, debe permitir otro tráfico de salida. Para obtener más información, consulte [Uso de Azure Machine Learning con un firewall](how-to-access-azureml-behind-firewall.md).

:::image type="content" source="./media/concept-secure-network-traffic-flow/compute-instance-and-cluster.png" alt-text="Diagrama del flujo de tráfico al usar una instancia o clúster de proceso":::

## <a name="scenario-use-azure-kubernetes-service"></a>Escenario: Uso de Azure Kubernetes Service

Para obtener información sobre la configuración de salida necesaria para Azure Kubernetes Service, consulte la sección de requisitos de conectividad de [Implementación en Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster).

> [!NOTE]
> El equilibrador de carga de Azure Kubernetes Service no es el mismo que el equilibrador de carga creado por Azure Machine Learning. Si desea hospedar el modelo como una aplicación segura, solo disponible en la red virtual, use el equilibrador de carga interno creado por Azure Machine Learning. Si desea permitir el acceso público, use el equilibrador de carga público creado por Azure Machine Learning.

Si el modelo requiere conectividad de entrada o salida adicional, como un origen de datos externo, use un grupo de seguridad de red o el firewall para permitir el tráfico.

## <a name="scenario-use-docker-images-managed-by-azure-ml"></a>Escenario: Uso de imágenes de Docker administradas por Azure ML

Azure Machine Learning proporciona imágenes de Docker que se pueden usar para entrenar modelos o realizar inferencias. Si no especifica sus propias imágenes, se usan las proporcionadas por Azure Machine Learning. Estas imágenes se hospedan en Microsoft Container Registry (MCR). También se hospedan en una instancia con replicación geográfica de Azure Container Registry denominada `viennaglobal.azurecr.io`.

Si ofrece sus propias imágenes de Docker, como en una instancia de Azure Container Registry que proporcione, no necesita la comunicación de salida con MCR o `viennaglobal.azurecr.io`.

> [!TIP]
> Si la instancia de Azure Container Registry está protegida en la red virtual, Azure Machine Learning no la puede utilizar para compilar imágenes de Docker. En su lugar, debe designar un clúster de proceso de Azure Machine Learning para compilar imágenes. Para obtener más información, consulte [Protección de un área de trabajo en una red virtual](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr).

:::image type="content" source="./media/concept-secure-network-traffic-flow/azure-machine-learning-docker-images.png" alt-text="Diagrama del flujo de tráfico al usar imágenes proporcionadas por Docker":::
## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido cómo fluye el tráfico de red en una configuración segura, obtenga más información sobre la protección de Azure ML en una red virtual. Para ello, lea el artículo [Información general sobre la privacidad y el aislamiento de la red virtual](how-to-network-security-overview.md).

Para obtener información sobre los procedimientos recomendados, consulte el artículo [Procedimientos recomendados de Azure Machine Learning para la seguridad empresarial](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security).