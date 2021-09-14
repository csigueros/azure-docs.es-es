---
title: Protección de un área de trabajo de Azure Machine Learning con redes virtuales
titleSuffix: Azure Machine Learning
description: Use una instancia aislada de Azure Virtual Network para proteger el área de trabajo de Azure Machine Learning y los recursos asociados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 08/04/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, security
ms.openlocfilehash: 2ed3e7d1525c750c698e853921900e6d39feb83e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480361"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Protección de un área de trabajo de Azure Machine Learning con redes virtuales

En este artículo, aprenderá a proteger un área de trabajo de Azure Machine Learning y sus recursos asociados en una red virtual.

> [!TIP]
> Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:
>
> * [Información general sobre redes virtuales](how-to-network-security-overview.md)
> * [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
> * [Protección de un entorno de inferencia](how-to-secure-inferencing-vnet.md)
> * [Habilitación de Azure Machine Learning Studio en una red virtual](how-to-enable-studio-virtual-network.md)
> * [Uso de un DNS personalizado](how-to-custom-dns.md)
> * [Uso de un firewall](how-to-access-azureml-behind-firewall.md)
>
> Para ver un tutorial sobre cómo crear un área de trabajo segura, vea [Tutorial: Creación de un área de trabajo segura](tutorial-create-secure-workspace.md).

En este artículo aprenderá a habilitar los siguientes recursos de áreas de trabajo en una red virtual:
> [!div class="checklist"]
> - Área de trabajo de Azure Machine Learning
> - Cuentas de Azure Storage
> - Conjuntos de datos y almacenes de datos de Azure Machine Learning
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Requisitos previos

+ Lea el artículo [Introducción a la seguridad de red](how-to-network-security-overview.md) para comprender los escenarios comunes de redes virtuales y la arquitectura de red virtual general.

+ Una red virtual y una subred existentes que se usarán con los recursos de proceso.

    > [!TIP]
    > Si tiene previsto usar Azure Container Instances en la red virtual (para implementar modelos), el área de trabajo y la red virtual deben estar en el mismo grupo de recursos. De lo contrario, podrán estar en grupos diferentes.

+ Para implementar recursos en una red virtual o subred, la cuenta de usuario debe tener permisos para realizar las siguientes acciones en los controles de acceso basados en roles de Azure (Azure RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" en el recurso de red virtual.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" en el recurso de subred.

    Para obtener más información sobre Azure RBAC con redes, consulte los [roles integrados de redes](../role-based-access-control/built-in-roles.md#networking).

### <a name="azure-container-registry"></a>Azure Container Registry

* La instancia de Azure Container Registry debe tener la versión Prémium. Para más información sobre la actualización, vea [Cambio de SKU](../container-registry/container-registry-skus.md#changing-tiers).

* La instancia de Azure Container Registry debe estar en la misma red virtual y subred que la cuenta de almacenamiento y los destinos de proceso utilizados para entrenamiento o inferencia.

* El área de trabajo de Azure Machine Learning debe contener un [clúster de proceso de Azure Machine Learning](how-to-create-attach-compute-cluster.md).

## <a name="limitations"></a>Limitaciones

### <a name="azure-storage-account"></a>Cuenta de Azure Storage

Si el área de trabajo de Azure Machine Learning y la cuenta de Azure Storage usan un punto de conexión privado para conectarse a la red virtual, las dos deben estar dentro de la misma subred.

### <a name="azure-container-registry"></a>Azure Container Registry

Cuando la instancia de ACR está detrás de una red virtual, Azure Machine Learning no puede usarla para compilar imágenes de Docker directamente. En su lugar, se usa el clúster de proceso para compilar las imágenes.

> [!IMPORTANT]
> El clúster de proceso que se usa para compilar imágenes de Docker debe poder acceder a los repositorios de paquetes que se usan para entrenar e implementar los modelos. Es posible que tenga que agregar reglas de seguridad de red que permitan el acceso a repositorios públicos, [usar paquetes de Python privados](how-to-use-private-python-packages.md) o usar [imágenes de Docker personalizadas](how-to-train-with-custom-image.md) que ya incluyan los paquetes.

## <a name="required-public-internet-access"></a>Acceso obligatorio a una red de Internet pública

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

Para obtener información sobre el uso de una solución de firewall, vea [Uso de un firewall con Azure Machine Learning](how-to-access-azureml-behind-firewall.md).

## <a name="secure-the-workspace-with-private-endpoint"></a>Protección del área de trabajo con punto de conexión privado

Azure Private Link le permite conectarse a su área de trabajo mediante un punto de conexión privado. El punto de conexión privado es un conjunto de direcciones IP privadas dentro de la red virtual. Después, puede limitar el acceso al área de trabajo para que solo se produzca en las direcciones IP privadas. Un punto de conexión privado ayuda a reducir el riesgo de una filtración de datos.

Para obtener más información sobre cómo configurar un punto de conexión privado para el área de trabajo, vea [Procedimientos para configurar un punto de conexión privado](how-to-configure-private-link.md).

> [!WARNING]
> La protección de un área de trabajo con puntos de conexión privados no garantiza la seguridad de un extremo a otro por sí misma. Debe seguir los pasos que se describen en el resto de este artículo y la serie de redes virtuales para proteger componentes individuales de la solución. Por ejemplo, si usa un punto de conexión privado para el área de trabajo, pero la cuenta de Azure Storage no está detrás de la red virtual, el tráfico entre el área de trabajo y el almacenamiento no usa la red virtual por motivos de seguridad.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Protección de cuentas de almacenamiento de Azure con puntos de conexión de servicio

Azure Machine Learning admite cuentas de almacenamiento configuradas para usar puntos de conexión de servicio o puntos de conexión privados. En esta sección, aprenderá a proteger una cuenta de Azure Storage mediante puntos de conexión de servicio. En el caso de los puntos de conexión privados, consulte la sección siguiente.

Para usar una cuenta de Azure Storage para el área de trabajo en una red virtual, siga estos pasos:

1. En Azure Portal, vaya al servicio de almacenamiento que quiera usar en el área de trabajo.

   [![Almacenamiento asociado al área de trabajo de Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. En la página de la cuenta de servicio de almacenamiento, seleccione __Redes__.

   ![Área de redes de la página de Azure Storage en Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. En la pestaña __Firewalls y redes virtuales__, realice las acciones siguientes:
    1. Seleccione __Redes seleccionadas__.
    1. En __Redes virtuales__, seleccione el vínculo __Agregar red virtual existente__. Esta acción agrega la red virtual en la que reside el proceso (vea el paso 1).

        > [!IMPORTANT]
        > La cuenta de almacenamiento debe estar en la misma red virtual y subred que las instancias de proceso o clústeres usados para entrenamiento o inferencia.

    1. Seleccione la casilla __Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento__. Este cambio no concede a todos los servicios de Azure acceso a su cuenta de almacenamiento.
    
        * Los recursos de algunos servicios, cuando **están registrados en la suscripción**, pueden obtener acceso a la cuenta de almacenamiento **de la misma suscripción** para ciertas operaciones. Por ejemplo, operaciones de escritura de registros o creación de copias de seguridad.
        * Los recursos de algunos servicios pueden conceder acceso explícito a su cuenta de almacenamiento. Para ello, __asignan un rol de Azure__ a su identidad administrada asignada por el sistema.

        Para más información, vea [Configuración de Firewalls y redes virtuales de Azure Storage](../storage/common/storage-network-security.md#trusted-microsoft-services).

> [!TIP]
> Al usar un punto de conexión de servicio, también puede deshabilitar el acceso público. Para obtener más información, consulte la sección sobre cómo [deshabilitar el acceso de lectura público](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Protección de cuentas de almacenamiento de Azure con puntos de conexión privados

Azure Machine Learning admite cuentas de almacenamiento configuradas para usar puntos de conexión de servicio o puntos de conexión privados. Si la cuenta de almacenamiento usa puntos de conexión privados, debe configurar dos para la cuenta de almacenamiento predeterminada:
1. Un punto de conexión privado con un recurso secundario de destino de **blob**.
1. Un punto de conexión privado con un recurso secundario de destino de **archivo** (recurso compartido de archivos).

> [!TIP]
> Si tiene previsto usar [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) en la canalización, también es necesario configurar puntos de conexión privados con una **cola** y un subrecurso de destino de **tabla**. ParallelRunStep usa la cola y la tabla en segundo plano para la programación y distribución de tareas.

![Captura de pantalla que muestra la página de configuración de los puntos de conexión privados con opciones de blob y archivo](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Para configurar un punto de conexión privado para una cuenta de almacenamiento que **no** sea el almacenamiento predeterminado, seleccione el tipo **Subrecurso de destino** correspondiente a la cuenta de almacenamiento que quiere agregar.

Para más información, consulte [Uso de puntos de conexión privados para Azure Storage](../storage/common/storage-private-endpoints.md).

> [!TIP]
> Al usar un punto de conexión privado, también puede deshabilitar el acceso público. Para obtener más información, consulte la sección sobre cómo [deshabilitar el acceso de lectura público](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

## <a name="secure-azure-key-vault"></a>Protección de Azure Key Vault

Azure Machine Learning usa una instancia de Key Vault asociada para almacenar las siguientes credenciales:
* La cadena de conexión de cuenta de almacenamiento asociada
* Contraseñas para las instancias de Azure Container Repository
* Cadenas de conexión a almacenes de datos

Azure Key Vault se puede configurar para usar puntos de conexión de servicio o puntos de conexión privados. Para usar las funcionalidades de experimentación de Azure Machine Learning con Azure Key Vault detrás de una red virtual, siga los pasos siguientes:

1. Vaya a la instancia de Key Vault asociada al área de trabajo.

1. En la página de __Key Vault__, en el panel izquierdo, seleccione __Redes__.

1. En la pestaña __Firewalls y redes virtuales__, realice las acciones siguientes:
    1. En __Permitir el acceso desde__, seleccione __Punto de conexión privado y redes seleccionadas__.
    1. En __Redes virtuales__, seleccione __Agregar redes virtuales existentes__ para agregar la red virtual donde reside el proceso de experimentación.
    1. En __¿Quiere permitir que los servicios de confianza de Microsoft puedan omitir este firewall?__ , seleccione __Sí__.

   [![Sección "Firewalls y redes virtuales" del panel de Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Habilitación de un registro de Azure Container Registry (ACR)

> [!TIP]
> Si no usó una instancia de Azure Container Registry existente al crear el área de trabajo, es posible que no exista ninguna. De forma predeterminada, el área de trabajo no creará ninguna instancia de ACR hasta que necesite una. Para forzar la creación de una, entrene o implemente un modelo mediante el área de trabajo antes de seguir los pasos de esta sección.

Azure Container Registry se puede configurar para usar puntos de conexión de servicio o puntos de conexión privados. Siga estos pasos para configurar el área de trabajo a fin de que use ACR cuando se encuentre en la red virtual:

1. Busque el nombre de la instancia de Azure Container Registry para el área de trabajo mediante alguno de los métodos siguientes:

    __Azure Portal__

    En la sección de información general del área de trabajo, el valor __Registro__ se vincula a la instancia de Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry para el área de trabajo" border="true":::

    __CLI de Azure__

    Si ha [instalado la extensión de Machine Learning para la CLI de Azure](reference-azure-machine-learning-cli.md), puede usar el comando `az ml workspace show` para mostrar la información del área de trabajo.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Este comando devuelve un valor similar a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. La última parte de la cadena es el nombre de Azure Container Registry para el área de trabajo.

1. Limite el acceso a la red virtual mediante los pasos descritos en [Configuración del acceso de red al registro](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Al agregar la red virtual, seleccione la red virtual y la subred para los recursos de Azure Machine Learning.

1. Configure la instancia de ACR del área de trabajo para [permitir el acceso de los servicios de confianza](../container-registry/allow-access-trusted-services.md).

1. Creará un clúster de proceso de Azure Machine Learning. Esto se usa para compilar imágenes de Docker cuando ACR está detrás de una red virtual. Para obtener más información, vea [Creación de un clúster de proceso](how-to-create-attach-compute-cluster.md).

1. Use el SDK de Python de Azure Machine Learning para configurar el área de trabajo a fin de compilar imágenes de Docker mediante la instancia de proceso. En el fragmento de código siguiente se muestra cómo actualizar el área de trabajo para establecer un proceso de compilación. Reemplace `mycomputecluster` por el nombre del clúster que se va a usar:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > La cuenta de almacenamiento, el clúster de proceso y Azure Container Registry deben estar en la misma subred de la red virtual.
    
    Para más información, vea la referencia del método [update()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-).

> [!TIP]
> Si ACR está detrás de una red virtual, también puede [deshabilitar el acceso público](../container-registry/container-registry-access-selected-networks.md#disable-public-network-access) a la misma.

## <a name="datastores-and-datasets"></a>Almacenes de datos y conjuntos de datos
En la tabla siguiente se muestra una lista de los servicios para los que debe omitir la validación:

| Servicio | ¿Omitir la validación necesaria? |
| ----- |:-----:|
| Azure Blob Storage | Sí |
| Recurso compartido de archivos de Azure | Sí |
| Azure Data Lake Store Gen1 | No |
| Azure Data Lake Store Gen2 | No |
| Azure SQL Database | Sí |
| PostgreSql | Sí |

> [!NOTE]
> En Azure Data Lake Store Gen1 y Azure Data Lake Store Gen2 se omite la validación de manera predeterminada, por lo que no tiene que hacer nada.

En el ejemplo de código siguiente se crea un almacén de datos de blobs de Azure y se establece `skip_validation=True`.

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Uso de conjuntos de datos

La sintaxis para omitir la validación de conjuntos de datos es similar para los siguientes tipos de datos:
- Archivo delimitado
- JSON 
- Parquet
- SQL
- Archivo

En el código siguiente se crea un conjunto de datos JSON y se establece `validate=False`.

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 
```

## <a name="securely-connect-to-your-workspace"></a>Conexión segura al área de trabajo

Se pueden usar los métodos siguientes para conectarse al área de trabajo segura:

* [Azure VPN Gateway:](../vpn-gateway/vpn-gateway-about-vpngateways.md) conecta redes locales a la VNet mediante una conexión privada. La conexión se realiza mediante la red pública de Internet. Hay dos tipos de puertas de enlace de VPN que puede usar:

    * [De punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md): cada equipo cliente usa un cliente VPN para conectarse a la red virtual.
    * [De sitio a sitio](../vpn-gateway/tutorial-site-to-site-portal.md): un dispositivo VPN conecta la red virtual a la red local.

* [ExpressRoute:](https://azure.microsoft.com/services/expressroute/) conecta redes locales a la nube mediante una conexión privada. La conexión se realiza mediante un proveedor de conectividad.
* [Azure Bastion:](../bastion/bastion-overview.md) en este escenario, se crea una máquina virtual de Azure (a veces denominada "jump box") dentro de la VNet. A continuación, se conecta a la máquina virtual mediante Azure Bastion. Bastion permite conectarse a la máquina virtual mediante una sesión RDP o SSH desde el explorador web local. A continuación, use la jump box como entorno de desarrollo. Puesto que está dentro de la VNet, puede acceder directamente al área de trabajo. Para obtener un ejemplo de cómo usar un jumpbox, vea [Tutorial: Creación de un área de trabajo segura](tutorial-create-secure-workspace.md).

> [!IMPORTANT]
> Al usar una __puerta de enlace de VPN__ o __ExpressRoute__, deberá planear cómo funciona la resolución de nombres entre los recursos locales y los de la red virtual. Para obtener más información, vea [Creación de un servidor DNS personalizado](how-to-custom-dns.md).

## <a name="workspace-diagnostics"></a>Diagnóstico del área de trabajo

[!INCLUDE [machine-learning-workspace-diagnostics](../../includes/machine-learning-workspace-diagnostics.md)]

## <a name="next-steps"></a>Pasos siguientes

Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:

* [Información general sobre redes virtuales](how-to-network-security-overview.md)
* [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
* [Protección de un entorno de inferencia](how-to-secure-inferencing-vnet.md)
* [Habilitación de Azure Machine Learning Studio en una red virtual](how-to-enable-studio-virtual-network.md)
* [Uso de un DNS personalizado](how-to-custom-dns.md)
* [Uso de un firewall](how-to-access-azureml-behind-firewall.md)
