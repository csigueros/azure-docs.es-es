---
title: Configuración de HSM administrado de Azure Key Vault con puntos de conexión privados
description: Aprenda a integrar HSM administrado de Azure Key Vault con el servicio Azure Private Link
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/21/2021
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: d9dff0d6d9d8421e160c19c60efc9e871d7867ed
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443580"
---
# <a name="integrate-managed-hsm-with-azure-private-link"></a>Integración de HSM administrado con Azure Private Link

El servicio Azure Private Link permite acceder a los servicios de Azure (por ejemplo, HSM administrado, Azure Storage, Azure Cosmos DB, etc.) y a los servicios de asociados o clientes hospedados de Azure mediante un punto de conexión privado de la red virtual.

Un punto de conexión privado de Azure es una interfaz de red que le conecta de forma privada y segura a un servicio con la tecnología de Azure Private Link. El punto de conexión privado usa una dirección IP privada de la red virtual para incorporar el servicio de manera eficaz a su red virtual. Todo el tráfico dirigido al servicio se puede enrutar mediante el punto de conexión privado, por lo que no se necesita ninguna puerta de enlace, dispositivos NAT, conexiones de ExpressRoute o VPN ni direcciones IP públicas. El tráfico entre la red virtual y el servicio atraviesa la red troncal de Microsoft, eliminando la exposición a la red pública de Internet. Puede conectarse a una instancia de un recurso de Azure, lo que le otorga el nivel más alto de granularidad en el control de acceso.

