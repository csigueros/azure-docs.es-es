---
title: 'Inicio rápido: uso de la CLI para crear un clúster de Azure Managed Instance for Apache Cassandra'
description: Use este inicio rápido para crear un clúster de Azure Managed Instance for Apache Cassandra mediante CLI de Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 795ad31b83f3a6f1acdd8bcc7561e6d75556b311
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892426"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli"></a>Inicio rápido: Creación de un clúster de Azure Managed Instance for Apache Cassandra mediante la CLI de Azure

Azure Managed Instance for Apache Cassandra proporciona operaciones de implementación y escalado automatizadas para los centros de datos administrados de código abierto de Apache Cassandra. Este servicio le ayuda a acelerar los escenarios híbridos y a reducir el mantenimiento continuo.

En este inicio rápido se muestra cómo usar los comandos de la CLI de Azure para crear un clúster con Azure Managed Instance for Apache Cassandra. También se muestra cómo crear un centro de recursos y escalar o reducir verticalmente los nodos en el centro de seguridad.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) con conectividad a su entorno autohospedado o local. Para obtener más información sobre cómo conectar entornos locales a Azure, consulte el artículo [Conexión de una red local a Azure](/azure/architecture/reference-architectures/hybrid-networking/).

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

> [!IMPORTANT]
> Este artículo requiere la CLI de Azure 2.30.0 o una versión posterior. Si usa Azure Cloud Shell, la versión más reciente ya está instalada.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Creación de un clúster de instancia administrada

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/)

1. Establezca el identificador de la suscripción en la CLI de Azure:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. A continuación, cree una red virtual con una subred dedicada en el grupo de recursos:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

   > [!NOTE]
   > La implementación de Azure Managed Instance for Apache Cassandra requiere acceso a Internet. En entornos con acceso limitado a Internet se produce un error de implementación. Asegúrese de no bloquear el acceso a los siguientes servicios de Azure que son esenciales para que las instancias administradas de Cassandra funcionen correctamente:
   > - Azure Storage
   > - Azure KeyVault
   > - Conjuntos de escalado de máquinas virtuales de Azure
   > - Supervisión de Azure
   > - Azure Active Directory
   > - Azure Security

1. Aplique algunos permisos especiales a la red virtual, ya que serán necesarios para la instancia administrada. Utilice el comando `az role assignment create` y reemplace `<subscriptionID>`, `<resourceGroupName>` y `<vnetName>` con los valores adecuados:

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > Los valores `assignee` y `role` del comando anterior son valores fijos. Escriba estos valores exactamente como se mencionó en el comando. Si no lo hace, se producirán errores al crear el clúster. Si se producen errores al ejecutar este comando, es posible que no tenga permisos para ejecutarlo. Para obtenerlos, póngase en contacto con el administrador.

1. Luego, cree el clúster en la red virtual recién creada mediante el comando [az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create). Ejecute el siguiente comando en el valor de la variable `delegatedManagementSubnetId`:

   > [!NOTE]
   > El valor de la variable `delegatedManagementSubnetId` que proporcionará a continuación es exactamente el mismo que el valor de `--scope` proporcionado en el comando anterior:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --initial-cassandra-admin-password $initialCassandraAdminPassword \
     --debug
   ```

1. Por último, cree un centro de datos para el clúster, con tres nodos, SKU de máquina virtual Standard D8s v4 y 4 discos P30 conectados para cada nodo mediante el comando [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create):

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   virtualMachineSKU='Standard_D8s_v4'
   noOfDisksPerNode=4
     
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 3 \
     --sku $virtualMachineSKU \
     --disk-capacity $noOfDisksPerNode \
     --availability-zone false
   ```

   > [!NOTE]
   > El valor de `--sku` se puede elegir entre las siguientes SKU disponibles:
   >
   > - Standard_E8s_v4
   > - Standard_E16s_v4 
   > - Standard_E20s_v4
   > - Standard_E32s_v4 
   > - Standard_DS13_v2
   > - Standard_DS14_v2
   > - Standard_D8s_v4
   > - Standard_D16s_v4
   > - Standard_D32s_v4 
   > 
   > Tenga en cuenta también que `--availability-zone` está establecido en `false`. Para habilitar las zonas de disponibilidad, establezca esta opción en `true`. 

   > [!WARNING]
   > Las zonas de disponibilidad no se admiten en todas las regiones. Se producirá un error en las implementaciones si selecciona una región en la que no se admiten las zonas de disponibilidad. Consulte [aquí](/azure/availability-zones/az-overview#azure-regions-with-availability-zones) las regiones admitidas. La implementación correcta de las zonas de disponibilidad también depende de la disponibilidad de los recursos de proceso en todas las zonas de la región dada. Podría producirse un error en las implementaciones si la SKU o la capacidad que ha seleccionado no están disponibles en todas las zonas. 

1. Una vez que se crea el centro de datos, si quiere escalar o reducir verticalmente los nodos que contiene, ejecute el comando [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update). Cambie el valor del parámetro `node-count` al valor deseado:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Conexión al clúster

Azure Managed Instance for Apache Cassandra no crea nodos con direcciones IP públicas. Para conectarse al clúster de Cassandra recién creado, tiene que crear otro recurso dentro de la red virtual. Este recurso puede ser una aplicación o una máquina virtual que tenga instalada la herramienta de consulta de código abierto de Apache [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html). Puede usar una [plantilla de Resource Manager](https://azure.microsoft.com/resources/templates/vm-simple-linux/) para implementar una máquina virtual de Ubuntu. Tras la implementación, use SSH para conectarse a la máquina e instalar CQLSH, tal y como se muestra en los siguientes comandos:

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
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>Solución de problemas

Si se produce un error al aplicar permisos a la red virtual mediante la CLI de Azure, por ejemplo, se indica que *no se encuentra el usuario o la entidad de servicio en la base de datos de grafos para "e5007d2c-4b13-4a74-9b6a-605d99f03501"* , puede aplicar el mismo permiso manualmente desde Azure Portal. Aprenda cómo hacerlo [aquí](add-service-principal.md).

> [!NOTE] 
> La asignación de roles de Azure Cosmos DB se usa solo con fines de implementación. Azure Managed Instance for Apache Cassandra no tiene dependencias de back-end en Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede usar el comando `az group delete` para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados cuando ya no se necesiten:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a crear un clúster de Azure Managed Instance for Apache Cassandra mediante la CLI de Azure. Ahora puede empezar a trabajar con el clúster:

> [!div class="nextstepaction"]
> [Implementación de un clúster de Apache Spark administrado con Azure Databricks](deploy-cluster-databricks.md)
