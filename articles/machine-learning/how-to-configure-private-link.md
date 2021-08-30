---
title: Configuración de un punto de conexión privado
titleSuffix: Azure Machine Learning
description: Use un punto de conexión privado para acceder de forma segura al área de trabajo de Azure Machine Learning desde una red virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2021
ms.openlocfilehash: 1c07e96a82814e59c635a592313e461d06a6fcc3
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217474"
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

* Debe tener una red virtual existente en la cual crear el punto de conexión privado. También tiene que [deshabilitar las directivas de red para los puntos de conexión privados](../private-link/disable-private-endpoint-network-policy.md) antes de agregar el punto de conexión privado.

## <a name="limitations"></a>Limitaciones

* El uso de un área de trabajo de Azure Machine Learning con un punto de conexión privado no está disponible en las regiones de Azure Government.
* Si habilita el acceso público para un área de trabajo protegida mediante un punto de conexión privado y usa Estudio de Azure Machine Learning a través de la red pública de Internet, es posible que algunas características como el diseñador no tengan acceso a sus datos. Este problema se produce cuando los datos se almacenan en un servicio protegido detrás de la red virtual. Por ejemplo, una cuenta de Azure Storage.
* Si usa Mozilla Firefox, puede encontrar problemas al intentar acceder al punto de conexión privado del área de trabajo. Este problema puede estar relacionado con DNS a través de HTTPS en Mozilla. Como solución alternativa, se recomienda usar Microsoft Edge o Google Chrome.
* El uso de un punto de conexión privado no afecta al plano de control de Azure (operaciones de administración), como la eliminación del área de trabajo o la administración de los recursos de proceso. Por ejemplo, crear, actualizar o eliminar un destino de proceso. Estas operaciones se realizan sobre la red pública de Internet como de costumbre. Las operaciones del plano de datos, como el uso de Azure Machine Learning Studio, las API (incluidas las canalizaciones publicadas) o el SDK usan el punto de conexión privado.

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

## <a name="using-a-workspace-over-a-private-endpoint"></a>Uso de un área de trabajo mediante un punto de conexión privado

Dado que la comunicación con el área de trabajo solo se permite desde la red virtual, cualquier entorno de desarrollo que use el área de trabajo debe ser miembro de la red virtual. Por ejemplo, una máquina virtual en la red virtual.

> [!IMPORTANT]
> Para evitar la interrupción temporal de la conectividad, Microsoft recomienda vaciar la memoria caché de DNS en las máquinas que se conectan al área de trabajo después de habilitar un punto de conexión privado. 

Para más información sobre Azure Virtual Machines, consulte [Documentación sobre Virtual Machines](../virtual-machines/index.yml).

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
