---
title: 'Guía de inicio rápido: Configuración de un clúster híbrido con Azure Managed Instance for Apache Cassandra'
description: En esta guía de inicio rápido se muestra cómo configurar un clúster híbrido con Azure Managed Instance for Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: bd334201d2dd93b38959aa9c8ebf19dc3125294f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751927"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Inicio rápido: Configuración de un clúster híbrido con Azure Managed Instance for Apache Cassandra (versión preliminar)

Azure Managed Instance for Apache Cassandra proporciona operaciones de implementación y escalado automatizadas para los centros de datos administrados de código abierto de Apache Cassandra. Este servicio le ayuda a acelerar los escenarios híbridos y a reducir el mantenimiento continuo.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En esta guía de inicio rápido se muestra cómo usar los comandos de la CLI de Azure para configurar un clúster híbrido. Si tiene centros de datos existentes en un entorno local o autohospedado, puede usar Azure Managed Instance for Apache Cassandra para agregar otros centros de datos a ese clúster y mantenerlos.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]



* Este artículo requiere la CLI de Azure 2.12.1 o una versión posterior. Si usa Azure Cloud Shell, la versión más reciente ya está instalada.

    > [!NOTE]
    > Asegúrese de que tiene la versión **0.9.0** (o superior) del módulo de la CLI `cosmosdb-preview` en ejecución en Cloud Shell. Esto es necesario para que todos los comandos que se enumeran a continuación funcionen correctamente. Puede comprobar las versiones de extensión mediante la ejecución de `az --version`. Si es necesario, actualice mediante `az extension update --name cosmosdb-preview`.

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) con conectividad a su entorno autohospedado o local. Para más información sobre cómo conectar entornos locales a Azure, consulte el artículo [Conexión de una red local a Azure](/azure/architecture/reference-architectures/hybrid-networking/).

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Configuración de un clúster híbrido

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya al recurso de Virtual Network.

1. Abra la pestaña **Subredes** y cree una nueva subred. Para más información sobre los campos del formulario **Agregar subred**, consulte el artículo [Incorporación, cambio o eliminación de una subred de red virtual](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet):

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Adición de una nueva subred a la red virtual." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

    > [!NOTE]
    > La implementación de Azure Managed Instance for Apache Cassandra requiere acceso a Internet. En entornos con acceso limitado a Internet se produce un error de implementación. Asegúrese de no bloquear el acceso desde la red virtual a los siguientes servicios de Azure que son esenciales para que las instancias administradas de Cassandra funcionen correctamente. También puede encontrar una amplia lista de dependencias de puertos y direcciones IP [aquí](network-rules.md). 
    > - Azure Storage
    > - Azure KeyVault
    > - Conjuntos de escalado de máquinas virtuales de Azure
    > - Supervisión de Azure
    > - Azure Active Directory
    > - Azure Security

1. Ahora aplicaremos algunos permisos especiales a la red virtual y a la subred que requiere Managed Instance for Apache Cassandra mediante la CLI de Azure. Utilice el comando `az role assignment create` y reemplace `<subscription ID>`, `<resource group name>` y `<VNet name>` con los valores adecuados:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Los valores `assignee` y `role` del comando anterior son los identificadores de rol y principio de servicio fijos, respectivamente.

