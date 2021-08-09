---
title: Escalado vertical de un tipo de nodo principal de Azure Service Fabric
description: Escale verticalmente el clúster de Service Fabric agregando un nuevo tipo de nodo y quitando el anterior.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ad6b105d18f431c53c9fc46a463271e0481b4f0b
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2021
ms.locfileid: "110688585"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Escalado vertical del tipo de nodo principal de un clúster de Service Fabric

En este artículo se explica cómo se escala verticalmente el tipo de nodo principal de un clúster de Service Fabric con el tiempo de inactividad mínimo. Las actualizaciones de SKU locales no se admiten en nodos de clúster de Service Fabric, ya que estas operaciones pueden conllevar la pérdida de datos y de disponibilidad. El método más seguro, confiable y recomendado para escalar verticalmente un tipo de nodo de Service Fabric es:

1. Agregue un nuevo tipo de nodo a su clúster de Service Fabric, respaldado por la configuración y la SKU del conjunto de escalado de máquinas virtuales actualizadas (o modificadas). Este paso también implica la configuración de un nuevo equilibrador de carga, una subred y una dirección IP pública para el conjunto de escalado.

1. Una vez que los conjuntos de escalado original y actualizado se ejecuten en paralelo, deshabilite las instancias de nodo originales una a una para que los servicios del sistema (o las réplicas de servicios con estado) migren al nuevo conjunto de escalado.

1. Compruebe que el clúster y los nuevos nodos tengan un estado correcto y, a continuación, quite el conjunto de escalado original (y los recursos relacionados) y el estado del nodo de los nodos eliminados.

Lo siguiente le guiará a través del proceso de actualización del tamaño de VM y el sistema operativo de las VM del tipo de nodo principal de un clúster de ejemplo con [durabilidad Silver](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), respaldado por un solo conjunto de escalado con cinco nodos. Actualizaremos el tipo de nodo principal:

- Del tamaño de VM *Standard_D2_V2* a *Standard D4_V2* y
- Del sistema operativo de la máquina virtual *Windows Server 2016 Datacenter con Containers* a *Windows Server 2019 Datacenter con Containers*.

> [!WARNING]
> Antes de intentar este procedimiento en un clúster de producción, se recomienda que revise atentamente las plantillas de ejemplo y compruebe el proceso en un clúster de prueba. También es posible que el clúster no esté disponible durante un breve período.
>
> No intente un procedimiento de escalado vertical de tipo de nodo principal si el estado del clúster es incorrecto, ya que esto solo desestabilizará el clúster.

Estas son las plantillas de implementación de Azure paso a paso que usaremos para completar este escenario de actualización de ejemplo: https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>Configuración del clúster de prueba

Configuremos el clúster de prueba inicial de Service Fabric. En primer lugar, [descargue](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) las plantillas de muestra de Azure Resource Manager que usaremos para completar este escenario.

A continuación, inicie sesión en la cuenta de Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

A continuación, abra el archivo [*parameters.json*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) y actualice el valor de `clusterName` a algo único (en Azure).

