---
title: Configuración de un punto de conexión privado
titleSuffix: Azure Machine Learning
description: Use un punto de conexión privado para acceder de forma segura al área de trabajo de Azure Machine Learning desde una red virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/07/2021
ms.openlocfilehash: 9bb40b02df8de4bbd5fc7e59f55c7f31052b3480
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429896"
---
# <a name="configure-a-private-endpoint-for-an-azure-machine-learning-workspace"></a>Configuración de un punto de conexión privado para un área de trabajo de Azure Machine Learning

En este documento, aprenderá a configurar un punto de conexión privado para el área de trabajo de Azure Machine Learning. Para obtener más información sobre la creación de una red virtual para Azure Machine Learning, consulte [Información general sobre la privacidad y el aislamiento de la red virtual](how-to-network-security-overview.md).

Azure Private Link le permite conectarse a su área de trabajo mediante un punto de conexión privado. El punto de conexión privado es un conjunto de direcciones IP privadas dentro de la red virtual. Después, puede limitar el acceso al área de trabajo para que solo se produzca en las direcciones IP privadas. Un punto de conexión privado ayuda a reducir el riesgo de una filtración de datos. Para más información sobre los puntos de conexión privados, consulte el artículo [Azure Private Link](../private-link/private-link-overview.md).

> [!WARNING]
> La protección de un área de trabajo con puntos de conexión privados no garantiza la seguridad de un extremo a otro por sí misma. Debe proteger todos los componentes individuales de la solución. Por ejemplo, si usa un punto de conexión privado para el área de trabajo, pero la cuenta de Azure Storage no está detrás de la red virtual, el tráfico entre el área de trabajo y el almacenamiento no usa la red virtual por motivos de seguridad.
>
> Para obtener más información sobre cómo proteger los recursos usados por Azure Machine Learning, consulte los artículos siguientes:
>
> * [Información general sobre la privacidad y el aislamiento de la red virtual](how-to-network-security-overview.md).
> * [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md).
> * [Protección de entornos de entrenamiento](how-to-secure-training-vnet.md).
> * [Protección de entornos de inferencia](how-to-secure-inferencing-vnet.md).
> * [Uso de Estudio de Azure Machine Learning en una red virtual](how-to-enable-studio-virtual-network.md).

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

* Debe tener una red virtual existente en la cual crear el punto de conexión privado. 
* [Deshabilitar las directivas de red para los puntos de conexión privados](../private-link/disable-private-endpoint-network-policy.md) antes de agregar el punto de conexión privado.

## <a name="limitations"></a>Limitaciones

* Si habilita el acceso público para un área de trabajo protegida mediante un punto de conexión privado y usa Estudio de Azure Machine Learning a través de la red pública de Internet, es posible que algunas características como el diseñador no tengan acceso a sus datos. Este problema se produce cuando los datos se almacenan en un servicio protegido detrás de la red virtual. Por ejemplo, una cuenta de Azure Storage.
* Si usa Mozilla Firefox, puede encontrar problemas al intentar acceder al punto de conexión privado del área de trabajo. Este problema puede estar relacionado con DNS a través de HTTPS en Mozilla. Como solución alternativa, se recomienda usar Microsoft Edge o Google Chrome.
* El uso de un punto de conexión privado no afecta al plano de control de Azure (operaciones de administración), como la eliminación del área de trabajo o la administración de los recursos de proceso. Por ejemplo, crear, actualizar o eliminar un destino de proceso. Estas operaciones se realizan sobre la red pública de Internet como de costumbre. Las operaciones del plano de datos, como el uso de Azure Machine Learning Studio, las API (incluidas las canalizaciones publicadas) o el SDK usan el punto de conexión privado.
* Al crear una instancia de proceso o un clúster de proceso en un área de trabajo con un punto de conexión privado, la instancia de proceso y el clúster de proceso deben estar en la misma región de Azure que el área de trabajo.
* Al crear o adjuntar un clúster de Azure Kubernetes Service a un área de trabajo con un punto de conexión privado, el clúster debe estar en la misma región que el área de trabajo.
* Al usar un área de trabajo con varios puntos de conexión privados (versión preliminar), uno de los puntos de conexión privados debe estar en la misma red virtual que los siguientes servicios de dependencia:

    * Cuenta de Azure Storage que proporciona el almacenamiento predeterminado para el área de trabajo
    * Azure Key Vault para el área de trabajo
    * Azure Container Registry para el área de trabajo.

    Por ejemplo, una red virtual (red virtual "services") contendrá un punto de conexión privado para los servicios de dependencia y el área de trabajo. Esta configuración permite que el área de trabajo se comunique con los servicios. Otra red virtual ("clientes") solo puede contener un punto de conexión privado para el área de trabajo y usarse solo para la comunicación entre las máquinas de desarrollo de cliente y el área de trabajo.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Creación de un área de trabajo que usa un punto de conexión privado

