---
title: 'Inicio rápido: Creación de un clúster de Azure Managed Instance for Apache Cassandra desde Azure Portal'
description: En este inicio rápido se muestra cómo crear un clúster de Azure Managed Instance for Apache Cassandra mediante Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 03b5cc8b56e7d6825ec7bee5ec61d156e960123b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704219"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal"></a>Inicio rápido: Creación de un clúster de Azure Managed Instance for Apache Cassandra desde Azure Portal

Azure Managed Instance for Apache Cassandra proporciona operaciones automatizadas de implementación y escalado para centros de datos de Apache Cassandra de código abierto administrados, lo que permite acelerar los escenarios híbridos y reducir el mantenimiento continuo.

En este inicio rápido se muestra cómo usar Azure Portal para crear un clúster de Azure Managed Instance for Apache Cassandra.

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Creación de un clúster de instancia administrada

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. En la barra de búsqueda, escriba **Managed Instance for Apache Cassandra** y seleccione el resultado.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Búsqueda de Managed Instance for Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Seleccione el botón **Create Managed Instance for Apache Cassandra cluster** (Crear clúster de Managed Instance for Apache Cassandra).

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Creación del clúster." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. En el panel **Create Managed Instance for Apache Cassandra cluster** (Crear clúster de Managed Instance for Apache Cassandra), escriba los siguientes detalles:

   * **Suscripción**: en la lista desplegable, seleccione su suscripción de Azure.
   * **Grupo de recursos**: especifique si quiere crear un grupo de recursos o utilizar uno ya existente. Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. Para más información, consulte el artículo de introducción [Grupo de recursos de Azure](../azure-resource-manager/management/overview.md).
   * **Nombre del clúster**: escriba un nombre para el clúster.
   * **Ubicación**: lugar donde se implementará el clúster.
   * **Initial Cassandra admin password** (Contraseña inicial de administrador de Cassandra): la contraseña que se usa para crear el clúster.
   * **Confirm Cassandra admin password** (Confirmar contraseña de administrador de Cassandra): vuelva a escribir la contraseña.
   * **Virtual Network** (Red virtual): seleccione una red virtual y una subred existentes o cree una nueva. 
   * **Assign roles** (Asignar roles): las redes virtuales requieren permisos especiales para permitir la implementación de clústeres de Cassandra administrados. Mantenga activada esta casilla si va a crear una nueva red virtual o si usa una red virtual existente sin permisos aplicados. Si usa una red virtual en la que ya ha implementado clústeres de Instancia administrada de Cassandra, desactive esta opción.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Rellenar el formulario de creación del clúster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

   > [!NOTE]
   > La implementación de Azure Managed Instance for Apache Cassandra requiere acceso a Internet. En entornos con acceso limitado a Internet se produce un error de implementación. Asegúrese de no bloquear el acceso desde la red virtual a los siguientes servicios de Azure que son esenciales para que las instancias administradas de Cassandra funcionen correctamente. Consulte [Reglas de red de salida necesarias](network-rules.md) para obtener información más detallada.
   > - Azure Storage
   > - Azure KeyVault
   > - Conjuntos de escalado de máquinas virtuales de Azure
   > - Supervisión de Azure
   > - Azure Active Directory
   > - Azure Security

1. A continuación, seleccione la pestaña **Data center** (Centro de datos).

