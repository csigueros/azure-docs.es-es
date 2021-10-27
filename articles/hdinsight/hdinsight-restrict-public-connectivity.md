---
title: Restricción de la conectividad pública en Azure HDInsight
description: Obtenga información sobre cómo quitar el acceso a todas las direcciones IP públicas de salida.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 6cc4a73c08d945f5a5ce26cef9c3774ffb5772d0
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069147"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight"></a>Restricción de la conectividad pública en Azure HDInsight

En la [arquitectura de red virtual predeterminada](./hdinsight-virtual-network-architecture.md) de Azure HDInsight, el proveedor de recursos de HDInsight se comunica con el clúster a través de la red pública. En este artículo, obtendrá información sobre los controles avanzados que puede usar para crear un clúster de HDInsight restringido en el que la conectividad de entrada está restringida a una red privada. 

Si quiere conectividad pública entre el clúster de HDInsight y los recursos dependientes, considere la posibilidad de restringir la conectividad del clúster siguiendo las instrucciones del [control del tráfico de red en Azure HDInsight](./control-network-traffic.md). Además de restringir la conectividad pública, puede configurar los recursos de dependencia habilitados para Azure Private Link para usar con clústeres de HDInsight.

En el diagrama siguiente se muestra el aspecto que podría tener una posible arquitectura de red virtual de HDInsight cuando `resourceProviderConnection` está establecido en *saliente*:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagrama de una arquitectura de HDInsight que usa una conexión de proveedor de recursos de salida.":::

> [!NOTE]
> Restringir la conectividad pública es un requisito previo para habilitar Private Link y no debe considerarse como la misma funcionalidad.

## <a name="initialize-a-restricted-cluster"></a>Inicialización de un clúster restringido

De manera predeterminada, el proveedor de recursos de HDInsight usa una conexión *entrante* al clúster mediante direcciones IP públicas. Cuando la propiedad de red `resourceProviderConnection` está establecida en *saliente*, invierte las conexiones al proveedor de recursos de HDInsight para que las conexiones siempre se inicien desde dentro del clúster y vayan hacia el proveedor de recursos. 

En esta configuración, sin una conexión de entrada, no es necesario configurar etiquetas de servicio de entrada en el grupo de seguridad de red. Tampoco es necesario omitir el firewall o la aplicación virtual de red a través de rutas definidas por el usuario.

Después de crear el clúster, configure la resolución DNS adecuada agregando los registros DNS necesarios para el clúster de HDInsight restringido. El registro DNS de nombre canónico (CNAME) siguiente se crea en la zona DNS pública administrada por Azure: `azurehdinsight.net`.

```dns
<clustername>    CNAME    <clustername>-int
```

Para acceder al clúster mediante nombres de dominio completos (FQDN), puede usar cualquiera de estas técnicas según sus necesidades:

- Use las direcciones IP privadas del equilibrador de carga interno directamente.
- Use su propia zona DNS privada para invalidar los puntos de conexión del clúster. En este caso, el nombre de la zona debe ser `azurehdinsight.net`.

Por ejemplo, para su zona DNS privada `azurehdinsight.net`, puede agregar sus direcciones IP privadas según sea necesario:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> No se recomienda colocar clústeres restringidos en la misma red virtual (con una zona DNS privada para `azurehdinsight.net`) que otros clústeres en los que está habilitada la conectividad pública. Podría provocar conflictos o comportamientos de resolución de DNS no deseados.

Para facilitar la configuración de DNS, se devuelven los nombres de dominio completo y las direcciones IP privadas correspondientes como parte de la respuesta `GET` del clúster. Puede usar este fragmento de código de PowerShell para empezar:

```powershell
<#
    This script is an example to help you get started with automation and can be adjusted based on your needs.
    This script assumes:
    - The HDInsight cluster has already been created, and the context where this script is run has permissions to read/write resources in the same resource group.
    - The private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of the Az.HDInsight module.

#>
$subscriptionId = "<Replace with subscription for deploying HDInsight clusters, and containing private DNS zone resource>"

$clusterName = "<Replace with cluster name>"
$clusterResourceGroupName = "<Replace with resource group name>"

# For example, azurehdinsight.net for public cloud.
$dnsZoneName = "<Replace with private DNS zone name>"
$dnsZoneResourceGroupName = "<Replace with private DNS zone resource group name>"

Connect-AzAccount -SubscriptionId $subscriptionId

# 1. Get cluster endpoints
$clusterEndpoints = $(Get-AzHDInsightCluster -ClusterName $clusterName ` -ResourceGroupName $clusterResourceGroupName).ConnectivityEndpoints

