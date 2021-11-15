---
title: Ejecución de Linux en nodos de proceso de máquinas virtuales
description: Aprenda a procesar cargas de trabajo de proceso paralelas en grupos de máquinas virtuales Linux en el servicio Azure Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: f2140fd4f2bfc383743b84f0b635afba3dda9afe
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458672"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Aprovisionamiento de nodos de proceso de Linux en grupos de Batch

Puede usar el servicio Azure Batch para ejecutar cargas de trabajo de proceso paralelas en máquinas virtuales Linux y Windows. Este artículo detalla cómo crear grupos de nodos de proceso de Linux en el servicio Batch mediante las bibliotecas de cliente de [Python de Batch](https://pypi.python.org/pypi/azure-batch) y [.NET de Batch](/dotnet/api/microsoft.azure.batch). 

## <a name="virtual-machine-configuration"></a>Configuración de máquina virtual

Cuando crea un grupo de nodos de proceso en el servicio Batch, tiene dos opciones para seleccionar el sistema operativo y el tamaño de nodo: configuración de Cloud Services y configuración de Virtual Machine. Los grupos de [configuración de máquina virtual](nodes-and-pools.md#virtual-machine-configuration) se componen de VM de Azure, que se pueden crear a partir de imágenes de Linux o Windows. Al crear un grupo con la configuración de máquina virtual, especifica un [tamaño de nodo de proceso disponible](../virtual-machines/sizes.md), la referencia de la imagen de la máquina virtual que se va a instalar en los nodos, y la SKU del agente de nodo de Batch (un programa que se ejecuta en cada nodo y que proporciona una interfaz entre el nodo y el servicio Batch).

### <a name="virtual-machine-image-reference"></a>Referencia de imagen de máquina virtual

El servicio Batch usa [conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/overview.md) para proporcionar nodos de ejecución en la configuración de la máquina virtual. Puede especificar una imagen desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) o [usar Azure Compute Gallery para preparar una imagen personalizada](batch-sig-images.md).

Al crear una referencia de la imagen de la máquina virtual, debe especificar las siguientes propiedades:

| **Propiedad de la referencia de la imagen** | **Ejemplo** |
| --- | --- |
| Publicador |Canonical |
| Oferta |UbuntuServer |
| SKU |18.04-LTS |
| Versión |latest |

> [!TIP]
> Puede encontrar más información sobre estas propiedades y cómo especificar imágenes de Marketplace en [Búsqueda de imágenes de máquina virtual Linux en Azure Marketplace con la CLI de Azure](../virtual-machines/linux/cli-ps-findimage.md). Tenga en cuenta que algunas imágenes del marketplace actualmente no son compatibles con Batch.

### <a name="list-of-virtual-machine-images"></a>Lista de imágenes de máquinas virtuales

No todas las imágenes de Marketplace son compatibles con los agentes de nodo de Batch disponibles actualmente. Para mostrar todas las imágenes de máquina virtual de Marketplace admitidas en el servicio Batch y sus SKU de agente de nodo correspondientes, use [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (.NET de Batch) o la API correspondiente del SDK de otro lenguaje.

### <a name="node-agent-sku"></a>SKU del agente de nodo

El [agente de nodo del servicio Batch](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) es un programa que se ejecuta en cada nodo del grupo y proporciona la interfaz de comandos y control entre el nodo y el servicio Batch. Hay diferentes implementaciones del agente de nodo, conocidas como SKU, para distintos sistemas operativos. Básicamente, al crear una configuración de la máquina virtual, debe especificar primero la referencia de la imagen de máquina virtual y, después, el agente de nodo que se va a instalar en la imagen. Normalmente, cada SKU del agente de nodo es compatible con varias imágenes de máquina virtual. Estos son algunos ejemplos de SKU del agente de nodo:

- batch.node.ubuntu 18.04
- batch.node.centos 7
- batch.node.windows amd64

## <a name="create-a-linux-pool-batch-python"></a>Crear un grupo de Linux: Python de Batch

El fragmento de código siguiente muestra un ejemplo de cómo usar la [biblioteca cliente de Microsoft Azure Batch para Python](https://pypi.python.org/pypi/azure-batch) a fin de crear un grupo de nodos de proceso del servidor de Ubuntu. Para más información sobre el módulo Python para Batch, consulte la [documentación de referencia](/python/api/overview/azure/batch).

En este fragmento de código, se crea una [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) explícitamente y se especifica cada una de sus propiedades (publicador, oferta, SKU, versión). Sin embargo, en el código de producción se recomienda usar el método [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) para seleccionar entre las combinaciones disponibles de SKU de agente de nodo e imagen en tiempo de ejecución.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Como se ha mencionado antes, es recomendable usar el método [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) para seleccionar de forma dinámica entre las combinaciones admitidas actualmente de agente de nodo e imagen de Marketplace (en lugar de crear explícitamente un elemento [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference)). El siguiente fragmento de código de Python muestra cómo usar este método.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Crear un grupo de Linux: .NET de Batch

El fragmento de código siguiente muestra un ejemplo de cómo utilizar la biblioteca cliente [.NET de Batch](https://www.nuget.org/packages/Microsoft.Azure.Batch/) para crear un grupo de nodos de proceso del servidor de Ubuntu. Para más información sobre .NET de Batch, consulte la [documentación de referencia](/dotnet/api/microsoft.azure.batch).

El fragmento de código siguiente usa el método [PoolOperations.ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) para seleccionar de la lista de combinaciones admitidas actualmente de SKU de agente de nodo e imagen de Marketplace. Esta técnica se recomienda porque la lista de combinaciones admitidas puede cambiar de vez en cuando. Normalmente, se agregan las combinaciones admitidas.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Aunque el fragmento de código anterior usa el método [PoolOperations.istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) para mostrar y seleccionar de forma dinámica entre las combinaciones admitidas (recomendadas) de SKU de agente de nodo e imagen, también puede configurar explícitamente un elemento [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference):

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>Conexión a los nodos de Linux mediante SSH

Durante el desarrollo o solución de problemas, es posible que necesite iniciar sesión en los nodos del grupo. A diferencia de los nodos de proceso de Windows, no puede utilizar el Protocolo de escritorio remoto (RDP) para conectarse a los nodos de Linux. En su lugar, el servicio Batch habilita el acceso SSH en cada nodo para la conexión remota.

El siguiente fragmento de código de Python crea un usuario en cada nodo de un grupo, que es necesario para la conexión remota. A continuación, imprime la información de conexión de SSH para cada nodo.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Este código tendrá una salida similar a la del siguiente ejemplo: En este caso, el grupo contiene cuatro nodos de Linux.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

En lugar de una contraseña, puede especificar una clave pública SSH al crear un usuario en un nodo. En el SDK de Python, use el parámetro **ssh_public_key** en [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser). En .NET, use la propiedad [ComputeNodeUser.SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey).

## <a name="pricing"></a>Precios

El servicio Azure Batch se basa en la tecnología de Azure Cloud Services y Azure Virtual Machines. El propio servicio Batch se ofrece sin costo, lo que significa que solo se cobra por los recursos de proceso (y los costos asociados que conlleva) usados por las soluciones del servicio. Al elegir **Configuración de la máquina virtual**, se le cobrará según la estructura de [precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/).

Si implementa aplicaciones en los nodos de Batch mediante [paquetes de aplicaciones](batch-application-packages.md), también se le cobrarán los recursos de Azure Storage que consumen los paquetes de aplicaciones.

## <a name="next-steps"></a>Pasos siguientes

- Explore los [ejemplos de código de Python](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) en el [repositorio azure-batch-samples de GitHub](https://github.com/Azure/azure-batch-samples) para ver cómo realizar operaciones de Batch comunes, como la creación de grupos, trabajos y tareas. El archivo [LÉAME](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) que acompaña a los ejemplos de Python incluye detalles sobre cómo instalar los paquetes necesarios.
- Aprenda sobre las [máquinas virtuales de prioridad baja](batch-low-pri-vms.md) con Batch.
