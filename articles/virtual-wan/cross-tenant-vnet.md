---
title: 'Conexión de redes virtuales entre inquilinos a un centro de conectividad: PowerShell'
titleSuffix: Azure Virtual WAN
description: Este artículo ayuda a conectar redes virtuales entre inquilinos a un centro de conectividad virtual mediante PowerShell.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: dc065e68d513850f9e006371861d087cccc6e155
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669860"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Conexión de redes virtuales entre inquilinos a un centro de conectividad de Virtual Wan

Este artículo ayuda a usar Virtual WAN para conectar una red virtual a un centro de conectividad virtual de otro inquilino. Esta arquitectura resulta útil cuando se tienen cargas de trabajo de cliente que deben conectarse para estar en la misma red, pero que se encuentran en distintos inquilinos. Por ejemplo, como se muestra en el diagrama siguiente, se puede conectar una red virtual que no es de Contoso (el inquilino remoto) a un centro de conectividad virtual de Contoso (el inquilino primario).

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Definir la configuración de enrutamiento" :::

En este artículo aprenderá a:

* Agregar otro inquilino como Colaborador a la suscripción de Azure.
* Conectar una red virtual entre inquilinos a un centro de conectividad virtual.

Los pasos necesarios para esta configuración se realizan mediante una combinación de Azure Portal y PowerShell, aunque la característica en sí solo está disponible en PowerShell y la CLI.

>[!NOTE]
> Tenga en cuenta que las conexiones de Virtual Network entre inquilinos solo se pueden administrar mediante PowerShell o la CLI. **No puede** administrar conexiones de Virtual Network entre inquilinos en Azure Portal.
> 
## <a name="before-you-begin"></a>Antes de empezar

### <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos de este artículo debe tener la configuración siguiente ya definida en el entorno:

* Una WAN y un centro de conectividad virtuales en la suscripción primaria.
* Una red virtual configurada en una suscripción de otro inquilino (remoto).
* Asegúrese de que el espacio de direcciones de la red virtual del inquilino remoto no se superponga a ningún otro espacio de direcciones de otras redes virtuales ya conectadas al centro de conectividad virtual primario.

### <a name="working-with-azure-powershell"></a>Trabajo con Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Asignación de permisos

Para que el usuario que administra la suscripción primaria al centro virtual pueda modificar las redes virtuales del inquilino remoto y acceder a ellas, debe asignar permisos de **Colaborador** a este usuario. La asignación de permisos de **Colaborador** a este usuario se hace en la suscripción de la red virtual del inquilino remoto.

1. Agregue la asignación de roles **Colaborador** al administrador (el usuario que administra el centro de la WAN virtual). Puede usar PowerShell o Azure Portal para asignar este rol. Vea los siguientes artículos **Incorporación o eliminación de asignaciones de roles** para conocer los pasos:

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portal](../role-based-access-control/role-assignments-portal.md)

1. Ahora agregue la suscripción del inquilino remoto y la del inquilino primario a la sesión actual de PowerShell. Ejecute el siguiente comando: Si ha iniciado sesión en el inquilino primario, solo tiene que ejecutar el comando para el inquilino remoto.

   ```azurepowershell-interactive
   Connect-AzAccount -SubscriptionId "[subscription ID]" -TenantId "[tenant ID]"
   ```

1. Inicie sesión en Azure PowerShell con las credenciales primarias y ejecute el siguiente comando para comprobar que la asignación de roles se ha realizado correctamente:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Si los permisos se han propagado correctamente al inquilino principal y se han agregado a la sesión, la suscripción propiedad de dicho inquilino **y** del inquilino remoto se mostrará en la salida del comando.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>Conexión de una red virtual a un centro de conectividad

En los pasos siguientes se cambia entre el contexto de las dos suscripciones al vincular la red virtual al centro de conectividad virtual. Reemplace los valores del ejemplo para reflejar su propio entorno.

1. Ejecute el siguiente comando para asegurarse de que está en el contexto de la cuenta remota:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Cree una variable local para almacenar los metadatos de la red virtual que quiere conectar al centro de conectividad.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Vuelva a cambiar a la cuenta primaria.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. Conecte la red virtual al centro de conectividad.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $remote
   ```

1. Puede ver la nueva conexión en PowerShell o en Azure Portal.

   * **PowerShell:** los metadatos de la conexión recién formada se muestran en la consola de PowerShell si la conexión se ha formado correctamente.
   * **Azure Portal:** vaya al centro de conectividad virtual, **Conectividad -> Conexiones de red virtual**. Puede ver el puntero a la conexión. Para ver el recurso en sí necesita los permisos adecuados.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Solución de problemas

* Compruebe que los metadatos de $remote (de la [sección](#connect) anterior) coinciden con la información de Azure Portal.
* Puede comprobar los permisos mediante la configuración de IAM del grupo de recursos del inquilino remoto o con comandos de Azure PowerShell (Get-AzSubscription).
* Asegúrese de que se incluyan las comillas en los nombres de los grupos de recursos o cualquier otra variable específica del entorno (por ejemplo, "VirtualHub1" o "VirtualNetwork1").

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Virtual WAN, vea:

* [Preguntas más frecuentes](virtual-wan-faq.md) sobre Virtual WAN