$endpointMapping = @{}

foreach($endpoint in $clusterEndpoints)
{
    $label = $endpoint.Location.ToLower().Replace(".$dnsZoneName".ToLower(), "")
    $ip = $endpoint.PrivateIPAddress

    $endpointMapping.Add($label, $ip)
}

# 2. Confirm that the DNS zone exists.
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone:
#    - If the entries already exist, update to the new IP.
#    - If the entries don't exist, create them.
$recordSets = Get-AzPrivateDnsRecordSet -ZoneName $dnsZoneName -ResourceGroupName $dnsZoneResourceGroupName -RecordType A

foreach($label in $endpointMapping.Keys)
{
    $updateRecord = $null

    foreach($record in $recordSets)
    {
        if($record.Name -eq $label)
        {
            $updateRecord = $record
            break;
        }
        
    }

    if($null -ne $updateRecord)
    {
        $updateRecord.Records[0].Ipv4Address = $endpointMapping[$label]
        Set-AzPrivateDnsRecordSet -RecordSet $updateRecord | Out-Null
    }
    else
    {
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $dnsZoneResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name $label `
            -RecordType A `
            -Ttl 3600 `
            -PrivateDnsRecord (New-AzPrivateDnsRecordConfig -Ipv4Address $endpointMapping[$label]) | Out-Null
    }
}

```

## <a name="add-private-link-connectivity-to-cluster-dependent-resources-optional"></a>Incorporación de conectividad de Private Link a recursos dependientes del clúster (opcional)

La configuración de `resourceProviderConnection` en *saliente* también le permite acceder a recursos específicos del clúster mediante puntos de conexión privados. Estos recursos incluyen:

- Almacenamiento: Azure Data Lake Storage Gen2 y Azure Blob Storage
- Metastores de SQL: Apache Ranger, Ambari, Oozie y Hive
- Azure Key Vault 

No es obligatorio usar puntos de conexión privados para estos recursos. Sin embargo, si planea hacerlo, debe crear estos recursos y configurar los puntos de conexión privados y las entradas DNS antes de crear el clúster de HDInsight. Todos estos recursos deben ser accesibles desde dentro de la subred del clúster, ya sea a través de un punto de conexión privado o de cualquier otro modo.

Al conectarse a Azure Data Lake Storage Gen2 a través de un punto de conexión privado, asegúrese de que la cuenta de almacenamiento de Gen2 tenga un punto de conexión establecido para `blob` y `dfs`. Para más información, consulte [Creación de un punto de conexión privado](../private-link/create-private-endpoint-portal.md).

## <a name="use-a-firewall-optional"></a>Uso del firewall (opcional)
Los clústeres de HDInsight todavía pueden conectarse a la red pública de Internet para obtener dependencias de salida. Si quiere restringir el acceso, puede [configurar un firewall](./hdinsight-restrict-outbound-traffic.md), pero no es requisito que lo haga.

## <a name="create-clusters"></a>Creación de clústeres

El fragmento de código JSON siguiente incluye las dos propiedades de red que tiene que configurar en una plantilla de Azure Resource Manager para crear un clúster de HDInsight privado:

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

Para una plantilla completa con muchas de las características de Enterprise Security de HDInsight, incluido Private Link, consulte el artículo sobre la [plantilla de Enterprise Security de HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

Para crear un clúster mediante PowerShell, consulte el [ejemplo](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

Para crear un clúster mediante la CLI de Azure, consulte el [ejemplo](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Pasos siguientes

* [Habilitación de Private Link en el clúster](./hdinsight-private-link.md)
* [Enterprise Security Package para Azure HDInsight](enterprise-security-package.md)
* [Directrices e información general de seguridad de la empresa en Azure HDInsight](./domain-joined/general-guidelines.md)
