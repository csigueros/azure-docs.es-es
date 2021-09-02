---
title: Creación, modificación o eliminación del prefijo de una dirección IP pública de Azure
titlesuffix: Azure Virtual Network
description: Obtenga información sobre los prefijos de las direcciones IP públicas y cómo crearlos, modificarlos o eliminarlos.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 085bc186b8a2290919820e35ff510346f9ab3bfc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438281"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Creación, modificación o eliminación del prefijo de una dirección IP pública

Obtenga información sobre un prefijo de dirección IP pública. Un prefijo de dirección IP pública es un rango continuo de direcciones IP públicas de SKU estándar.  Al crear un recurso de dirección IP pública, puede asignar una dirección IP pública estática a partir del prefijo y asociar la dirección a los recursos. Para más información, consulte [Prefijo de dirección IP pública](public-ip-address-prefix.md).

## <a name="create-a-public-ip-address-prefix"></a>Creación del prefijo de una dirección IP pública

En la sección siguiente se detallan los parámetros a la hora de crear un prefijo de dirección IP pública.

   |Configuración|¿Necesario?|Detalles|
   |---|---|---|
   |Subscription|Sí|Debe existir en la misma [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que el recurso al cual desee asociar la dirección IP pública.|
   |Resource group|Sí|Puede existir en la misma suscripción que el [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) al cual desee asociar la dirección IP pública o en otra diferente.|
   |Nombre|Sí|El nombre debe ser único dentro del grupo de recursos que seleccione.|
   |Region|Sí|Debe existir en la misma [región](https://azure.microsoft.com/regions) que las direcciones IP públicas que asignará a direcciones del intervalo.|
   |Versión de la dirección IP|Sí| Versión de IP del prefijo (v4 o v6).
   |Tamaño del prefijo|Sí| El tamaño del prefijo que necesita. El valor predeterminado es un intervalo con 16 direcciones IP (/28 para v4 o /124 para v6).

Como alternativa, puede usar los comandos siguientes de la CLI y de PowerShell para crear un prefijo de dirección IP pública.

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>En las regiones con zonas de disponibilidad, puede usar comandos de PowerShell o de la CLI para crear un prefijo de dirección IP pública como no zonal, asociada a una zona específica o para usar la redundancia de zona.  En la versión de API 2020-08-01 o posterior, si no se proporciona un parámetro de zona, se crea un prefijo de dirección IP pública no zonal. En el caso de las versiones de la API anteriores a 2020-08-01, se crea un prefijo de dirección IP pública con redundancia de zona. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Creación de una dirección IP pública estática a partir de un prefijo

En la sección siguiente se detallan los parámetros necesarios para crear una dirección IP pública estática a partir de un prefijo.

   |Configuración|¿Necesario?|Detalles|
   |---|---|---|
   |Nombre|Sí|El nombre de la dirección IP pública debe ser único dentro del grupo de recursos que seleccione.|
   |Tiempo de espera de inactividad (minutos)|No|Cantidad de minutos para mantener una conexión TCP o HTTP abierta sin que dependa del envío de mensajes de mantenimiento de los clientes. |
   |Etiqueta de nombre DNS|No|Debe ser único dentro de la región de Azure en la que cree el nombre (entre todas las suscripciones y todos los clientes). Azure registra automáticamente el nombre y la dirección IP en el DNS para que pueda conectarse a un recurso con el nombre. Azure anexará una subred predeterminada llamada *location.cloudapp.azure.com* al nombre que proporcione para crear el nombre DNS completo. Para obtener más información, vea los detalles relativos al [uso de Azure DNS con una dirección IP pública de Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

También puede usar los comandos siguientes de la CLI y de PowerShell con los parámetros **--public-ip-prefix (CLI)** y **-PublicIpPrefix (PowerShell)** , para crear un recurso de dirección IP pública a partir de un prefijo. 

|Herramienta|Get-Help|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

>[!NOTE]
>Solo se pueden asignar direcciones IP públicas estáticas del intervalo del prefijo creadas con la SKU Estándar. Para más información sobre las SKU de direcciones IP públicas, consulte [Direcciones IP publicas](./public-ip-addresses.md#public-ip-addresses).

## <a name="view-or-delete-a-prefix"></a>Ver o eliminar un prefijo

Para ver o eliminar un prefijo, se pueden usar los siguientes comandos en la CLI de Azure y Azure PowerShell.

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) para enumerar las direcciones IP públicas.<br>[az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) para mostrar la configuración.<br> [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) para actualizar.<br>[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) para eliminar.|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) para recuperar un objeto de dirección IP pública y ver su configuración.<br>[Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) para actualizar la configuración.<br> [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) para eliminar.|

## <a name="permissions"></a>Permisos

Para obtener los permisos para administrar prefijos de direcciones IP públicas, su cuenta debe asignarse al rol [Colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un rol [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

| Acción                                                            | Nombre                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Leer el prefijo de una dirección IP pública                                |
| Microsoft.Network/publicIPPrefixes/write                          | Crear o actualizar el prefijo de una dirección IP pública                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Eliminación del prefijo de una dirección IP pública                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Creación de una dirección IP pública a partir de un prefijo |

## <a name="next-steps"></a>Pasos siguientes

- Obtener información sobre los escenarios y las ventajas de usar un [prefijo de IP pública](public-ip-address-prefix.md)
