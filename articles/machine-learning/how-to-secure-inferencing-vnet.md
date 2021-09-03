---
title: Protección de entornos de inferencia con redes virtuales
titleSuffix: Azure Machine Learning
description: Use una instancia aislada de Azure Virtual Network para proteger el entorno de inferencia de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 07/13/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 27c2b5d5af181aea982a6aed735997f5ac866b6d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739140"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Protección de un entorno de inferencia de Azure Machine Learning con redes virtuales

En este artículo, aprenderá a proteger los entornos de inferencia con una red virtual en Azure Machine Learning.

> [!TIP]
> Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:
>
> * [Información general sobre redes virtuales](how-to-network-security-overview.md)
> * [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
> * [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
> * [Habilitación de Estudio de Azure Machine Learning en una red virtual](how-to-enable-studio-virtual-network.md)
> * [Uso de un DNS personalizado](how-to-custom-dns.md)
> * [Uso de un firewall](how-to-access-azureml-behind-firewall.md)

En este artículo aprenderá a proteger los siguientes recursos de inferencia en una red virtual:
> [!div class="checklist"]
> - Clúster de Azure Kubernetes Service (AKS) predeterminado
> - Clúster privado de AKS
> - Clúster de AKS con Private Link
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>Requisitos previos

+ Lea el artículo [Introducción a la seguridad de red](how-to-network-security-overview.md) para comprender los escenarios comunes de redes virtuales y la arquitectura de red virtual general.

+ Una red virtual y una subred existentes que se usarán con los recursos de proceso.

+ Para implementar recursos en una red virtual o subred, la cuenta de usuario debe tener permisos para realizar las siguientes acciones en los controles de acceso basados en roles de Azure (Azure RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" en el recurso de red virtual.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" en el recurso de subred.

    Para obtener más información sobre Azure RBAC con redes, consulte los [roles integrados de redes](../role-based-access-control/built-in-roles.md#networking).

## <a name="limitations"></a>Limitaciones

### <a name="azure-container-instances"></a>Azure Container Instances

* Al usar Azure Container Instances en una red virtual, la red virtual debe estar en el mismo grupo de recursos que el área de trabajo de Azure Machine Learning. De lo contrario, la red virtual puede estar en un grupo de recursos diferente.
* Si el área de trabajo tiene un __punto de conexión privado__, la red virtual que se utilice con Azure Container Instances debe ser la misma que la que se use en el punto de conexión privado del área de trabajo.
* Al usar Azure Container Instances en la red virtual, la instancia de Azure Container Registry (ACR) para el área de trabajo no puede estar en la red virtual.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

> [!IMPORTANT]
> Para usar un clúster de AKS en una red virtual, primero siga los requisitos previos de [Configuración de redes avanzadas en Azure Kubernetes Service (AKS)](../aks/configure-azure-cni.md#prerequisites).


Para agregar AKS en una red virtual a su área de trabajo, siga los pasos siguientes:

1. Inicie sesión en [Azure Machine Learning Studio](https://ml.azure.com/) y, después, seleccione la suscripción y el área de trabajo.
1. Seleccione __Proceso__ a la izquierda, __Inference clusters__ (Clústeres de inferencia) en el centro y, luego, seleccione __+ Nuevo__.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-inference.png" alt-text="Captura de pantalla del cuadro de diálogo de creación de clústeres de inferencia":::

1. En el cuadro de diálogo __Create inference cluster__ (Crear clúster de inferencia), seleccione __Crear nuevo__ y el tamaño de máquina virtual que se usará para el clúster. Por último, seleccione __Siguiente__.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-inference-vm.png" alt-text="Captura de pantalla de la configuración de la máquina virtual":::

1. En la sección __Parámetros de configuración__, escriba un __nombre de proceso__, seleccione el __objetivo del clúster__, el __número de nodos__ y, luego, seleccione __Avanzadas__ para mostrar la configuración de red. En el área __Configurar red virtual__, establezca los valores siguientes:

    * Establezca la __red virtual__ que se usará.

        > [!TIP]
        > Si el área de trabajo usa un punto de conexión privado para conectarse a la red virtual, el campo de selección __Red virtual__ está atenuado.

    * Establezca la __subred__ en la que se crea el clúster.
    * En el campo __Intervalo de direcciones del servicio de Kubernetes__, escriba el intervalo de direcciones del servicio Kubernetes. Este intervalo de direcciones utiliza un intervalo de IP de notación CIDR (enrutamiento entre dominios sin clases) para definir las direcciones IP que están disponibles para el clúster. No debe superponerse con ningún intervalo IP de subred (por ejemplo, 10.0.0.0/16).
    * En el campo __Dirección IP del servicio DNS de Kubernetes__, escriba la dirección IP del servicio DNS de Kubernetes. Esta dirección IP se asigna al servicio DNS de Kubernetes. Debe estar dentro del intervalo de direcciones del servicio Kubernetes (por ejemplo, 10.0.0.10).
    * En el campo __Dirección de puente de Docker__, escriba la dirección del puente de Docker. Esta dirección IP se asigna al puente de Docker. No debe estar en ningún intervalo IP de subred o en el intervalo de direcciones del servicio Kubernetes (por ejemplo, 172.18.0.1/16).

    :::image type="content" source="./media/how-to-enable-virtual-network/create-inference-settings.png" alt-text="Captura de pantalla de la configuración de red":::

1. Cuando se implementa un modelo como un servicio web en AKS, se crea un punto de conexión de puntuación para controlar las solicitudes de inferencia. Asegúrese de que el grupo de seguridad de red (NSG) que controla la red virtual tenga una regla de seguridad de entrada habilitada para la dirección IP del punto de conexión de puntuación si quiere llamarlo desde fuera de la red virtual.

    Para buscar la dirección IP del punto de conexión de puntuación, examine el URI de puntuación para el servicio implementado. Para obtener información sobre cómo ver el URI de puntuación, vea [Consumo de un modelo implementado como servicio web](how-to-consume-web-service.md#connection-information).

   > [!IMPORTANT]
   > Mantenga las reglas de salida predeterminadas para el grupo de seguridad de red. Para más información, consulte las reglas de seguridad predeterminadas en [Grupos de seguridad](../virtual-network/network-security-groups-overview.md#default-security-rules).

   [![Regla de seguridad de entrada](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > La dirección IP que se muestra en la imagen para el punto de conexión de puntuación será diferente para sus implementaciones. Aunque todas las implementaciones comparten la misma dirección IP en un clúster de AKS, cada clúster de AKS tendrá una dirección IP diferente.

También puede usar el SDK de Azure Machine Learning para agregar Azure Kubernetes Service en una red virtual. Si ya tiene un clúster de AKS en una red virtual, asócielo al área de trabajo como se describe en el tema sobre la [implementación en AKS](how-to-deploy-and-where.md). El código siguiente crea una instancia de Azure Kubernetes Service en la subred `default` de una red virtual denominada `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Cuando finalice el proceso de creación, puede ejecutar una inferencia, o puntuación de modelos, en un clúster de AKS detrás de una red virtual. Para más información, consulte [Implementación en AKS](how-to-deploy-and-where.md).

Para más información sobre el uso del control de acceso basado en rol con Kubernetes, consulte [Uso de Azure RBAC para la autorización de Kubernetes (versión preliminar)](../aks/manage-azure-rbac.md).

## <a name="network-contributor-role"></a>Rol de colaborador de red

> [!IMPORTANT]
> Si crea o asocia un clúster de AKS al proporcionar una red virtual creada anteriormente, debe conceder a la entidad de servicio (SP) o la identidad administrada del clúster de AKS el rol _Colaborador de la red_ del grupo de recursos que contiene la red virtual.
>
> Para agregar la identidad como colaborador de red, siga estos pasos:

1. Para buscar la entidad de servicio o el id. de identidad administrada para AKS, use los siguientes comandos de la CLI de Azure. Reemplace `<aks-cluster-name>` por el nombre del clúster. Reemplace `<resource-group-name>` por el nombre del grupo de recursos que _contiene el clúster de AKS_:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Si este comando devuelve el valor `msi`, use el siguiente comando para identificar el identificador de entidad de seguridad de la identidad administrada:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Para buscar el identificador del grupo de recursos que contiene la red virtual, use el siguiente comando. Reemplace `<resource-group-name>` por el nombre del grupo de recursos que _contiene la red virtual_.

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Para agregar la entidad de servicio o la identidad administrada como colaborador de la red, use el siguiente comando. Reemplace `<SP-or-managed-identity>` por el identificador devuelto para la entidad de servicio o la identidad administrada. Reemplace `<resource-group-id>` por el identificador revuelto para el grupo de recursos que contiene la red virtual:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Para más información sobre el uso del equilibrador de carga interno con AKS, consulte [Uso del equilibrador de carga interno con Azure Kubernetes Service](../aks/internal-lb.md).

## <a name="secure-vnet-traffic"></a>Tráfico de red virtual seguro

Hay dos métodos para aislar el tráfico hacia y desde el clúster de AKS a la red virtual:

* __Clúster privado de AKS__: este enfoque usa Azure Private Link para proteger las comunicaciones con el clúster para las operaciones de implementación y administración.
* __Equilibrador de carga interno de AKS__: Este enfoque configura el punto de conexión de las implementaciones en AKS para usar una dirección IP privada en la red virtual.

### <a name="private-aks-cluster"></a>Clúster privado de AKS

De manera predeterminada, los clústeres de AKS tienen un plano de control o un servidor de API con las direcciones IP públicas. Puede configurar AKS para usar un plano de control privado mediante la creación de un clúster privado de AKS. Para obtener más información, consulte [Creación de un clúster privado de Azure Kubernetes Service](../aks/private-clusters.md).

Después de crear el clúster privado de AKS, [adjunte el clúster a la red virtual](how-to-create-attach-kubernetes.md) para usarlo con Azure Machine Learning.

### <a name="internal-aks-load-balancer"></a>Equilibrador de carga interno de AKS

De manera predeterminada, las implementaciones de AKS usan un [equilibrador de carga público](../aks/load-balancer-standard.md). En esta sección, aprenderá a configurar AKS para usar un equilibrador de carga interno. Un equilibrador de carga interno (o privado) se usa cuando solo se admiten direcciones IP privadas como front-end. Los equilibradores de carga internos se usan para equilibrar la carga del tráfico dentro de una red virtual.

Un equilibrador de carga privado se habilita mediante la configuración de AKS para usar un _equilibrador de carga interno_. 

#### <a name="enable-private-load-balancer"></a>Habilitación del equilibrador de carga privado

> [!IMPORTANT]
> No se puede habilitar la IP privada al crear el clúster de Azure Kubernetes Service en Estudio de Azure Machine Learning. Puede crear uno con un equilibrador de carga interno al usar el SDK de Python o la extensión CLI de Azure para aprendizaje automático.

En los siguientes ejemplos se muestra cómo __crear un clúster de AKS mediante la IP privada o el equilibrador de carga interno__ mediante el SDK y la CLI:

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

Para actualizar un clúster de AKS existente para usar un equilibrador de carga interno, utilice el siguiente comando:

```azurecli
az ml computetarget update aks \
                           -n myaks \
                           --load-balancer-subnet mysubnet \
                           --load-balancer-type InternalLoadBalancer \
                           --workspace-name myworkspace \
                           -g myresourcegroup
```


Para más información, consulte la referencia [az ml computetarget create aks](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_aks) y [az ml computetarget update aks](/cli/azure/ml(v1)/computetarget/update#az_ml_computetarget_update_aks).

---

Cuando __asocie un clúster existente__ al área de trabajo, debe esperar a que finalice la operación de adjuntar para configurar el equilibrador de carga. Para obtener información sobre cómo asociar un clúster, consulte [Asociación de un clúster de AKS ya existente](how-to-create-attach-kubernetes.md).

Después de asociar el clúster existente, puede actualizarlo para usar una dirección IP privada o un equilibrador de carga:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Habilitación de Azure Container Instances (ACI)

Azure Container Instances se crean dinámicamente al implementar un modelo. Para habilitar Azure Machine Learning para crear ACI dentro de la red virtual, debe habilitar la __delegación de subred__ para la subred que usa la implementación. A fin de usar ACI en una red virtual para su área de trabajo, siga los pasos siguientes:

1. Para habilitar la delegación de subred en la red virtual, use la información del artículo [Adición o eliminación de una delegación de subred](../virtual-network/manage-subnet-delegation.md). Puede habilitar la delegación al crear una red virtual o agregarla a una red existente.

    > [!IMPORTANT]
    > Al habilitar la delegación, use `Microsoft.ContainerInstance/containerGroups` como el valor __Delegar subred en un servicio__.

2. Implemente el modelo mediante [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), use los parámetros `vnet_name` y `subnet_name`. Establezca estos parámetros en el nombre de red virtual y subred donde habilitó la delegación.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a> Limitación de la conectividad saliente de la red virtual

Si no quiere usar las reglas de salida predeterminadas y quiere limitar el acceso de salida de la red virtual, debe permitir el acceso a Azure Container Registry. Por ejemplo, asegúrese de que los grupos de seguridad de red (NSG) contienen una regla que permita el acceso a la etiqueta de servicio __AzureContainerRegistry.RegionName__, donde {RegionName} es el nombre de una región de Azure.

## <a name="next-steps"></a>Pasos siguientes

Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:

* [Información general sobre redes virtuales](how-to-network-security-overview.md)
* [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
* [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
* [Habilitación de Estudio de Azure Machine Learning en una red virtual](how-to-enable-studio-virtual-network.md)
* [Uso de un DNS personalizado](how-to-custom-dns.md)
* [Uso de un firewall](how-to-access-azureml-behind-firewall.md)