Para más información, consulte [¿Qué es Azure Private Link?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Requisitos previos

Para integrar un HSM administrado con Azure Private Link, necesitará lo siguiente:

- Un HSM administrado. Para más información, consulte [Aprovisionamiento y activación de un HSM administrado mediante la CLI de Azure](quick-create-cli.md).
- Una red virtual de Azure.
- Una subred en la red virtual.
- Permisos de propietario o colaborador tanto para el HSM administrado y la red virtual.
- La CLI de Azure, versión 2.25.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

El punto de conexión privado y la red virtual deben estar en la misma región. Al seleccionar una región para el punto de conexión privado mediante el portal, solo se filtran automáticamente las redes virtuales que se encuentran en dicha región. El HSM puede estar en una otra región.

El punto de conexión privado usa una dirección IP privada en la red virtual.


## <a name="establish-a-private-link-connection-to-managed-hsm-using-cli-initial-setup"></a>Establecimiento de una conexión de vínculo privado con HSM administrado mediante la CLI (configuración inicial)

```azurecli
az login                                                                   # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                            # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                            # Create a new Resource Group
az provider register -n Microsoft.KeyVault                                 # Register KeyVault as a provider
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny # Turn on firewall

az network vnet create -g {RG} -n {vNet NAME} --location {REGION}           # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.managedhsm.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.managedhsm.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="allow-trusted-services-to-access-managed-hsm"></a>Permitir que los servicios de confianza accedan a Managed HSM

Cuando el firewall está activado, se denegará todo el acceso al HSM desde cualquier ubicación que no use una conexión de puntos de conexión privados, lo que incluye los servicios de Azure e Internet público. Use la opción `--baypss AzureServices` si desea que los servicios de Microsoft puedan acceder a las claves en HSM administrado. Las entidades individuales (como una cuenta de Azure Storage o una instancia de Azure SQL Server) deben tener asignaciones de roles concretas para poder acceder a una clave. 

> [!NOTE]
> Solo se admiten escenarios de uso de servicios de confianza concretos. Para más información, consulte la [lista de escenario de uso de servicios de confianza](../general/overview-vnet-service-endpoints.md#trusted-services).

```azurecli
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny --bypass AzureServices
```

### <a name="create-a-private-endpoint-automatically-approve"></a>Creación de un punto de conexión privado (aprobación automática) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

> [!NOTE]
> Si elimina este HSM, el punto de conexión privado dejará de funcionar. Si recupera (recupera) este HSM posteriormente, debe volver a crear un punto de conexión privado.

### <a name="create-a-private-endpoint-manually-request-approval"></a>Crear un punto de conexión privado (solicitud manual de aprobación) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Administración de conexiones de vínculo privado

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --hsm-name {HSM NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>Adición de registros DNS privados
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.managedhsm.azure.net" -n {HSM NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.managedhsm.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {HSM NAME}.managedhsm.azure.net
nslookup {HSM NAME}.privatelink.managedhsm.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>Validación de que la conexión de vínculo privado funciona

Debe validar que los recursos de la misma subred del recurso de punto de conexión privado se conectan al HSM mediante una dirección IP privada y que tienen la integración correcta de la zona DNS privada.

En primer lugar, cree una máquina virtual siguiendo los pasos que encontrará en [Creación de una máquina virtual Windows en Azure Portal](../../virtual-machines/windows/quick-create-portal.md).

Haga clic en la pestaña "Redes".

1. Especifique una red virtual y una subred. Puede crear una red virtual o seleccionar una existente. Si selecciona una existente, asegúrese de que la región coincide.
1. Especifique un recurso de dirección IP pública.
1. En "Grupo de seguridad de red de NIC", seleccione "Ninguno".
1. En "Equilibrio de carga", seleccione "No".

Abra el símbolo del sistema y ejecute el siguiente comando:

```console
nslookup <your-HSM-name>.managedhsm.azure.net
```

Si ejecuta el comando nslookup para resolver la dirección IP de un HSM administrado sobre un punto de conexión público, verá un resultado similar al siguiente:

```console
c:\ >nslookup <your-hsm-name>.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-hsm-name>.managedhsm.azure.net
```

Si ejecuta el comando nslookup para resolver la dirección IP de un HSM administrado sobre un punto de conexión privado, verá un resultado similar al siguiente:

```console
c:\ >nslookup your_hsm_name.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-hsm-name>.managed.azure.net
          <your-hsm-name>.privatelink.managedhsm.azure.net
```

## <a name="troubleshooting-guide"></a>Guía de solución de problemas

* Asegúrese de que el punto de conexión privado esté en estado aprobado. 
    1. Use el subcomando ```az keyvault private-endpoint-connections show``` para ver el estado de una conexión de punto de conexión privado.
    2. Asegúrese de que el estado de la conexión sea Aprobado y de que el estado de aprovisionamiento sea Correcto. 
    3. Asegúrese de que la red virtual coincide con la que usa.

* Asegúrese de que tiene un recurso Zona DNS privada. 
    1. Debe tener un recurso de zona DNS privada cuyo nombre sea exactamente privatelink.managedhsm.azure.net. 
    2. Para aprender a configurarlo, consulte el siguiente vínculo. [Zonas DNS privadas](../../dns/private-dns-privatednszone.md)
    
* Asegúrese de que la zona DNS privada está vinculada a la red virtual. Si se devuelve la dirección IP pública, es posible que el problema sea este. 
    1. Si la zona DNS privada no está vinculada a la red virtual, la consulta de DNS cuyo origen era la red virtual devolverá la dirección IP pública del HSM. 
    2. Vaya al recurso Zona DNS privada de Azure Portal y haga clic en la opción de los vínculos de la red virtual. 
    4. La red virtual que realizará las llamadas al HSM debe aparecer en la lista. 
    5. Si no está, agréguela. 
    6. Para ver los pasos detallados, consulte el siguiente documento [Vínculo de una red virtual a una zona DNS privada](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Asegúrese de que en la zona DNS privada no falta un registro D del HSM. 
    1. Vaya a la página Zona DNS privada. 
    2. Haga clic en Información general y compruebe si hay un registro D con el nombre simple de su HSM. No especifique ningún sufijo.
    3. Asegúrese de revisar la ortografía y de crear o reparar el registro D. Puede usar un TTL de 3600 (1 hora). 
    4. Asegúrese de especificar la dirección IP privada correcta. 
    
* Asegúrese de que el registro D tiene la dirección IP correcta. 
    1. Para confirmar la dirección IP, abra el recurso Punto de conexión privado en Azure Portal.
    2. Vaya al recurso Microsoft.Network/privateEndpoints en Azure Portal.
    3. En la página de información general, busque Interfaz de red y haga clic en ese vínculo. 
    4. El vínculo mostrará la información general del recurso NIC, que contiene la dirección IP privada de la propiedad. 
    5. Compruebe que se trata de la dirección IP correcta que se especifica en el registro D.

## <a name="limitations-and-design-considerations"></a>Limitaciones y consideraciones de diseño

> [!NOTE]
> El número de HSM administrado con puntos de conexión privados habilitados por suscripción es un límite ajustable. El límite que se muestra a continuación es el límite predeterminado. Si desea solicitar un aumento del límite para su suscripción, cree una incidencia de soporte técnico de Azure. Estas solicitudes se aprobarán caso por caso.

**Precios**: Para más información sobre los precios, consulte [Precios de Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Número máximo de puntos de conexión privados por HSM administrado**: 64.

**Número predeterminado de HSM administrado con puntos de conexión privados por suscripción**: 400.

Para más información, consulte [Servicio Azure Private Link: Limitaciones](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Azure Private Link](../../private-link/private-link-service-overview.md).
- Más información sobre [HSM administrado](overview.md).
