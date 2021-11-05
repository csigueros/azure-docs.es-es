---
title: Configuración de una red virtual para la inserción estándar de un entorno de ejecución de integración de SSIS de Azure
description: Aprenda a configurar una red virtual para la inserción estándar de un entorno de ejecución de integración de SSIS de Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ee88df19385041de660da311fe51c66099d15cc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091269"
---
# <a name="standard-virtual-network-injection-method"></a>Método de inserción de red virtual estándar

Al usar SQL Server Integration Services (SSIS) en Azure Data Factory (ADF), hay dos métodos para conectar el entorno de ejecución de integración de SSIS (IR) de Azure a una red virtual: estándar y rápido. Si usa el método estándar, debe configurar la red virtual para cumplir estos requisitos:

- Asegúrese de que *Microsoft.Batch* es un proveedor de recursos registrado en la suscripción de Azure que tiene la red virtual idónea para la conexión a la misma de la instancia de Azure-SSIS IR. Para obtener instrucciones detalladas, consulte la sección [Registrar Azure Batch como proveedor de recursos](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

- Asegúrese de que al usuario que crea Azure-SSIS IR se le conceden los permisos de control de acceso basado en rol (RBAC) necesarios para conectarse a la red virtual o subred.  Para obtener más información, consulte la sección [Selección de permisos de red virtual](#perms) a continuación.

- Seleccione una subred adecuada en la red virtual para que Azure-SSIS IR se conecte. Para obtener más información, consulte la sección [Seleccionar una subred](#subnet) a continuación.

En función de su escenario específico, puede configurar opcionalmente lo siguiente:

- Si desea traer sus propias direcciones IP públicas estáticas (BYOIP) para el tráfico saliente de su Azure-SSIS IR, consulte la sección [Configuración de direcciones IP públicas estáticas](#ip) a continuación.

- Si desea usar su propio servidor de sistema de nombres de dominio (DNS) en la red virtual, consulte la sección [Configuración de un servidor DNS personalizado](#dns) a continuación.

- Si desea usar un grupo de seguridad de red (NSG) para limitar el tráfico entrante y saliente en la subred, consulte la sección [Configuración de un grupo de seguridad de red](#nsg) a continuación.

- Si desea usar rutas definidas por el usuario (UDR) para auditar e inspeccionar el tráfico saliente, consulte la sección [Configuración de UDR](#udr) a continuación.

- Asegúrese de que el grupo de recursos de la red virtual (o el grupo de recursos de las direcciones IP públicas, si trae las suyas propias) puede crear y eliminar determinados recursos de red de Azure. Para más información, consulte [Configuración del grupo de recursos correspondiente](#rg). 

- Si personaliza su Azure-SSIS IR como se describe en el artículo [Configuración personalizada para Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md), nuestro proceso interno para administrar sus nodos consumirá direcciones IP privadas de un intervalo predefinido de *172.16.0.0* a *172.31.255.255*. Por lo tanto, asegúrese de que los intervalos de direcciones IP privadas de las redes virtuales o locales no entren en conflicto con este intervalo.

Este diagrama muestra las conexiones necesarias para Azure-SSIS Integration Runtime:

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Diagrama que muestra las conexiones necesarias para Azure-SSIS IR.":::

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>Selección de permisos de red virtual

Para habilitar la inserción de red virtual estándar, al usuario que crea Azure-SSIS IR se le deben conceder los permisos de RBAC necesarios para conectarse a la red virtual o subred.

- Si va a conectar Azure-SSIS IR a una Azure Resource Manager virtual, tiene dos opciones:

  - Usar el rol integrado *Colaborador de red*. Este rol requiere el permiso _Microsoft.Network /\*_ , que tiene un ámbito mucho mayor del necesario.

  - Crear un rol personalizado que incluya solo el permiso _Microsoft.Network/virtualNetworks/\*/join/action_ necesario. Si también quiere traer sus propias direcciones IP públicas estáticas para Azure-SSIS IR al conectarla a una red virtual de Azure Resource Manager, incluya también el permiso _Microsoft.Network/publicIPAddresses/\*/join/action_ en el rol.

  - Para obtener instrucciones detalladas, consulte la sección [Concesión de permisos de red virtual](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms).

- Si va a conectar su Azure-SSIS IR a una red virtual clásica, se recomienda usar el rol *Colaborador de la máquina virtual clásica* integrado. De lo contrario, tendrá que crear un rol personalizado que incluya el permiso para conectarse a la red virtual. También debe asignar *MicrosoftAzureBatch* a ese rol integrado o personalizado.

## <a name="select-a-subnet"></a><a name="subnet"></a>Seleccione una subred

Para habilitar la inserción de red virtual estándar, debe seleccionar una subred adecuada para que el Azure-SSIS IR se conecte:

- No seleccione GatewaySubnet, ya que está dedicado para puertas de enlace de red virtual.

- Asegúrese de que la subred seleccionada tenga direcciones IP disponibles para al menos dos veces su número de nodo de Azure-SSIS IR. Estos son necesarios para evitar interrupciones al implementar revisiones o actualizaciones para su Azure-SSIS IR. Azure también reserva algunas direcciones IP que no se pueden usar en cada subred. La primera y la última dirección IP están reservadas para el cumplimiento del protocolo, mientras que otras tres direcciones están reservadas para los servicios de Azure. Para más información, consulte la sección [Restricciones de direcciones IP de subred](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

- No use una subred que esté ocupada exclusivamente por otros servicios de Azure (por ejemplo, Azure SQL Managed Instance, App Service, etc.). 

## <a name="configure-static-public-ip-addresses"></a><a name="ip"></a>Configuración de direcciones IP públicas estáticas

Si desea traer sus propias direcciones IP públicas estáticas para el tráfico saliente de Azure-SSIS IR mientras lo conecta a una red virtual, para que pueda permitirlas en los firewalls, asegúrese de que cumplen los requisitos siguientes:

- Se deben proporcionar exactamente dos sin usar que no estén ya asociadas a otros recursos de Azure. Se usará la adicional en las actualizaciones periódicas de Azure-SSIS IR. Tenga en cuenta que no se puede compartir una dirección IP pública entre las instancias de Azure-SSIS IR activas.

- Ambas deben ser estáticas de tipo estándar. Consulte la sección [SKU de la dirección IP pública](../virtual-network/ip-services/public-ip-addresses.md#sku) para obtener más detalles.

- Las dos deben tener un nombre DNS. Si no ha proporcionado un nombre DNS al crearlos, puede hacerlo en Azure Portal.

  :::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Integration Runtime de SSIS de Azure":::

- Estas y la red virtual deben estar en la misma suscripción y en la misma región.

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>Configuración de un servidor DNS personalizado 

Si desea usar su propio servidor DNS en la red virtual para resolver los nombres de host privados, asegúrese de que también puede resolver nombres de host globales de Azure (por ejemplo, Azure Blob Storage denominado `<your storage account>.blob.core.windows`).

Se recomienda configurar su propio servidor DNS para desviar solicitudes DNS sin resolver a la dirección IP de las resoluciones recursivas de Azure (*168.63.129.16*).

Para más información, consulte la sección [Resolución de nombres de servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!NOTE]
> Use un nombre de dominio completo (FQDN) para el nombre de host privado (por ejemplo, use `<your_private_server>.contoso.com` en lugar de `<your_private_server>`). Como alternativa, puede utilizar una configuración personalizada estándar en Azure-SSIS IR para anexar automáticamente su propio sufijo DNS (por ejemplo `contoso.com`) a un nombre de dominio de una sola etiqueta no calificado y convertirlo en un FQDN antes de usarlo en consultas DNS (consulte la sección [Ejemplos de instalación personalizada estándar](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples)). 

## <a name="configure-an-nsg"></a><a name="nsg"></a>Configuración de un NSG

Si desea usar un grupo de seguridad de red en la subred conectada por la Azure-SSIS IR, permita el siguiente tráfico entrante y saliente:

| Dirección | Protocolo de transporte | Source | Puertos de origen | Destination | Puertos de destino | Comentarios | 
|-----------|--------------------|--------|--------------|-------------|-------------------|----------| 
| Entrada | TCP | *BatchNodeManagement* | * | *VirtualNetwork* | *29876, 29877* (si conecta SSIS IR a una red virtual de Azure Resource Manager)<br/><br/>*10100, 20100, 30100* (si conecta SSIS IR a una red virtual clásica)| El servicio de Data Factory utiliza estos puertos para comunicarse con los nodos Azure-SSIS IR en la red virtual.<br/><br/>Independientemente de si crea o no un NSG en la subred, Data Factory configura siempre un NSG en la tarjeta de interfaz de red (NIC) adjunta a las máquinas virtuales que hospedan el Azure-SSIS IR.<br/><br/>El NSG de nivel de NIC solo permite el tráfico entrante de direcciones IP de Data Factory en los puertos especificados.<br/><br/>Incluso si abre estos puertos al tráfico de Internet a nivel de subred, el tráfico de direcciones IP que no son direcciones IP de Data Factory sigue bloqueado en el nivel de NIC. | 
| Entrada | TCP | *CorpNetSaw* | * | *VirtualNetwork* | *3389* | (Opcional) Solo es necesario cuando un ingeniero de soporte técnico de Microsoft le pide que abra el puerto *3389* para solucionar problemas avanzados y se puede cerrar justo después de solucionar problemas.<br/><br/>La etiqueta de servicio *CorpNetSaw* solo permite que las máquinas de estación de trabajo de acceso seguro (SAW) de la red corporativa de Microsoft accedan a su Azure-SSIS IR a través del protocolo de escritorio remoto (RDP).<br/><br/>Esta etiqueta de servicio no se puede seleccionar de Azure Portal, solo está disponible a través de Azure PowerShell/CLI.<br/><br/>En el grupo de seguridad de red de nivel de NIC, el puerto *3389* está abierto de forma predeterminada, pero puede controlarlo con un NSG de nivel de subred, mientras que el tráfico saliente en él no está permitido de forma predeterminada en los nodos de Azure-SSIS IR mediante una regla de firewall de Windows. | 

| Dirección | Protocolo de transporte | Source | Puertos de origen | Destination | Puertos de destino | Comentarios |
|-----------|--------------------|--------|--------------|-------------|-------------------|----------|
| Salida | TCP | *VirtualNetwork* | * | *AzureCloud* | *443* | Necesario para que Azure-SSIS IR acceda a los servicios de Azure, como Azure Storage y Azure Event Hubs. | 
| Salida | TCP | *VirtualNetwork* | * | *Internet* | *80* | (Opcional) El Azure-SSIS IR usa este puerto para descargar una lista de revocación de certificados (CRL) desde Internet.<br/><br/>Si bloquea este tráfico, podría experimentar una degradación del rendimiento al iniciar el Azure-SSIS IR y perder la capacidad de comprobar las CRL al usar certificados, lo que no se recomienda desde el punto de vista de la seguridad.<br/><br/>Si desea restringir los destinos fuera de servicio a determinados FQDN, consulte la sección **Configuración de UDR** a continuación | 
| Salida | TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433, 11000-11999* | (Opcional) Solo es necesario si usa el servidor de Azure SQL Database o una Instancia administrada para hospedar el catálogo de SSIS (SSISDB).<br/><br/>Si el servidor de Azure SQL Database o la Instancia administrada están configurados con un punto de conexión público o un punto de conexión de servicio de red virtual, use la etiqueta de servicio *Sql* como destino.<br/><br/>Si el servidor de Azure SQL Database o instancia administrada están configurados con un punto de conexión privado, use la etiqueta de servicio *VirtualNetwork* como destino.<br/><br/>Si la directiva de conexión del servidor está establecida en *Proxy*, en lugar de *Redirigir*, solo se requiere el puerto *1433*. | 
| Salida | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | (Opcional) Solo es necesario si usa un contenedor de Azure Storage blobs para almacenar el script o los archivos de instalación personalizados estándar.<br/><br/>Si Azure Storage está configurado con un punto de conexión público o un punto de conexión de servicio de red virtual, use la etiqueta de servicio de *Storage* como destino.<br/><br/>Si Azure Storage está configurado con un punto de conexión privado, use la etiqueta de servicio *VirtualNetwork* como destino. | 
| Salida | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | (Opcional) Solo es necesario si necesita acceder a Azure Files.<br/><br/>Si Azure Storage está configurado con un punto de conexión público o un punto de conexión de servicio de red virtual, use la etiqueta de servicio de *Storage* como destino.<br/><br/>Si Azure Storage está configurado con un punto de conexión privado, use la etiqueta de servicio *VirtualNetwork* como destino. | 

## <a name="configure-udrs"></a><a name="udr"></a>Configuración de UDR

Si desea auditar o inspeccionar el tráfico saliente desde su Azure-SSIS IR, puede usar [rutas definidas por el usuario (UDR)](../virtual-network/virtual-networks-udr-overview.md) para redirigirlo a un dispositivo de firewall local [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) a través de una tunelización forzada que anuncia una ruta de protocolo de puerta de enlace de borde (BGP) *0.0.0.0/0* a la red virtual, a una aplicación virtual de red (NVA) configurada como firewall o a un servicio [Azure Firewall](../firewall/overview.md).

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="Escenario de NVA para Azure-SSIS Integration Runtime":::

Para que funcione, debe asegurarse de lo siguiente:

- El tráfico entre el servicio de administración de Azure Batch y su Azure-SSIS IR no debe enrutarse a un dispositivo o servicio de firewall.

- El dispositivo o servicio de firewall debe permitir el tráfico saliente requerido por Azure-SSIS IR.

Si el tráfico entre el servicio de administración de Azure Batch y su Azure-SSIS IR se enruta a un dispositivo o servicio de firewall, se interrumpirá debido al enrutamiento asimétrico. Las UDR deben definirse para este tráfico, de modo que pueda salir a través de las mismas rutas en las que llegó. Puede configurar UDR para enrutar el tráfico entre el servicio de administración de Azure Batch y su Azure-SSIS IR con el tipo de próximo salto como *Internet*.

Por ejemplo, si su Azure-SSIS IR se encuentra en el *Sur de Reino Unido* y desea inspeccionar el tráfico saliente mediante Azure Firewall, primero puede obtener los intervalos IP de la etiqueta de servicio *BatchNodeManagement.UKSouth* desde el [vínculo de descarga del intervalo IP de la etiqueta de servicio](https://www.microsoft.com/download/details.aspx?id=56519) o la [API de detección de etiquetas de servicio](../virtual-network/service-tags-overview.md#service-tags-on-premises). A continuación, puede configurar las siguientes UDR para las rutas de intervalo IP relevantes con el siguiente próximo salto como *Internet* y la ruta *0.0.0.0/0* con el tipo próximo salto como *aplicación virtual*.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Configuración de UDR de Azure Batch":::

> [!NOTE]
> Este enfoque supone un costo de mantenimiento adicional, ya que debe comprobar periódicamente los intervalos IP pertinentes y agregar UDR para los nuevos a fin de evitar que Azure-SSIS IR se interrumpa. Se recomienda comprobarlos mensualmente, ya que cuando aparezca un nuevo intervalo IP para la etiqueta de servicio correspondiente, se tardará otro mes en entrar en vigor. 

Puede ejecutar el siguiente script de PowerShell para agregar UDR para el servicio de administración de Azure Batch:

```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your route table]"
$RouteTableResourceName = "[resource name of your route table]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start adding UDRs to your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch Azure service tag, please confirm that your location is valid."
}
```

Mediante la guía de la sección Anterior [Configuración de un grupo de seguridad de red](#nsg), debe implementar reglas similares en el dispositivo o servicio del firewall para permitir el tráfico saliente desde la Azure-SSIS IR:

- Si usa Azure Firewall:
  - Debe abrir el puerto *443* para el tráfico TCP saliente con la etiqueta de servicio *AzureCloud* como destino.

  - Si usa Azure SQL Database server/Instancia administrada para hospedar SSISDB, debe abrir los puertos *1433, 11000-11999* para el tráfico TCP saliente con la etiqueta de servicio *Sql/VirtualNetwork* como destino.

  - Si usa un contenedor de blobs de Azure Storage para almacenar el script o los archivos de instalación personalizada estándar, debe abrir el puerto *443* para el tráfico TCP saliente con la etiqueta de servicio *Storage/VirtualNetwork* como destino.

  - Si necesita acceder a Azure Files, debe abrir el puerto *445* para el tráfico TCP saliente con la etiqueta de servicio *Storage/VirtualNetwork* como destino.

- Si usa otro dispositivo o servicio de firewall:
  - Debe abrir el puerto *443* para el tráfico TCP saliente con *0.0.0.0/0* o los siguientes FQDN específicos del entorno de Azure como destino.

    | Entorno de Azure | FQDN |
    |-------------------|-------|
    | <b>Azure público</b> | <ul><li><b>Azure Data Factory (administración)</b><ul><li>_\*.frontend.clouddatahub.net_</li></ul></li><li><b>Azure Storage (administración)</b><ul><li>_\*.blob.core.windows.net_</li><li>_\*.table.core.windows.net_</li></ul></li><li><b>Azure Container Registry (instalación personalizada)</b><ul><li>_\*.azurecr.io_</li></ul></li><li><b>Event Hubs (registro)</b><ul><li>_\*.servicebus.windows.net_</li></ul></li><li><b>Servicio de registro de Microsoft (uso interno)</b><ul><li>_gcs.prod.monitoring.core.windows.net_</li><li>_prod.warmpath.msftcloudes.com_</li><li>_azurewatsonanalysis-prod.core.windows.net_</li></ul></li></ul> |
    | <b>Azure Government</b> | <ul><li><b>Azure Data Factory (administración)</b><ul><li>_\*.frontend.datamovement.azure.us_</li></ul></li><li><b>Azure Storage (administración)</b><ul><li>_\*.blob.core.usgovcloudapi.net_</li><li>_\*.table.core.usgovcloudapi.net_</li></ul></li><li><b>Azure Container Registry (instalación personalizada)</b><ul><li>_\*.azurecr.us_</li></ul></li><li><b>Event Hubs (registro)</b><ul><li>_\*.servicebus.usgovcloudapi.net_</li></ul></li><li><b>Servicio de registro de Microsoft (uso interno)</b><ul><li>_fairfax.warmpath.usgovcloudapi.net_</li><li>_azurewatsonanalysis.usgovcloudapp.net_</li></ul></li></ul> |
    | <b>Azure China 21Vianet</b> | <ul><li><b>Azure Data Factory (administración)</b><ul><li>_\*.frontend.datamovement.azure.cn_</li></ul></li><li><b>Azure Storage (administración)</b><ul><li>_\*.blob.core.chinacloudapi.cn_</li><li>_\*.table.core.chinacloudapi.cn_</li></ul></li><li><b>Azure Container Registry (instalación personalizada)</b><ul><li>_\*.azurecr.cn_</li></ul></li><li><b>Event Hubs (registro)</b><ul><li>_\*.servicebus.chinacloudapi.cn_</li></ul></li><li><b>Servicio de registro de Microsoft (uso interno)</b><ul><li>_mooncake.warmpath.chinacloudapi.cn_</li><li>_azurewatsonanalysis.chinacloudapp.cn_</li></ul></li></ul> |

  - Si usa el servidor de Azure SQL Database o una instancia administrada para hospedar SSISDB, debe abrir los puertos *1433, 11000-11999* para el tráfico TCP saliente con *0.0.0.0/0* o el FQDN del servidor de Azure SQL Database o instancia administrada como destino.

  - Si usa un contenedor de blobs de Azure Storage para almacenar el script o los archivos de instalación personalizada estándar, debe abrir el puerto *443* para el tráfico TCP saliente con *0.0.0.0/0* o el FQDN de Azure Blob Storage como destino.

  - Si necesita acceder a Azure Files, debe abrir el puerto *445* para el tráfico TCP saliente con *0.0.0.0/0* o el FQDN de Azure Files como destino.

- Si configura un punto de conexión de servicio de red virtual para los recursos de Microsoft.Azure Storage/Container Registry/Event Hubs/SQL habilitando *Microsoft.Storage*/ *Microsoft.ContainerRegistry*/*Microsoft.EventHub*/*Microsoft.Sql*, respectivamente, en la subred, todo el tráfico entre el Azure-SSIS IR y estos servicios de las mismas regiones emparejadas se enruta a la red troncal de Azure en lugar del dispositivo o servicio del firewall.

- Debe abrir el puerto *80* para el tráfico TCP saliente con los siguientes sitios de descarga de la lista de revocación de certificados (CRL) como destino:

  - *crl.microsoft.com:80*
  - *mscrl.microsoft.com:80*
  - *crl3.digicert.com:80*
  - *crl4.digicert.com:80*
  - *ocsp.digicert.com:80*
  - *cacerts.digicert.com:80*
  
  Si usa certificados con distintas CRL, también debe agregar sus sitios de descarga como destino. Para más información, consulte el artículo [Lista de revocación de certificados](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

  Si bloquea este tráfico, podría experimentar una degradación del rendimiento al iniciar el Azure-SSIS IR y perder la capacidad de comprobar las CRL al usar certificados, lo que no se recomienda desde el punto de vista de la seguridad.

Si no necesita auditar ni inspeccionar el tráfico saliente de la Azure-SSIS IR, puede usar UDR para forzar todo el tráfico con el tipo de próximo salto como *Internet*:

- Al usar Azure ExpressRoute, puede configurar una ruta UDR para *0.0.0.0/0* en la subred con el tipo de próximo salto como *Internet*. 

- Al usar una NVA, puede modificar el UDR existente para la ruta *0.0.0.0/0* en la subred para cambiar el tipo de próximo salto de *Aplicación virtual* a *Internet.*

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="Agregar una ruta":::

> [!NOTE]
> La configuración de UDR con el tipo de próximo salto como *Internet* no significa que todo el tráfico vaya a través de Internet. Siempre que la dirección de destino pertenezca a uno de los servicios de Azure, Azure enruta todo el tráfico a esa dirección a través de la red troncal de Azure en lugar de Internet.

## <a name="configure-the-relevant-resource-group"></a><a name="rg"></a>Configuración del grupo de recursos pertinente

Para habilitar la inserción de red virtual estándar, Azure-SSIS IR debe crear determinados recursos de red en el mismo grupo de recursos que la red virtual. Estos recursos incluyen:

- Una instancia de Azure Load Balancer, con el nombre _\<Guid\>-azurebatch-cloudserviceloadbalancer_.
- Una dirección IP pública de Azure llamada _\<Guid\>-azurebatch-cloudservicepublicip_.
- Un NSG, con el nombre _\<Guid\> -azurebatch-cloudservicenetworksecuritygroup_. 

> [!NOTE]
> Ahora puede traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR. En este escenario, crearemos el equilibrador de carga de Azure y el grupo de seguridad de red en el mismo grupo de recursos que las direcciones IP públicas estáticas en lugar de la red virtual.

Estos recursos se crearán cuando se inicie Azure-SSIS IR. Se eliminarán cuando esta se detenga. Si trae sus propias direcciones IP públicas estáticas de Azure-SSIS IR, no se eliminarán cuando la instancia se detenga. Para evitar que el Azure-SSIS IR se detenga, no reutilice estos recursos para otros fines.

Asegúrese de que no tiene ningún bloqueo de recursos en el grupo de recursos o la suscripción a los que pertenecen la red virtual o las direcciones IP públicas estáticas. Si configura un bloqueo de solo lectura o de eliminación, se producirá un error al iniciar o detener la instancia de Azure-SSIS IR, o esta dejará de responder.

Asegúrese de que no tiene ninguna asignación de Azure Policy que impida que se creen los siguientes recursos en el grupo de recursos o suscripción al que pertenecen la red virtual o las direcciones IP públicas estáticas: 

- *Microsoft.Network/LoadBalancers* 
- *Microsoft.Network/NetworkSecurityGroups* 
- *Microsoft.Network/PublicIPAddresses* 

Asegúrese de que la cuota de recursos de la suscripción es suficiente para estos recursos. En concreto, para cada Azure-SSIS IR creado en una red virtual, debe reservar el doble de estos recursos, ya que los recursos adicionales se usarán cuando actualicemos periódicamente el Azure-SSIS IR.

## <a name="faq"></a><a name="faq"></a>P+F

- ¿Cómo se protege la dirección IP pública expuesta en Azure-SSIS Integration Runtime para la conexión entrante? ¿Es posible quitar la dirección IP pública?
 
  En este momento se creará automáticamente una dirección IP pública cuando Azure-SSIS Integration Runtime se conecte a una red virtual. Tenemos un NSG de nivel de NIC para permitir que solo el servicio de administración de Azure Batch se conecte de entrada a su Azure-SSIS IR. También puede especificar un grupo de seguridad de red de nivel de subred para la protección de la entrada.

  Si no desea que se exponga ninguna dirección IP pública, considere la posibilidad de configurar un entorno de ejecución de integración autohospedado como proxy para [Azure-SSIS IR](self-hosted-integration-runtime-proxy-ssis.md) en lugar de conectar Azure-SSIS IR a una red virtual.
 
- ¿Puedo agregar la dirección IP pública de Azure-SSIS Integration Runtime a la lista de permitidos del firewall para los orígenes de datos?

  Ahora puede traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR. En este caso, puede agregar las direcciones IP proporcionadas a la lista de permitidos del firewall para los orígenes de datos. Como alternativa, también puede considerar otras opciones (a continuación) para proteger el acceso a los datos desde Azure-SSIS IR según su escenario:

  - Si el origen de datos es local, después de conectar una red virtual a la red local y de conectar Azure-SSIS Integration Runtime a la subred de esa red virtual, puede agregar el intervalo de direcciones IP privadas de esa subred a la lista de permitidos del firewall para el origen de datos.

  - Si el origen de datos es un servicio de Azure que admite puntos de conexión de servicio de red virtual, puede configurar uno de estos puntos en la subred de la red virtual y conectar el Azure-SSIS IR a esa subred. Después, puede agregar una regla de red virtual con esa subred al firewall para el origen de datos.

  - Si el origen de datos es un servicio en la nube que no es de Azure, puede usar una UDR para enrutar el tráfico saliente desde su Azure-SSIS IR a su dirección IP pública estática mediante un NVA o Azure Firewall. A continuación, puede agregar la dirección IP pública estática de la aplicación virtual de red o de Azure Firewall a la lista de permitidos del firewall para el origen de datos.

  - Si ninguna de las opciones anteriores cumple sus expectativas, considere la posibilidad de [configurar el entorno de ejecución de integración autohospedado como proxy para Azure-SSIS IR](self-hosted-integration-runtime-proxy-ssis.md). A continuación, puede agregar la dirección IP pública estática de la máquina que hospeda el entorno de ejecución de integración autohospedado a la lista de permitidos del firewall del origen de datos.

- ¿Por qué es necesario proporcionar dos direcciones IP públicas estáticas si deseo traer las mías propias para la instancia de Azure-SSIS IR?

  Azure SSIS IR se actualizará automáticamente de forma periódica. Los nuevos nodos se crean durante la actualización y los antiguos se eliminan. Sin embargo, para evitar tiempos de inactividad, los nodos antiguos no se eliminarán hasta que los nuevos estén preparados. Por lo tanto, la primera dirección IP pública estática utilizada por los nodos antiguos no se puede liberar inmediatamente y se necesita la segunda para crear los nuevos nodos.

- He traído mis propias direcciones IP públicas estáticas para Azure-SSIS IR, pero ¿por qué aún no puede acceder a mis orígenes de datos?

  Confirme que las dos direcciones IP públicas estáticas se agregan a la lista de permitidos del firewall para los orígenes de datos. Cada vez que se actualiza Azure-SSIS IR, su dirección IP pública estática cambia entre las dos que trajo el usuario. Si agrega solo una de ellas a la lista de permitidos, el acceso a los datos de Azure-SSIS IR se interrumpirá después de la actualización.

  Si el origen de datos es un servicio de Azure, compruebe si lo ha configurado con puntos de conexión de servicio de red virtual. En ese caso, el tráfico de Azure-SSIS IR al origen de datos cambiará para usar las direcciones IP privadas administradas por los servicios de Azure y la adición de sus propias direcciones IP públicas estáticas a la lista de permitidos del firewall para el origen de datos no tendrá efecto.

## <a name="next-steps"></a>Pasos siguientes

- [Conexión de Azure-SSIS IR a una red virtual mediante la interfaz de usuario de ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Conexión de Azure-SSIS IR a una red virtual mediante Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa el servidor de Azure SQL Database para hospedar SSISDB. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones sobre el uso de un servidor de Azure SQL Database configurado con un punto de conexión de servicio de red virtual, una regla de firewall de IP, un punto de conexión privado o Azure SQL Managed Instance que se conecta a una red virtual para hospedar SSISDB. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