Los comandos siguientes le guiarán a lo largo de la generación de un nuevo certificado autofirmado y la implementación del clúster de prueba. Si ya tiene un certificado que le gustaría usar, vaya a [Uso de un certificado existente para implementar el clúster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generación de un certificado autofirmado e implementación del clúster

En primer lugar, asigne las variables que necesitará para la implementación del clúster de Service Fabric. Ajuste los valores de `resourceGroupName`, `certSubjectName`, `parameterFilePath` y `templateFilePath` para su entorno y cuenta específicos:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> Asegúrese de que la ubicación `certOutputFolder` existe en el equipo local antes de ejecutar el comando para implementar un nuevo clúster de Service Fabric.

A continuación, implemente el clúster de prueba de Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Una vez finalizada la implementación, busque el archivo *.pfx* (`$certPfx`) en la máquina local e impórtelo al almacén de certificados:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

La operación devolverá la huella digital de certificado, que puede usar ahora para [conectarse al nuevo clúster](#connect-to-the-new-cluster-and-check-health-status) y comprobar su estado de mantenimiento. (Omita la sección siguiente, que es un enfoque alternativo a la implementación del clúster).

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Uso de un certificado existente para implementar el clúster

También puede usar un certificado de Azure Key Vault existente para implementar el clúster de prueba. Para ello, necesitará [obtener referencias a su instancia de Key Vault](#obtain-your-key-vault-references) y huella digital de certificado.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

A continuación, designe un nombre del grupo de recursos para el clúster y establezca las ubicaciones `templateFilePath` y `parameterFilePath`:

> [!NOTE]
> El grupo de recursos designado ya debe existir y debe estar ubicado en la misma región que el Key Vault.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Finalmente, ejecute el comando siguiente para implementar el clúster de prueba inicial:

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Conexión al nuevo clúster y comprobación del estado de mantenimiento

Conéctese al clúster y asegúrese de que los cinco nodos sean correctos (reemplace las variables `clusterName` y `thumb` por sus propios valores):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Con eso, estamos listos para comenzar el procedimiento de actualización.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Implementación de un nuevo tipo de nodo principal con un conjunto de escalado actualizado

Para actualizar (escalar verticalmente) un tipo de nodo, primero tendremos que implementar un nuevo tipo de nodo respaldado por un nuevo conjunto de escalado y recursos de apoyo. El nuevo conjunto de escalado se marcará como principal (`isPrimary: true`), al igual que el conjunto de escalado original (a menos que realice una actualización del tipo de nodo no principal). Los recursos creados en la sección siguiente se convertirán en última instancia en el nuevo tipo de nodo principal del clúster y se eliminarán los recursos de tipo de nodo principal originales.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Actualización de la plantilla de clúster con el conjunto de escalado actualizado

Estas son las modificaciones sección por sección de la plantilla de implementación del clúster original para agregar un nuevo tipo de nodo principal y recursos de apoyo.

Los cambios necesarios para este paso ya se han realizado automáticamente en el archivo de plantilla [*Step1-AddPrimaryNodeType.json*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) y lo siguiente explicará estos cambios de forma detallada. Si lo prefiere, puede omitir la explicación y continuar para [obtener las referencias de Key Vault](#obtain-your-key-vault-references) e [implementar la plantilla actualizada](#deploy-the-updated-template) que agrega un nuevo tipo de nodo principal al clúster.

> [!Note]
> Asegúrese de usar nombres que sean únicos del tipo de nodo original, el conjunto de escalado, el equilibrador de carga, la dirección IP pública y la subred del tipo de nodo principal original, ya que estos recursos se eliminarán en un paso posterior del proceso.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Creación de una nueva subred en la red virtual existente

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Creación de una nueva dirección IP pública con un valor de domainNameLabel único

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Creación de un nuevo equilibrador de carga para la dirección IP pública

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Creación de un nuevo conjunto de escalado de máquinas virtuales (con la máquina virtual actualizada y las SKU del sistema operativo)

Referencia del tipo de nodo

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

SKU de la máquina virtual

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU del sistema operativo

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Además, asegúrese de que incluye las extensiones adicionales necesarias para la carga de trabajo.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Incorporación de un nuevo tipo de nodo principal al clúster

Ahora que el nuevo tipo de nodo (vmNodeType1Name) tiene su propio nombre, subred, dirección IP, equilibrador de carga y conjunto de escalado, puede volver a usar todas las demás variables del tipo de nodo original (por ejemplo, `nt0applicationEndPort` `nt0applicationStartPort` y `nt0fabricTcpGatewayPort`):

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

Una vez que haya implementado todos los cambios en los archivos de plantilla y de parámetros, continúe con la sección siguiente para adquirir las referencias de Key Vault e implementar las actualizaciones en el clúster.

### <a name="obtain-your-key-vault-references"></a>Obtener las referencias de Key Vault

Para implementar la configuración actualizada, necesitará varias referencias al certificado de clúster almacenado en Key Vault. La forma más fácil de encontrar estos valores es desde Azure Portal. Necesitará:

* **La dirección URL de Key del certificado del clúster.** En la instancia de Key Vault de Azure Portal, seleccione **Certificados** > *el certificado que quiera* > **Identificador secreto**:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **La huella digital del certificado del clúster.** (Probablemente ya lo tenga si se [conectó al clúster original](#connect-to-the-new-cluster-and-check-health-status) para comprobar su estado de mantenimiento). En la misma hoja de certificado (**Certificados** > *el certificado que quiera*) en Azure Portal, copie la **huella digital X.509 SHA-1 (en formato hexadecimal)** :

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Identificador de recurso de Key Vault.** En la instancia de Key Vault en Azure Portal, seleccione **Propiedades** > **Id. de recurso**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Implementar la plantilla actualizada

Ajuste `templateFilePath` según sea necesario y ejecute el siguiente comando:

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Una vez finalizada la implementación, vuelva a comprobar el estado del clúster y asegúrese de que todos los nodos de ambos tipos de nodo tengan un estado correcto.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>Migración de nodos de inicialización al nuevo tipo de nodo

Ahora estamos listos para actualizar el tipo de nodo original como no principal y empezar a deshabilitar sus nodos. A medida que se deshabilitan los nodos, los servicios del sistema del clúster y los nodos de inicialización migran al nuevo conjunto de escalado.

### <a name="unmark-the-original-node-type-as-primary"></a>Desmarcar el tipo de nodo original como principal

En primer lugar, quite la designación `isPrimary` en la plantilla del tipo de nodo original.

```json
{
    "isPrimary": false,
}
```

A continuación, implemente la plantilla con la actualización. De este modo se iniciará la migración de los nodos de inicialización al nuevo conjunto de escalado.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Se tardará un tiempo en completar la migración de los nodos de inicialización al nuevo conjunto de escalado. Para garantizar la coherencia de los datos, solo se puede cambiar un nodo de inicialización a la vez. Cada cambio de nodo de inicialización requiere una actualización de clúster. Por lo tanto, para reemplazar un nodo de inicialización, se requieren dos actualizaciones de clúster (para la adición y eliminación de nodos). La actualización de los cinco nodos de inicialización en este escenario de muestra dará como resultado diez actualizaciones de clúster.

Use Service Fabric Explorer para supervisar la migración de los nodos de inicialización al nuevo conjunto de escalado. Todos los nodos del tipo de nodo original (nt0vm) deben ser *false* en la columna **IsSeedNode** y los del nuevo tipo de nodo (nt1vm) serán *true*.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>Deshabilitación de los nodos del conjunto de escalado de tipo de nodo original

Una vez que todos los nodos de inicialización hayan migrado al nuevo conjunto de escalado, podrá deshabilitar los nodos del conjunto de escalado original.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Use Service Fabric Explorer para supervisar la progresión de los nodos del conjunto de escalado original del estado *Desactivando* a *Deshabilitado*.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="Service Fabric Explorer con los estados de los nodos deshabilitados":::

En el caso de la durabilidad Silver y Gold, algunos nodos se pondrán en estado Deshabilitado, mientras que otros podrían permanecer en el estado *Desactivando*. En Service Fabric Explorer, compruebe la pestaña **Detalles** de los nodos en estado Desactivando. Si muestran una *comprobación de seguridad pendiente* de tipo *EnsurePartitionQuorem* (lo que garantiza el cuórum para las particiones del servicio de infraestructura), es seguro continuar.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="Puede continuar con la detención de datos y la eliminación de nodos bloqueados en estado &quot;Desactivando&quot; si muestran una comprobación de seguridad pendiente de tipo &quot;EnsurePartitionQuorum&quot;.":::

Si el clúster es una durabilidad Bronze, espere a que todos los nodos lleguen al estado *Deshabilitado*.

### <a name="stop-data-on-the-disabled-nodes"></a>Detener datos en los nodos deshabilitados

Ahora puede detener los datos en los nodos deshabilitados.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Quitar el tipo de nodo original y limpiar sus recursos

Estamos preparados para quitar el tipo de nodo original y sus recursos asociados para concluir el procedimiento de escalado vertical.

### <a name="remove-the-original-scale-set"></a>Quitar el conjunto de escalado original

En primer lugar, quite el conjunto de escalado de respaldo del tipo de nodo.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>Eliminación de los recursos Equilibrador de carga e IP originales

Ahora puede eliminar los recursos Equilibrador de carga e IP originales. En este paso también actualizará el nombre DNS.

> [!Note]
> Este paso es opcional si ya usa una IP pública de la SKU *Estándar* y un equilibrador de carga. En este caso puede tener varios conjuntos de escalado o tipos de nodos en el mismo equilibrador de carga.

Ejecute los siguientes comandos, modificando el valor `$lbname` según sea necesario.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Eliminación del estado del nodo del tipo de nodo original

Los nodos de tipo de nodo originales ahora mostrarán *Error* para su **Estado de mantenimiento**. Quite su estado del nodo del clúster.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer debería reflejar ahora solo los cinco nodos del nuevo tipo de nodo (nt1vm), todos ellos con los valores de estado de mantenimiento *Aceptar*. El estado de mantenimiento del clúster seguirá mostrando *Error*. Corregiremos esto a continuación actualizando la plantilla para reflejar los cambios más recientes y volviendo a implementar.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>Actualización de la plantilla de implementación para reflejar el tipo de nodo principal recién escalado verticalmente

Los cambios necesarios para este paso ya se han realizado automáticamente en el archivo de plantilla [*Step3-CleanupOriginalPrimaryNodeType.json*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) y en las secciones siguientes se explicarán estos cambios de plantilla de forma detallada. Si lo prefiere, puede omitir la explicación y continuar para [implementar la plantilla actualizada](#deploy-the-finalized-template) y completar el tutorial.

#### <a name="update-the-cluster-management-endpoint"></a>Actualización del punto de conexión de administración del clúster

Actualice el clúster `managementEndpoint` en la plantilla de implementación para hacer referencia a la nueva dirección IP (mediante la actualización de *vmNodeType0Name* con *vmNodeType1Name*).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>Eliminación de la referencia de tipo de nodo original

Elimine la referencia del tipo de nodo original del recurso de Service Fabric en la plantilla de implementación:

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Configuración de las directivas de mantenimiento para omitir los errores existentes

Solo en el caso de los clústeres de durabilidad Silver y superior, actualice el recurso de clúster en la plantilla y configure directivas de mantenimiento para omitir el estado de la aplicación `fabric:/System`; para ello, agregue *applicationDeltaHealthPolicies* en las propiedades del recurso de clúster como se indica a continuación. La directiva siguiente omitirá los errores existentes, pero no permitirá nuevos errores de estado.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Eliminación de recursos de apoyo para el tipo de nodo original

Elimine de la plantilla de ARM y el archivo de parámetros el resto de recursos que tengan relación con el tipo de nodo original. Elimine lo siguiente:

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>Implementación de la plantilla finalizada

Por último, implemente la plantilla de Azure Resource Manager modificada.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Este paso tardará un rato, habitualmente puede llegar a las dos horas.

La actualización cambiará la configuración a *InfrastructureService*; por lo que es necesario reiniciar el nodo. En este caso, *forceRestart* se omite. El parámetro `upgradeReplicaSetCheckTimeout` especifica el tiempo máximo que Service Fabric espera a que una partición pase a un estado seguro, si aún no lo está. Una vez que se superan las comprobaciones de seguridad de todas las particiones de un nodo, Service Fabric continúa con la actualización en ese nodo. El valor del parámetro `upgradeTimeout` puede reducirse a 6 horas, pero debe usarse la seguridad máxima de 12 horas.

Una vez finalizada la implementación, compruebe en Azure Portal que el estado del recurso de Service Fabric es *Listo*. Compruebe que puede tener acceso al nuevo punto de conexión de Service Fabric Explorer, que el **estado de mantenimiento del clúster** es *Aceptar* y que las aplicaciones implementadas funcionan correctamente.

Así ha escalado verticalmente un tipo de nodo principal del clúster.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre la [incorporación de un tipo de nodo a un clúster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Obtenga información sobre [escalabilidad de aplicaciones](service-fabric-concepts-scalability.md).
* [Escalado horizontal de un clúster de Azure](service-fabric-tutorial-scale-cluster.md).
* [Escalado de un clúster de Azure mediante programación](service-fabric-cluster-programmatic-scaling.md) con el SDK de proceso de Azure.
* [Escalado o reducción horizontal de un clúster independiente](service-fabric-cluster-windows-server-add-remove-nodes.md)