1. Escriba la siguiente información:

   * **Datacenter name** (Nombre de centro de datos): en la lista desplegable, seleccione su suscripción de Azure.
   * **Availability zone** (Zona de disponibilidad): active esta casilla si desea habilitar las zonas de disponibilidad.
   * **SKU Size** (Tamaño de SKU): elija entre los tamaños de SKU de máquina virtual disponibles.
   * **No. of disks** (Número de discos): elija el número de discos p30 que se conectarán a cada nodo de Cassandra.
   * **No. of nodes** (Número de nodos): elija el número de nodos de Cassandra que se implementarán en este centro de datos.

   :::image type="content" source="./media/create-cluster-portal/create-datacenter-page.png" alt-text="Revisión del resumen para crear el centro de datos." lightbox="./media/create-cluster-portal/create-datacenter-page.png" border="true":::

   > [!WARNING]
   > Las zonas de disponibilidad no se admiten en todas las regiones. Se producirá un error en las implementaciones si selecciona una región en la que no se admiten las zonas de disponibilidad. Consulte [aquí](../availability-zones/az-overview.md#azure-regions-with-availability-zones) las regiones admitidas. La implementación correcta de las zonas de disponibilidad también depende de la disponibilidad de los recursos de proceso en todas las zonas de la región dada. Podría producirse un error en las implementaciones si la SKU que ha seleccionado o la capacidad no están disponibles en todas las zonas. 

1. A continuación, seleccione **Revisar y crear** > **Crear**.

   > [!NOTE]
   > El clúster puede tardar hasta 15 minutos en crearse.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Revisión del resumen para crear el clúster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::

1. Una vez finalizada la implementación, compruebe el grupo de recursos para ver el clúster de instancia administrada recién creado:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Página de información general una vez creado el clúster." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Para examinar los nodos del clúster, vaya al recurso de clúster y abra el panel **Centro de datos** para verlos:

   :::image type="content" source="./media/create-cluster-portal/datacenter-1.png" alt-text="Consulta de los nodos del centro de datos." lightbox="./media/create-cluster-portal/datacenter-1.png" border="true":::

<!-- ## <a id="create-account"></a>Add a datacenter

1. To add another datacenter, click the add button in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/add-datacenter.png" alt-text="Click on add datacenter." lightbox="./media/create-cluster-portal/add-datacenter.png" border="true":::

   > [!WARNING]
   > If you are adding a datacenter in a different region, you will need to select a different virtual network. You will also need to ensure that this virtual network has connectivity to the primary region's virtual network created above (and any other virtual networks that are hosting datacenters within the managed instance cluster). Take a look at [this article](../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks) to learn how to peer virtual networks using Azure portal. You also need to make sure you have applied the appropriate role to your virtual network before attempting to deploy a managed instance cluster, using the below CLI command.
   >
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. Fill in the appropriate fields:

   * **Datacenter name** - From the drop-down, select your Azure subscription.
   * **Availability zone** - Check this box if you want availability zones to be enabled in this datacenter.
   * **Location** - Location where your datacenter will be deployed to.
   * **SKU Size** - Choose from the available Virtual Machine SKU sizes.
   * **No. of disks** - Choose the number of p30 disks to be attached to each Cassandra node.
   * **SKU Size** - Choose the number of Cassandra nodes that will be deployed to this datacenter.
   * **Virtual Network** - Select an Exiting Virtual Network and Subnet.  

   :::image type="content" source="./media/create-cluster-portal/add-datacenter-2.png" alt-text="Add Datacenter." lightbox="./media/create-cluster-portal/add-datacenter-2.png" border="true":::

   > [!WARNING]
   > Notice that we do not allow creation of a new virtual network when adding a datacenter. You need to choose an existing virtual network, and as mentioned above, you need to ensure there is connectivity between the target subnets where datacenters will be deployed. You also need to apply the appropriate role to the VNet to allow deployment (see above).

1. When the datacenter is deployed, you should be able to view all datacenter information in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/multi-datacenter.png" alt-text="View the cluster resources." lightbox="./media/create-cluster-portal/multi-datacenter.png" border="true":::

## Troubleshooting

If you encounter an error when applying permissions to your Virtual Network using Azure CLI, such as *Cannot find user or service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501'*, you can apply the same permission manually from the Azure portal. Learn how to do this [here](add-service-principal.md).

> [!NOTE] 
> The Azure Cosmos DB role assignment is used for deployment purposes only. Azure Managed Instanced for Apache Cassandra has no backend dependencies on Azure Cosmos DB.   -->

## <a name="connecting-to-your-cluster"></a>Conectarse a los clúster

Azure Managed Instance for Apache Cassandra no crea nodos con direcciones IP públicas, así que para conectarse al clúster de Cassandra recién creado, deberá crear otro recurso dentro de la red virtual. Puede ser una aplicación o una máquina virtual con la herramienta de consulta de código abierto de Apache, [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html), instalada. Puede usar una [plantilla](https://azure.microsoft.com/resources/templates/vm-simple-linux/) para implementar una máquina virtual de Ubuntu. Cuando se implemente, use SSH para conectarse a la máquina e instale CQLSH con los siguientes comandos:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
initial_admin_password="Password provided when creating the cluster"
cqlsh $host 9042 -u cassandra -p $initial_admin_password --ssl
```


## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando este clúster de instancia administrada, elimínelo mediante los siguientes pasos:

1. En el menú de la izquierda de Azure Portal, seleccione **Grupos de recursos**.
1. En la lista, seleccione el grupo de recursos que creó para este inicio rápido.
1. En el panel **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. En la ventana siguiente, escriba el nombre del grupo de recursos que desea eliminar y, después, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a crear un clúster de Azure Managed Instance for Apache Cassandra mediante Azure Portal. Ahora puede empezar a trabajar con el clúster:

> [!div class="nextstepaction"]
> [Implementación de un clúster de Apache Spark administrado con Azure Databricks](deploy-cluster-databricks.md)