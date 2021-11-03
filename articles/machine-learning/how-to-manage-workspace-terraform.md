---
title: Administración de áreas de trabajo mediante Terraform
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo administrar áreas de trabajo de Azure Machine Learning mediante Terraform.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: deeikele
author: denniseik
ms.date: 10/08/2021
ms.topic: how-to
ms.custom: ''
ms.openlocfilehash: b567accf1a83506731fa58b793fca428c9c702e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085020"
---
# <a name="manage-azure-machine-learning-workspaces-using-terraform-preview"></a>Administración de áreas de trabajo de Azure Machine Learning mediante Terraform (versión preliminar)

En este artículo aprenderá a crear y administrar un área de trabajo de Azure Machine Learning mediante archivos de configuración de Terraform. Los archivos de configuración basados en plantilla de [Terraform](/azure/developer/terraform/) permiten definir, crear y configurar recursos de Azure de forma repetible y predecible. Terraform realiza un seguimiento del estado de los recursos y es capaz de limpiar y destruir estos. 

Una configuración de Terraform es un documento que define los recursos que son necesarios para una implementación. También puede especificar las variables de implementación. Las variables se usan para proporcionar valores de entrada al usar la configuración.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prerrequisitos

* Una **suscripción de Azure**. Si no tiene una ya, pruebe la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).
* Una versión instalada de la [CLI de Azure](/cli/azure/).
* Configuración de Terraform: siga las instrucciones de este artículo y del artículo sobre [Terraform y la configuración del acceso a Azure](/azure/developer/terraform/get-started-cloud-shell).

## <a name="declare-the-azure-provider"></a>Declaración del proveedor de Azure

Cree el archivo de configuración de Terraform que declara el proveedor de Azure:

1. Cree un nuevo archivo llamado `main.tf`. Si trabaja con Azure Cloud Shell, use bash:

    ```bash
    code main.tf
    ```

1. Pegue el siguiente código en el editor:

    **main.tf**:
    :::code language="terraform" source="~/terraform/quickstart/101-machine-learning/main.tf":::

1. Guarde el archivo ( **&lt;Ctrl > S**) y salga del editor ( **&lt;Ctrl > Q**).

## <a name="deploy-a-workspace"></a>Implementación de un área de trabajo

Las siguientes configuraciones de Terraform se pueden usar para crear un área de trabajo de Azure Machine Learning. Al crear un área de trabajo de Azure Machine Learning, se requieren otros servicios como dependencias. La plantilla también especifica estos [recursos asociados al área de trabajo](./concept-workspace.md#resources). En función de sus necesidades, puede optar por usar la plantilla que crea recursos con conectividad de red pública o privada.

# <a name="public-network-connectivity"></a>[Conectividad de red pública](#tab/publicworkspace)

Algunos recursos de Azure requieren nombres únicos globales. Antes de implementar los recursos mediante las siguientes plantillas, establezca la variable `name` en un valor que sea único.

**variables.tf**:
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/variables.tf":::

**workspace.tf**:
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/workspace.tf":::

# <a name="private-network-connectivity"></a>[Conectividad de red privada](#tab/privateworkspace)

La configuración siguiente crea un área de trabajo en un entorno de red aislado mediante puntos de conexión de Azure Private Link. [Zonas DNS privadas](../dns/private-dns-privatednszone.md) se incluyen para que los nombres de dominio se puedan resolver dentro de la red virtual.

Algunos recursos de Azure requieren nombres únicos globales. Antes de implementar los recursos mediante las siguientes plantillas, establezca la variable `resourceprefix` en un valor que sea único.

Al usar puntos de conexión de vínculo privado para Container Registry y Azure Machine Learning, las tareas de Azure Container Registry no se pueden usar para compilar imágenes del [entorno](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true). En su lugar, puede compilar imágenes mediante un clúster de proceso de Azure Machine Learning. Para configurar el nombre de clúster de uso, establezca el argumento [image_build_compute_name](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace). Puede configurar para [permitir el acceso público](./how-to-configure-private-link.md?tabs=python#enable-public-access) a un área de trabajo con un punto de conexión de vínculo privado mediante el argumento [public_network_access_enabled](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace).

**variables.tf**:
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/variables.tf":::

**workspace.tf**:
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/workspace.tf":::

**network.tf**:
```terraform
# Virtual Network
resource "azurerm_virtual_network" "default" {
  name                = "vnet-${var.name}-${var.environment}"
  address_space       = var.vnet_address_space
  location            = azurerm_resource_group.default.location
  resource_group_name = azurerm_resource_group.default.name
}

resource "azurerm_subnet" "snet-training" {
  name                                           = "snet-training"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.training_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-aks" {
  name                                           = "snet-aks"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.aks_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-workspace" {
  name                                           = "snet-workspace"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.ml_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

# ...
# For full reference, see: https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/network.tf
```

Hay varias opciones para conectarse al área de trabajo del punto de conexión de vínculo privado. Para obtener más información sobre estas opciones, consulte [Conectarse de forma segura al área de trabajo](./how-to-secure-workspace-vnet.md#securely-connect-to-your-workspace).

---

## <a name="troubleshooting"></a>Solución de problemas

### <a name="resource-provider-errors"></a>Errores del proveedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="current-limitations"></a>Limitaciones actuales

* Actualmente no es posible usar Terraform para la implementación de áreas de trabajo mediante claves de cifrado administradas por el cliente.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre la compatibilidad de Terraform en Azure, consulte [Terraform en la documentación de Azure](/azure/developer/terraform/).
* Para buscar ejemplos de plantilla "de inicio rápido" para Terraform, consulte las [plantillas de inicio rápido de Azure Terraform](https://github.com/Azure/terraform/tree/master/quickstart):
  
  * [101: Machine learning workspace and compute](https://github.com/Azure/terraform/tree/master/quickstart/101-machine-learning) (Área de trabajo y proceso de Machine Learning): el conjunto mínimo de recursos necesarios para empezar a trabajar con Azure ML.
  * [201: Machine learning workspace, compute, and a set of network components for network isolation](https://github.com/Azure/terraform/tree/master/quickstart/201-machine-learning-moderately-secure) (Área de trabajo de Machine Learning, proceso y un conjunto de componentes de red para el aislamiento de red): todos los recursos que son necesarios para crear un entorno piloto de producción que se va a usar con los datos de alto impacto de negocio (HBI).
  * [202: similar a 201, pero con la opción de traer los componentes de red existentes.](https://github.com/Azure/terraform/tree/master/quickstart/202-machine-learning-moderately-secure-existing-VNet).
  
* Para obtener detalles sobre el proveedor de Azure para Terraform, consulte la información del [proveedor de Azure Resource Manager del registro de Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs).
* Para obtener más información sobre las opciones de configuración de red, consulte [Protección de los recursos del área de trabajo de Azure Machine Learning con redes virtuales (VNet)](./how-to-network-security-overview.md).
* Para implementaciones basadas en plantillas de Azure Resource Manager, consulte [Implementación de recursos con plantillas de Resource Manager y la API REST de Resource Manager](../azure-resource-manager/templates/deploy-rest.md).