Siga uno de los métodos a continuación para crear un área de trabajo con un punto de conexión privado. Cada uno de estos métodos __requiere una red virtual existente__:

> [!TIP]
> Si quiere crear un área de trabajo, un punto de conexión privado y una red virtual al mismo tiempo, consulte [Uso de una plantilla de Azure Resource Manager para crear un área de trabajo para Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

El SDK de Azure Machine Learning para Python proporciona la clase [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig), que se puede usar con [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) para crear un área de trabajo con un punto de conexión privado. Esta clase requiere una red virtual existente.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

La [extensión 1.0 de la CLI de Azure para Machine Learning](reference-azure-machine-learning-cli.md) proporciona el comando [az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create). Los siguientes parámetros para este comando se pueden usar para crear un área de trabajo con una red privada, pero se requiere una red virtual existente:

* `--pe-name`: nombre del punto de conexión privado creado.
* `--pe-auto-approval`: indica si las conexiones de punto de conexión privado al área de trabajo se deben aprobar automáticamente.
* `--pe-resource-group`: grupo de recursos en el que se va a crear el punto de conexión privado. Debe ser el mismo grupo que contiene la red virtual.
* `--pe-vnet-name`: red virtual existente en la que se va a crear el punto de conexión privado.
* `--pe-subnet-name`: nombre de la subred en la que se va a crear el punto de conexión privado. El valor predeterminado es `default`.

Estos parámetros se suman a otros parámetros necesarios para el comando create. Por ejemplo, el siguiente comando crea una nueva área de trabajo en la región oeste de EE. UU., con un grupo de recursos y una red virtual existentes:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

La pestaña __Redes__ de Azure Machine Learning Studio le permite configurar un punto de conexión privado. Sin embargo, se requiere una red virtual existente. Para obtener más información, consulte [Creación de áreas de trabajo en el portal](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Adición de un punto de conexión privado a un área de trabajo

Siga uno de los métodos a continuación para agregar un punto de conexión privado a un área de trabajo existente:

> [!WARNING]
>
> Si tiene destinos de proceso existentes asociados a esta área de trabajo y no están detrás de la misma red virtual en la que se crea el punto de conexión privado, no funcionarán.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Para obtener más información sobre las clases y los métodos usados en este ejemplo, consulte [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) y [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

La [extensión 1.0 de la CLI de Azure para Machine Learning](reference-azure-machine-learning-cli.md) proporciona el comando [az ml workspace private-endpoint add](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_add).

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Desde el área de trabajo de Azure Machine Learning en el portal, seleccione __Conexiones de punto de conexión privado__ y, a continuación, seleccione __+ Punto de conexión privado__. Use los campos para crear un punto de conexión privado.

* Al seleccionar la __Región__, seleccione la misma región que la red virtual. 
* Al seleccionar __Tipo de recurso__, use __Microsoft.MachineLearningServices/workspaces__. 
* Establezca __Recurso__ en el nombre del área de trabajo.

Por último, seleccione __Crear__ para crear el punto de conexión privado.

---

## <a name="remove-a-private-endpoint"></a>Eliminación de un punto de conexión privado

Siga uno de los métodos a continuación para quitar un punto de conexión privado de un área de trabajo:

# <a name="python"></a>[Python](#tab/python)

Use [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) para quitar un punto de conexión privado.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

La [extensión 1.0 de la CLI de Azure para Machine Learning](reference-azure-machine-learning-cli.md) proporciona el comando [az ml workspace private-endpoint delete](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Desde el área de trabajo de Azure Machine Learning en el portal, seleccione __Conexiones de punto de conexión privado__ y, a continuación, seleccione el punto de conexión que quiere quitar. Por último, seleccione __Quitar__.

---

## <a name="securely-connect-to-your-workspace"></a>Conexión segura al área de trabajo

[!INCLUDE [machine-learning-connect-secure-workspace](../../includes/machine-learning-connect-secure-workspace.md)]

## <a name="multiple-private-endpoints-preview"></a>Múltiples puntos de conexión privados (versión preliminar)

Como característica en versión preliminar, Azure Machine Learning admite varios puntos de conexión privados para un área de trabajo. A menudo se usan varios puntos de conexión privados cuando se desea mantener distintos entornos separados. Estos son algunos escenarios que se habilitan mediante el uso de varios puntos de conexión privados:

* Entornos de desarrollo de cliente en una red virtual independiente.
* Un clúster Azure Kubernetes Service (AKS) en una red virtual independiente.
* Otros servicios de Azure en una red virtual independiente. Por ejemplo, Azure Synapse y Azure Data Factory pueden usar una red virtual administrada por Microsoft. En cualquier caso, se puede agregar un punto de conexión privado para el área de trabajo a la red virtual administrada utilizada por esos servicios. Para más información sobre el uso de una red virtual administrada con estos servicios, consulte los artículos siguientes:

    * [Synapse managed private endpoints](/azure/synapse-analytics/security/synapse-workspace-managed-private-endpoints) (Puntos de conexión privados administrados de Synapse)
    * [Azure Data Factory managed virtual network](/azure/data-factory/managed-virtual-network-private-endpoint) (Red virtual administrada de Azure Data Factory)

    > [!IMPORTANT]
    > La [protección contra la filtración de datos de Synapse](/azure/synapse-analytics/security/workspace-data-exfiltration-protection) no se admite con Azure Machine Learning.

> [!IMPORTANT]
> Cada red virtual que contiene un punto de conexión privado para el área de trabajo también debe poder acceder a la cuenta de Azure Storage, Azure Key Vault y Azure Container Registry utilizada por el área de trabajo. Por ejemplo, puede crear un punto de conexión privado para los servicios de cada red virtual.

La adición de varios puntos de conexión usa los mismos pasos que se describen en la sección [Adición de un punto de conexión privado a un área de trabajo](#add-a-private-endpoint-to-a-workspace).

### <a name="scenario-isolated-clients"></a>Escenario: clientes aislados

Si desea aislar los clientes de desarrollo para que no tengan acceso directo a los recursos de proceso utilizados por Azure Machine Learning, siga estos pasos:

> [!NOTE]
> Estos pasos suponen que tiene un área de trabajo existente, una cuenta de Azure Storage, Azure Key Vault y Azure Container Registry. Cada uno de estos servicios tiene un punto de conexión privado en una red virtual existente.

1. Cree otra red virtual para los clientes. Esta red virtual puede contener máquinas virtuales de Azure que actúan como clientes o puede contener una VPN Gateway utilizada por los clientes locales para conectarse a la red virtual.
1. Agregue un nuevo punto de conexión privado para la cuenta de Azure Storage, Azure Key Vault y Azure Container Registry utilizada por el área de trabajo. Estos puntos de conexión privados deben existir en la red virtual del cliente.
1. Si tiene almacenamiento adicional utilizado por el área de trabajo, agregue un nuevo punto de conexión privado para ese almacenamiento. El punto de conexión privado debe existir en la red virtual del cliente y tener habilitada la integración de zona DNS privada.
1. Adición de un punto de conexión privado al área de trabajo. Este punto de conexión privado debe existir en la red virtual del cliente y tener habilitada la integración de zona DNS privada.
1. Siga los pasos descritos en el artículo [Uso de Studio en una red virtual](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account) para permitir que el estudio acceda a las cuentas de almacenamiento.

En el siguiente diagrama, se ilustra esta configuración. La red virtual de __carga de trabajo__ contiene los procesos creados por el área de trabajo para el entrenamiento y la implementación. La red virtual de __cliente__ contiene clientes o conexiones ExpressRoute/VPN de cliente. Ambas redes virtuales contienen puntos de conexión privados para el área de trabajo, la cuenta de Azure Storage, Azure Key Vault y Azure Container Registry.

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-client.png" alt-text="Diagrama de red virtual de cliente aislado":::

### <a name="scenario-isolated-azure-kubernetes-service"></a>Escenario: Azure Kubernetes Service aislado

Si desea crear una cuenta aislada de Azure Kubernetes Service utilizada por el área de trabajo, siga estos pasos:

> [!NOTE]
> Estos pasos suponen que tiene un área de trabajo existente, una cuenta de Azure Storage, Azure Key Vault y Azure Container Registry. Cada uno de estos servicios tiene un punto de conexión privado en una red virtual existente.

1. Cree una instancia de Azure Kubernetes Service. Durante la creación, AKS crea una red virtual que contiene el clúster de AKS.
1. Agregue un nuevo punto de conexión privado para la cuenta de Azure Storage, Azure Key Vault y Azure Container Registry utilizada por el área de trabajo. Estos puntos de conexión privados deben existir en la red virtual del cliente.
1. Si tiene otro almacenamiento utilizado por el área de trabajo, agregue un nuevo punto de conexión privado para ese almacenamiento. El punto de conexión privado debe existir en la red virtual del cliente y tener habilitada la integración de zona DNS privada.
1. Adición de un punto de conexión privado al área de trabajo. Este punto de conexión privado debe existir en la red virtual del cliente y tener habilitada la integración de zona DNS privada.
1. Conecte el clúster de AKS a un área de trabajo de Azure Machine Learning. Para obtener más información, consulte [Creación y conexión de un clúster de Azure Kubernetes Service](how-to-create-attach-kubernetes.md#attach-an-existing-aks-cluster).

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-aks.png" alt-text="Diagrama de la red virtual de AKS aislada":::

## <a name="enable-public-access"></a>Habilitación del acceso público

En algunas situaciones, puede que desee permitir que alguien se conecte al área de trabajo protegida mediante un punto de conexión público, en lugar de hacerlo mediante la red virtual. Después de configurar un área de trabajo con un punto de conexión privado, tiene la opción de habilitar el acceso público al área de trabajo. Al hacerlo, no se quita el punto de conexión privado. Todas las comunicaciones entre los componentes de la red virtual siguen estando protegidas. Se habilita el acceso público solo al área de trabajo, además del acceso privado mediante la red virtual.

> [!WARNING]
> Al conectarse a través del punto de conexión público:
> * __Algunas características de Studio no podrán acceder a los datos__. Este problema se produce cuando los _datos se almacenan en un servicio protegido detrás de la red virtual_. Por ejemplo, una cuenta de Azure Storage. 
> * __No se admite__ el uso de Jupyter, JupyterLab y RStudio en una instancia de proceso, incluidos los cuadernos en ejecución.

Para habilitar el acceso público a un área de trabajo con el punto de conexión privado habilitado, siga estos pasos:

# <a name="python"></a>[Python](#tab/python)

Use [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) para quitar un punto de conexión privado.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

La [extensión 1.0 de la CLI de Azure para Machine Learning](reference-azure-machine-learning-cli.md) proporciona el comando [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update). Para habilitar el acceso público al área de trabajo, agregue el parámetro `--allow-public-access true`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Actualmente no hay ninguna manera de habilitar esta funcionalidad mediante el portal.

---

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre cómo proteger el área de trabajo de Azure Machine Learning, consulte el artículo [Información general sobre la privacidad y el aislamiento de la red virtual](how-to-network-security-overview.md).

* Si tiene pensado usar una solución DNS personalizada en la red virtual, consulte [cómo usar un área de trabajo con un servidor DNS personalizado](how-to-custom-dns.md).