1. A continuación, vamos a configurar los recursos para nuestro clúster híbrido. Puesto que ya tiene un clúster, el nombre del clúster solo será un recurso lógico para identificar el nombre del clúster existente. Asegúrese de usar el nombre del clúster existente al definir las variables `clusterName` y `clusterNameOverride` en el script siguiente. 
 
    También necesita, como mínimo, los nodos de inicialización del centro de datos existente y los certificados de intercambio necesarios para el cifrado de nodo a nodo. Azure Managed Instance for Apache Cassandra requiere cifrado de nodo a nodo para la comunicación entre centros de datos. Si no tiene implementado el cifrado de nodo a nodo en el clúster existente, deberá implementarlo. Consulte la documentación [aquí](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLNodeToNode.html). Debe proporcionar la ruta de acceso a la ubicación de los certificados. Todos los certificados deben estar en formato PEM; p. ej., `-----BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----`. En general, hay dos maneras de implementar certificados:

    1. Certificados autofirmados. Esto significa que existe un certificado privado y público (sin CA) para cada nodo. En este caso, necesitamos todos los certificados públicos.

    1. Certificados firmados por una CA. Puede ser una entidad de certificación autofirmada o incluso una pública. En este caso, necesitamos el certificado de entidad de certificación raíz (consulte las instrucciones sobre cómo [preparar certificados SSL para producción](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLCertWithCA.html)) y todos los intermediarios (si procede).

    Opcionalmente, si también ha implementado certificados de cliente a nodo (puede consultarlo [aquí](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLClientToNode.html)), también debe proporcionarlos en el mismo formato al crear el clúster híbrido. Vea el ejemplo a continuación.

   > [!NOTE]
   > El valor de la variable `delegatedManagementSubnetId` que proporcionará a continuación es exactamente el mismo que el valor de `--scope` proporcionado en el comando anterior:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2 10.52.221.3 10.52.221.4 \
      --external-gossip-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/gossipKeyStore.crt_signed
      # optional - add your existing datacenter's client-to-node certificates (if implemented):
      # --client-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/nodeKeyStore.crt_signed 
   ```

    > [!NOTE]
    > Si el clúster ya tiene cifrado de nodo a nodo y de cliente a nodo, debe saber dónde se guardan los certificados SSL de cliente o intercambio existentes. Si no está seguro, debería poder ejecutar `keytool -list -keystore <keystore-path> -rfc -storepass <password>` para imprimir los certificados. 

1. Una vez creado el recurso del clúster, ejecute el siguiente comando para obtener los detalles de configuración del clúster:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. El comando anterior devuelve información sobre el entorno de la instancia administrada. Necesitará los certificados de intercambio para poder instalarlos en el almacén de confianza para los nodos del centro de datos existente. En la captura de pantalla siguiente se muestra la salida del comando anterior y el formato de los certificados:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Obtenga los detalles del certificado del clúster." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

    > [!NOTE]
    > Los certificados devueltos por el comando anterior contienen saltos de línea representados como texto; por ejemplo, `\r\n`. Debe copiar cada certificado en un archivo y formatearlo antes de intentar importarlo en el almacén de confianza del centro de datos existente.

    > [!TIP]
    > Copie el valor de matriz `gossipCertificates` que se muestra en la captura de pantalla anterior en un archivo y use el siguiente script de Bash (tendrá que [descargar e instalar jq](https://stedolan.github.io/jq/download/) para la plataforma) para dar formato a los certificados y crear archivos pem independientes para cada uno.
    >
    > ```bash
    > readarray -t cert_array < <(jq -c '.[]' gossipCertificates.txt)
    > # iterate through the certs array, format each cert, write to a numbered file.
    > num=0
    > filename=""
    > for item in "${cert_array[@]}"; do
    >   let num=num+1
    >   filename="cert$num.pem"
    >   cert=$(jq '.pem' <<< $item)
    >   echo -e $cert >> $filename
    >   sed -e '1d' -e '$d' -i $filename
    > done
    > ```

1. A continuación, cree un nuevo centro de datos en el clúster híbrido. Asegúrese de reemplazar los valores de las variables por los detalles del clúster:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 9 
   ```

1. Ahora que se ha creado el nuevo centro de datos, ejecute el comando datacenter show para ver sus detalles:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. El comando anterior incluye en la salida los nodos de inicialización del nuevo centro de datos: 

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Obtención de detalles del centro de datos." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->


1. Ahora agregue los nodos de inicialización del nuevo centro de datos a la [configuración de nodos de inicialización](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html#configCassandra_yaml__seed_provider) del centro existente en el archivo [cassandra.yaml](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html). Además, instale los certificados de intercambio de instancia administrada que recopiló anteriormente en el almacén de confianza para cada nodo del clúster existente mediante el comando `keytool` para cada certificado:

    ```bash
        keytool -importcert -keystore generic-server-truststore.jks -alias CassandraMI -file cert1.pem -noprompt -keypass myPass -storepass truststorePass
    ```

   > [!NOTE]
   > Si desea agregar más centros de datos, puede repetir los pasos anteriores, pero solo necesita los nodos de inicialización. 

1. Por último, use la siguiente consulta CQL para actualizar la estrategia de replicación de cada espacio de claves para incluir todos los centros de datos del clúster:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3};
   ```
   También debe actualizar las tablas de contraseñas:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Solución de problemas

Si se produce un error al aplicar permisos a la red virtual, por ejemplo, se indica que *no se encuentra el usuario o la entidad de servicio en la base de datos de grafos para "e5007d2c-4b13-4a74-9b6a-605d99f03501"* , puede aplicar el mismo permiso manualmente desde Azure Portal. Para aplicar permisos desde el portal, vaya al panel **Control de acceso (IAM)** de la red virtual existente y agregue una asignación de roles para "Azure Cosmos DB" al rol "Administrador de red". Si aparecen dos entradas al buscar "Azure Cosmos DB", agregue las dos entradas, tal como se muestra en la siguiente imagen: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Permisos de aplicación" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> La asignación de roles de Azure Cosmos DB se usa solo con fines de implementación. Azure Managed Instance for Apache Cassandra no tiene dependencias de back-end en Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando este clúster de instancia administrada, elimínelo mediante los siguientes pasos:

1. En el menú de la izquierda de Azure Portal, seleccione **Grupos de recursos**.
1. En la lista, seleccione el grupo de recursos que creó para este inicio rápido.
1. En el panel **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
3. En la ventana siguiente, escriba el nombre del grupo de recursos que desea eliminar y, después, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a crear un clúster híbrido con la CLI de Azure y Azure Managed Instance for Apache Cassandra. Ahora puede empezar a trabajar con el clúster.

> [!div class="nextstepaction"]
> [Administración de recursos de Azure Managed Instance for Apache Cassandra mediante la CLI de Azure](manage-resources-cli.md)
