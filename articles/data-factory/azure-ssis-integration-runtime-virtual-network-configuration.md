---
title: Configuración de una red virtual de Azure para un entorno de ejecución de integración de SSIS de Azure
description: Obtenga información sobre cómo configurar una red virtual de Azure para un entorno de ejecución de integración de SSIS de Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c6e66487211ed3928f393bb028ae9f4c06e3b0eb
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403127"
---
# <a name="virtual-network-configuration-for-an-azure-ssis-integration-runtime"></a>Configuración de la red virtual para un entorno de ejecución de integración de SSIS de Azure

Configure la red virtual para que cumpla estos requisitos: 

- Asegúrese de que `Microsoft.Batch` es un proveedor registrado en la suscripción de la subred de la red virtual que hospeda Azure-SSIS Integration Runtime. Si usa una red virtual clásica, también debe conectar `MicrosoftAzureBatch` al rol Colaborador de la máquina virtual clásica de esa red virtual. 

- Asegúrese de que tiene los permisos necesarios. Para más información, consulte [Configuración de permisos](#perms).

- Seleccione la subred adecuada para hospedar la instancia de Integration Runtime para la integración de SSIS en Azure. Para más información, consulte el apartado sobre la [selección de la subred](#subnet). 

- Si trae sus propias direcciones IP públicas para su instancia de Azure-SSIS IR, consulte [Selección de las direcciones IP públicas estáticas.](#publicIP)

- Si usa su propio servidor del sistema de nombres de dominio (DNS) en la red virtual, consulte [Configuración del servidor DNS](#dns_server). 

- Si usa un grupo de seguridad de red (NSG) en la subred, consulte la sección acerca de la [configuración de un grupo de seguridad de red](#nsg). 

- Si usa Azure ExpressRoute o una ruta definida por el usuario (UDR), consulte [Uso de Azure ExpressRoute o una ruta definida por el usuario](#route). 

- Asegúrese de que el grupo de recursos de la red virtual (o el grupo de recursos de las direcciones IP públicas, si trae las suyas propias) puede crear y eliminar determinados recursos de red de Azure. Para más información, consulte [Configuración del grupo de recursos](#resource-group). 

- Si personaliza la instancia de Azure-SSIS Integration Runtime como se describe en [Personalización de la instalación en una instancia de Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md), el proceso interno que seguimos para administrar sus nodos consumirá direcciones IP privadas de un intervalo predefinido que va de 172.16.0.0 a 172.31.255.255. Por tanto, asegúrese de que los intervalos de direcciones IP privadas de las redes virtuales o locales no entran en conflicto con este intervalo.

Este diagrama muestra las conexiones necesarias para Azure-SSIS Integration Runtime:

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Diagrama que muestra las conexiones necesarias para Azure-SSIS IR.":::

## <a name="set-up-permissions"></a><a name="perms"></a> Configuración de permisos

El usuario que cree Azure-SSIS Integration Runtime debe tener los permisos siguientes:

- Si va a unir SSIS IR a una red virtual de Azure Resource Manager, tiene dos opciones:

  - Use el rol Colaborador de red integrado. Este rol requiere el permiso _Microsoft.Network /\*_ , que tiene un ámbito mucho mayor del necesario.

  - Crear un rol personalizado que incluya solo el permiso _Microsoft.Network/virtualNetworks/\*/join/action_ necesario. Si también quiere traer sus propias direcciones IP públicas de Azure-SSIS IR y conectar este a una red virtual de Azure Resource Manager, incluya también el permiso _Microsoft.Network/publicIPAddresses/*/join/action_ en el rol.

- Si va a conectar SSIS IR a una red virtual clásica, se recomienda usar el rol Colaborador de máquina virtual clásica integrado. En caso contrario, tendrá que definir un rol personalizado que incluya el permiso para unirse a la red virtual.

## <a name="select-the-subnet"></a><a name="subnet"></a> Seleccionar la subred

Cuando elija una subred: 

- No seleccione GatewaySubnet para implementar Azure-SSIS Integration Runtime. Está dedicado a las puertas de enlace de red virtual. 

- Asegúrese de que la subred que seleccione tenga suficiente espacio de direcciones disponible para usar Azure-SSIS IR. Deje direcciones IP disponibles para al menos dos veces el número de nodos del entorno de ejecución de integración. Azure reserva algunas direcciones IP dentro de cada subred. Estas direcciones no se pueden usar. La primera y la última direcciones IP de las subredes están reservadas para la conformidad con el protocolo y las otras tres se usan para los servicios de Azure. Para más información, consulte [¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- No use una subred que esté ocupada exclusivamente por otros servicios de Azure (por ejemplo, una instancia administrada de SQL Database, App Service, etc.). 

## <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Selección de direcciones IP públicas estáticas

Si desea traer sus propias direcciones IP públicas estáticas para Azure-SSIS IR y conectar este a una red virtual, asegúrese de que las direcciones cumplen los requisitos siguientes:

- Se deben proporcionar exactamente dos sin usar que no estén ya asociadas a otros recursos de Azure. Se usará la adicional en las actualizaciones periódicas de Azure-SSIS IR. Tenga en cuenta que no se puede compartir una dirección IP pública entre sus instancias de Azure-SSIS IR.

- Ambas deben ser estáticas de tipo estándar. Consulte [SKU de dirección IP pública](../virtual-network/public-ip-addresses.md#sku) para más detalles.

- Las dos deben tener un nombre DNS. Si no ha proporcionado un nombre DNS al crearlas, puede hacerlo en Azure Portal.

:::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Integration Runtime de SSIS de Azure":::

- Estas y la red virtual deben estar en la misma suscripción y en la misma región.

## <a name="set-up-the-dns-server"></a><a name="dns_server"></a> Configuración del servidor DNS 
Si tiene que usar su propio servidor DNS en una red virtual a la que se ha conectado Azure-SSIS Integration Runtime para resolver su nombre de host privado, asegúrese de que puede resolver también nombres de host globales de Azure (por ejemplo, un blob de Azure Storage llamado `<your storage account>.blob.core.windows.net`). 

Un enfoque recomendado es el siguiente: 

-   Configure el DNS personalizado para que reenvié las solicitudes a Azure DNS. Los registros DNS no resueltos se pueden reenviar a la dirección IP de resoluciones recursivas de Azure (168.63.129.16) de su propio servidor DNS. 

Para más información, consulte [Resolución de nombres con su propio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Use un nombre de dominio completo (FQDN) para el nombre de host privado (por ejemplo, use `<your_private_server>.contoso.com` en lugar de `<your_private_server>`). Como alternativa, puede utilizar una configuración personalizada estándar en Azure-SSIS IR para anexar automáticamente su propio sufijo DNS (por ejemplo `contoso.com`) a un nombre de dominio de una sola etiqueta no calificado y convertirlo en un FQDN antes de usarlo en consultas DNS (consulte [Ejemplos de instalación personalizada estándar](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples)). 

## <a name="set-up-an-nsg"></a><a name="nsg"></a> Configuración de un grupo de seguridad de red
Si tiene que implementar grupos de seguridad de red para la subred que usa Azure-SSIS Integration Runtime, permita que el tráfico entrante y saliente atraviese los siguientes puertos: 

-   **Requisito de entrada de Azure-SSIS Integration Runtime**

| Dirección | Protocolo de transporte | Source | Intervalo de puertos de origen | Destination | Destination port range | Comentarios |
|---|---|---|---|---|---|---|
| Entrada | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (si conecta el entorno de ejecución de integración a una red virtual de Resource Manager) <br/><br/>10100, 20100, 30100 (si une la instancia de Integration Runtime a una red virtual clásica)| El servicio Data Factory usa estos puertos para comunicarse con los nodos de Azure-SSIS Integration Runtime en la red virtual. <br/><br/> Tanto si crea un grupo de seguridad de red en el nivel de subred como si no lo hace, Data Factory siempre configura uno en el nivel de las tarjetas de interfaz de red (NIC) conectadas a las máquinas virtuales que hospedan Azure-SSIS Integration Runtime. Se permite solo el tráfico entrante desde direcciones IP de Data Factory en los puertos especificados por ese grupo de seguridad de red a nivel de NIC. Aunque si se abren estos puertos al tráfico de Internet en el nivel de la subred, el tráfico de las direcciones IP que no sean de Data Factory se bloquea en el nivel de las tarjetas de interfaz de red. |
| Entrada | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Opcional). Esta regla solo es necesaria cuando alguien en el servicio de soporte técnico de Microsoft pide al cliente que abra para realizar una solución de problemas avanzada, y se puede cerrar inmediatamente después de solucionar el problema. La etiqueta de servicio **CorpNetSaw** permite que únicamente las estaciones de trabajo de acceso seguro de la red corporativa de Microsoft usen el Escritorio remoto. Esta etiqueta de servicio no se puede seleccionar desde el portal, y solo está disponible a través de Azure PowerShell o la CLI de Azure. <br/><br/> En el nivel de NIC del NSG, el puerto 3389 está abierto de forma predeterminada, y le permite controlar el puerto 3389 en el nivel de subred del NSG, mientras tanto, por motivos de protección, Azure-SSIS Integration Runtime ha deshabilitado el puerto 3389 de salida de forma predeterminada en la regla de Firewall de Windows en cada nodo del entorno de ejecución de integración. |
||||||||

-   **Requisito de salida de Azure-SSIS Integration Runtime**

| Dirección | Protocolo de transporte | Source | Intervalo de puertos de origen | Destination | Destination port range | Comentarios |
|---|---|---|---|---|---|---|
| Salida | TCP | VirtualNetwork | * | AzureCloud | 443 | Los nodos de Azure-SSIS Integration Runtime en la red virtual usan este puerto para acceder a servicios de Azure, como Azure Storage y Azure Event Hubs. |
| Salida | TCP | VirtualNetwork | * | Internet | 80 | (Opcional). Los nodos de Azure-SSIS Integration Runtime en la red virtual usan este puerto para descargar una lista de revocación de certificados de Internet. Si bloquea este tráfico, puede experimentar una degradación del rendimiento al iniciar el entorno de ejecución de integración y perder capacidad para comprobar el uso de certificados en la lista de revocación de certificados. Si desea restringir aún más el destino a determinados FQDN, consulte la sección **Uso de Azure ExpressRoute o de una ruta definida por el usuario**|
| Salida | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | (Opcional). Esta regla es necesaria solamente cuando los nodos de Azure-SSIS Integration Runtime en la red virtual acceden a una base de datos de SSISDB hospedada en el servidor. Si la directiva de conexión del servidor está establecida en **Proxy**, en lugar de **Redirigir**, solo se necesita el puerto 1433. <br/><br/> Esta regla de seguridad de salida no es aplicable a las bases de datos de SSISDB hospedadas por su Instancia administrada de SQL en la red virtual o a una instancia de SQL Database configurada con un punto de conexión privado. |
| Salida | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Opcional). Esta regla solo es necesaria cuando los nodos de Azure-SSIS Integration Runtime en la red virtual acceden a una base de datos de SSISDB hospedada por la Instancia administrada de SQL en la red virtual o una instancia de SQL Database configurada con un punto de conexión privado. Si la directiva de conexión del servidor está establecida en **Proxy**, en lugar de **Redirigir**, solo se necesita el puerto 1433. |
| Salida | TCP | VirtualNetwork | * | Storage | 445 | (Opcional). Esta regla solo es necesaria cuando quiera ejecutar un paquete SSIS almacenado en Azure Files. |
||||||||

## <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Uso de Azure ExpressRoute o de una ruta definida por el usuario
Si desea inspeccionar el tráfico saliente desde Azure-SSIS Integration Runtime, puede enrutar el tráfico iniciado desde allí al dispositivo de firewall local mediante la tunelización forzada de [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) (con anuncio de una ruta BGP, 0.0.0.0/0, a la red virtual), o a una aplicación virtual de red (NVA) como firewall, o a [Azure Firewall](../firewall/index.yml) a través de las [rutas definidas por el usuario (UDR)](../virtual-network/virtual-networks-udr-overview.md). 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="Escenario de NVA para Azure-SSIS Integration Runtime":::

Tiene que hacer lo siguiente para que el escenario completo funcione
   -   No puede enrutar el tráfico entrante entre los servicios de administración de Azure Batch y Azure-SSIS Integration Runtime a través del dispositivo de firewall.
   -   El dispositivo de firewall debe permitir el tráfico saliente que requiera Azure-SSIS Integration Runtime.

No puede enrutar el tráfico entrante entre los servicios de administración de Azure Batch y Azure-SSIS Integration Runtime al dispositivo de firewall, de lo contrario, el tráfico se interrumpirá debido un problema de enrutamiento asimétrico. Tiene que definir las rutas para el tráfico entrante de modo que el tráfico pueda responder por la misma vía por la que entró. Puede definir las UDR específicas para que enruten el tráfico entre servicios de administración de Azure Batch y Azure-SSIS Integration Runtime con el tipo de próximo salto como **Internet**.

Por ejemplo, si Azure-SSIS Integration Runtime se encuentra en `UK South` y desea inspeccionar el tráfico de salida mediante Azure Firewall, primero tendría que obtener una lista de intervalos IP de la etiqueta de servicio `BatchNodeManagement.UKSouth` desde el [vínculo de descarga de intervalos IP y etiquetas de servicio](https://www.microsoft.com/download/details.aspx?id=56519), o mediante [Service Tag Discovery API](../virtual-network/service-tags-overview.md#service-tags-on-premises). A continuación, aplique las siguientes UDR de rutas de intervalo IP relacionadas con el tipo de próximo salto como **Internet**, junto con la ruta 0.0.0.0/0 con el tipo de salto siguiente como **aplicación virtual**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Configuración de UDR de Azure Batch":::

> [!NOTE]
> Este enfoque conlleva un costo de mantenimiento adicional. Compruebe periódicamente el intervalo de direcciones IP y agregue nuevos intervalos de direcciones IP a la ruta definida por el usuario UDR para evitar romper Azure-SSIS Integration Runtime. Se recomienda comprobar el intervalo IP mensualmente porque cuando la nueva dirección IP aparece en la etiqueta de servicio, la dirección IP tardará otro mes en surtir efecto. 

Para facilitar la configuración de reglas de UDR, puede ejecutar el siguiente script de PowerShell para agregar reglas de UDR para servicios de administración de Azure Batch:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Para que el dispositivo de firewall permita el tráfico saliente, tiene que permitir que la salida a los puertos a continuación sea la misma que los requisitos de las reglas de salida del NSG.
-   Puerto 443 con destino como Azure Cloud Services.

    Si usa Azure Firewall, puede especificar una regla de red con la etiqueta de servicio AzureCloud. Si el firewall pertenece a los otros tipos, puede simplemente permitir el destino como "todo" para el puerto 443 o permitir "por debajo de" los FQDN en función del tipo de entorno de Azure:

    | Entorno de Azure | Puntos de conexión                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li><b>Azure Data Factory (administración)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (administración)</b><ul><li>\*blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure Container Registry (instalación personalizada)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>Centro de eventos (registro)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Servicio de registro de Microsoft (uso interno)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (administración)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (administración)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (instalación personalizada)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>Centro de eventos (registro)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Servicio de registro de Microsoft (uso interno)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (administración)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (administración)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (instalación personalizada)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>Centro de eventos (registro)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Servicio de registro de Microsoft (uso interno)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Con respecto a los FQDN de Azure Storage, Azure Container Registry y Event Hub, también puede elegir habilitar los siguientes puntos de conexión de servicio para la red virtual de modo que el tráfico de red a estos puntos de conexión pase por la red troncal de Azure en lugar de enrutarse al dispositivo de firewall:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Puerto 80 con destino como sitios de descarga de CRL.

    Debe permitir los siguientes FQDN que se usan como sitios de descarga de certificados de CRL (lista de revocación de certificados) para fines de administración de Azure-SSIS Integration Runtime:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Si usa certificados con una CRL diferente, se recomienda incluirlos también. Puede leer esto para encontrar una información más detallada sobre la [lista de revocación de certificados](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Si no permite este tráfico, puede experimentar una degradación del rendimiento al iniciar Azure-SSIS Integration Runtime y perder capacidad para comprobar el uso de certificados en la lista de revocación de certificados, lo que se desaconseja por razones de seguridad.

-   Puerto 1433, 11000-11999 con destino como Azure SQL Database (necesario solamente cuando los nodos de Azure-SSIS Integration Runtime en la red virtual acceden a una base de datos de SSISDB hospedada en el servidor).

    Si usa Azure Firewall, puede especificar una regla de red con la etiqueta de servicio de Azure SQL; de lo contrario, podría permitir el destino como especifico de la dirección URL de Azure SQL en el dispositivo de firewall.

-   Puerto 445 con destino como Azure Storage (solo es necesario cuando se ejecuta el paquete SSIS almacenado en Azure Files).

    Si usa Azure Firewall, puede especificar una regla de red con la etiqueta de servicio Storage; de lo contrario, puede permitir el destino como dirección URL del recurso compartido de archivos de Azure especifica en el dispositivo de firewall.

> [!NOTE]
> Para Azure SQL y Storage, si configura los puntos de conexión de servicio de red virtual en la subred, el tráfico entre Azure-SSIS Integration Runtime y Azure SQL en la misma región o Azure Storage en la misma región o región emparejada, se enrutará directamente a la red troncal de Microsoft Azure en lugar de al dispositivo de firewall.

Si no necesita la funcionalidad de inspección del tráfico saliente de Azure-SSIS Integration Runtime, puede aplicar simplemente la ruta para forzar todo el tráfico al tipo de próximo salto como **Internet**:

-   En un escenario de Azure ExpressRoute puede aplicar una ruta 0.0.0.0/0 con el tipo de próximo salto como **Internet** en la subred que hospeda Azure-SSIS Integration Runtime. 
-   En un escenario de NVA, puede modificar la ruta 0.0.0.0/0 existente aplicada en la subred que hospeda a Azure-SSIS Integration Runtime de tipo de próximo salto como **aplicación virtual** a tipo de próximo salto como **Internet**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="Agregar una ruta":::

> [!NOTE]
> La especificación de la ruta con el tipo de próximo salto como **Internet** no significa que todo el tráfico pasará por Internet. Mientras la dirección de destino sea para uno de los servicios de Azure, Azure enruta el tráfico directamente al servicio a través de la red troncal de Azure, en lugar de enrutarlo a Internet.

## <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Configuración del grupo de recursos

La instancia de Integration Runtime para la integración de SSIS en Azure necesita crear determinados recursos de red en el mismo grupo de recursos de la red virtual. Estos recursos incluyen:
- Una instancia de Azure Load Balancer, con el nombre *\<Guid>-azurebatch-cloudserviceloadbalancer*.
- Una dirección IP pública de Azure llamada *\<Guid>-azurebatch-cloudservicepublicip*.
- Un grupo de seguridad de red llamado *\<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Ahora puede traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR. En este escenario solo se creará el equilibrador de carga de Azure y el grupo de seguridad de red en el mismo grupo de recursos que las direcciones IP públicas estáticas, en lugar de la red virtual.

Estos recursos se crearán al iniciarse la instancia de Azure-SSIS IR. Se eliminarán cuando esta se detenga. Si trae sus propias direcciones IP públicas estáticas de Azure-SSIS IR, no se eliminarán cuando la instancia se detenga. Para evitar que se bloquee la detención en la instancia de Azure-SSIS IR, no vuelva a usar estos recursos de red en los demás recursos.

Asegúrese de que no tiene ningún bloqueo de recursos en el grupo de recursos ni en la suscripción a la que la red virtual o las direcciones IP públicas estáticas pertenecen. Si configura un bloqueo de solo lectura o de eliminación, se producirá un error al iniciar o detener la instancia de Azure-SSIS IR, o esta dejará de responder.

Asegúrese de que no tiene ninguna asignación de directiva de Azure que impida que se creen los siguientes recursos en el grupo de recursos o en la suscripción a la que pertenecen la red virtual o las direcciones IP públicas estáticas: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

Asegúrese de que la cuota de recursos de la suscripción sea suficiente para los tres recursos de red anteriores. En concreto, para cada instancia de Azure-SSIS IR creada en la red virtual, debe reservar dos cuotas gratuitas para cada uno de los tres recursos de red anteriores. Se usará la cuota adicional cuando se actualice periódicamente la instancia de Azure-SSIS IR.

## <a name="faq"></a><a name="faq"></a> Preguntas más frecuentes

- ¿Cómo se protege la dirección IP pública expuesta en Azure-SSIS Integration Runtime para la conexión entrante? ¿Es posible quitar la dirección IP pública?
 
  En este momento se creará automáticamente una dirección IP pública cuando Azure-SSIS Integration Runtime se conecte a una red virtual. Tenemos un grupo de seguridad de red en el nivel de NIC para permitir que solo los servicios de administración de Azure Batch se conecten de forma entrante a Azure-SSIS Integration Runtime. También puede especificar un grupo de seguridad de red de nivel de subred para la protección de la entrada.

  Si no desea que se expongan direcciones IP públicas, considere la posibilidad de [configurar el entorno de ejecución de integración autohospedado como proxy para Azure-SSIS Integration Runtime](./self-hosted-integration-runtime-proxy-ssis.md) en lugar de conectar la instancia a una red virtual, si es su escenario.
 
- ¿Puedo agregar la dirección IP pública de Azure-SSIS Integration Runtime a la lista de permitidos del firewall para los orígenes de datos?

  Ahora puede traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR. En este caso, puede agregar las direcciones IP proporcionadas a la lista de permitidos del firewall para los orígenes de datos. También puede tener en cuenta otras opciones (a continuación) para proteger el acceso a los datos desde Azure-SSIS IR en función de su escenario:

  - Si el origen de datos es local, después de conectar una red virtual a la red local y de conectar Azure-SSIS Integration Runtime a la subred de esa red virtual, puede agregar el intervalo de direcciones IP privadas de esa subred a la lista de permitidos del firewall para el origen de datos.
  - Si el origen de datos es un servicio de Azure compatible con los puntos de conexión de servicio de red virtual, puede configurar uno de estos puntos en la subred de la red virtual y conectar Azure-SSIS Integration Runtime a esa subred. Después, puede agregar una regla de red virtual con esa subred al firewall para el origen de datos.
  - Si el origen de datos es un servicio en la nube que no es de Azure, puede usar una ruta definida por el usuario para enrutar el tráfico de salida de Azure-SSIS IR a una aplicación virtual de red o a Azure Firewall mediante una dirección IP pública estática. A continuación, puede agregar la dirección IP pública estática de la aplicación virtual de red o de Azure Firewall a la lista de permitidos del firewall para el origen de datos.
  - Si ninguna de las opciones anteriores cumple sus expectativas, considere la posibilidad de [configurar el entorno de ejecución de integración autohospedado como proxy para Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). A continuación, puede agregar la dirección IP pública estática de la máquina que hospeda el entorno de ejecución de integración autohospedado a la lista de permitidos del firewall del origen de datos.

- ¿Por qué es necesario proporcionar dos direcciones IP públicas estáticas si deseo traer las mías propias para la instancia de Azure-SSIS IR?

  Azure SSIS IR se actualizará automáticamente de forma periódica. Los nuevos nodos se crean durante la actualización y los antiguos se eliminan. Sin embargo, para evitar tiempos de inactividad, los nodos antiguos no se eliminarán hasta que los nuevos estén preparados. Por lo tanto, la primera dirección IP pública estática utilizada por los nodos antiguos no se puede liberar inmediatamente y se necesita la segunda para crear los nuevos nodos.

- He traído mis propias direcciones IP públicas estáticas de Azure-SSIS IR, pero ¿por qué sigo sin poder acceder a mis orígenes de datos?

  - Confirme que las dos direcciones IP públicas estáticas se han agregado a la lista de permitidos del firewall para los orígenes de datos. Cada vez que se actualiza Azure-SSIS IR, se cambia la dirección IP pública estática entre las dos que trajo el usuario. Si agrega solo una de ellas a la lista de permitidos, el acceso a los datos de Azure-SSIS IR se interrumpirá después de la actualización.
  - Si el origen de datos es un servicio de Azure, compruebe que se ha configurado con puntos de conexión de servicio de red virtual. En ese caso, el tráfico desde Azure-SSIS IR a su origen de datos cambiará y usará las direcciones IP privadas administradas por los servicios de Azure, y la incorporación de sus direcciones IP públicas estáticas propias a la lista de permitidos del firewall para el origen de datos no surtirá efecto.

## <a name="next-steps"></a>Pasos siguientes
- [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual: información general](join-azure-ssis-integration-runtime-virtual-network.md)
- [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual con la interfaz de usuario de Azure Data Factory Studio](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual con Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](./tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa Azure SQL Database para hospedar el catálogo de SSIS. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones acerca del uso de Azure SQL Database con puntos de conexión de servicio de red virtual o una Instancia administrada de SQL en una red virtual para hospedar el catálogo SSIS. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo obtener información acerca de Azure-SSIS IR. Proporciona descripciones del estado de la información devuelta. 
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Integration Runtime para la integración de SSIS en Azure mediante la adición de nodos.

