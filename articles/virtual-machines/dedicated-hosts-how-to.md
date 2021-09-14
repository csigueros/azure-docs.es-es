---
title: Implementación de hosts dedicados de Azure
description: Implemente máquinas virtuales y conjuntos de escalado en hosts dedicados.
author: brittanyrowe
ms.author: brittanyrowe
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2021
ms.reviewer: cynthn, zivr
ms.openlocfilehash: c4d895551f852c26d9083c570de29d5cbdedfa53
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440230"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts"></a>Implementación de máquinas virtuales y conjuntos de escalado en hosts dedicados

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado uniformes

En este artículo se ofrecen instrucciones para crear un [host dedicado](dedicated-hosts.md) de Azure para hospedar las máquinas virtuales (VM) y las instancias de conjuntos de escalado.


## <a name="limitations"></a>Limitaciones

- Los tamaños y tipos de hardware disponibles para hosts dedicados varían según la región. Para más información, consulte la [página de precios](https://aka.ms/ADHPricing) de hosts.

## <a name="create-a-host-group"></a>Creación de un grupo host

Un **grupo host** es un recurso que representa una colección de hosts dedicados. Puede crear un grupo host en una región y una zona de disponibilidad, y agregarle hosts. Al planear la alta disponibilidad, hay otras opciones. Puede usar una o ambas de las dos opciones siguientes con los hosts dedicados:
- Abarcar varias zonas de disponibilidad. En este caso, es necesario tener un grupo host en cada una de las zonas que quiera usar.
- Abarcar varios dominios de error que se asignan a bastidores físicos.

En cualquier caso, es necesario proporcionar el número de dominios de error del grupo host. Si no quiere abarcar dominios de error en el grupo, use un número de dominios de error de 1.

También puede usar zonas de disponibilidad y dominios de error a la vez.

### <a name="portal"></a>[Portal](#tab/portal)

En este ejemplo, se creará un grupo host con una zona de disponibilidad y dos dominios de error.

1. Abra el [portal](https://portal.azure.com).
1. Seleccione **Crear un recurso** en la esquina superior izquierda.
1. Busque el **grupo host** y, a continuación, seleccione los **grupos host** en los resultados.
1. En la página de los **grupos host**, seleccione **Crear**.
1. Seleccione la suscripción que quiere usar y, a continuación, seleccione **Crear nuevo** para crear un nuevo grupo de recursos.
1. Escriba *myDedicatedHostsRG* como **nombre** y seleccione **Aceptar**.
1. En el **nombre del grupo host**, escriba *myHostGroup*.
1. En **Ubicación**, seleccione **Este de EE.UU.**
1. Para la **zona de disponibilidad**, seleccione **1**.
1. En el **recuento de dominios de error**, seleccione **2**.
1. Seleccione **Selección de ubicación automática** para asignar automáticamente las instancias de máquinas virtuales y conjuntos de escalado a un host disponible de este grupo.
1. Seleccione **Revisar y crear** y, a continuación, espere a la confirmación.
1. Cuando reciba el mensaje de **Validación superada**, seleccione **Crear** para crear el grupo host.

Solo tardará unos minutos en crear el grupo host.


### <a name="cli"></a>[CLI](#tab/cli)

No todas las SKU de host están disponibles en todas las regiones y zonas de disponibilidad. Puede enumerar la disponibilidad del host y cualquier restricción de la oferta antes de iniciar el aprovisionamiento de hosts dedicados.

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table
```

En este ejemplo, se usará [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) para crear un grupo host con zonas de disponibilidad y dominios de error.

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2
```

Agregue el parámetro `--automatic-placement true` para que las máquinas virtuales y las instancias del conjunto de escalado se coloquen automáticamente en los hosts, dentro de un grupo host. Para obtener más información, vea [Selección de ubicación manual frente a automática ](dedicated-hosts.md#manual-vs-automatic-placement).


**Otros ejemplos**

También se puede usar [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) para crear un grupo host en la zona de disponibilidad 1, pero sin ningún dominio de error.

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1
```

En el ejemplo siguiente se usa [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) para crear un grupo host únicamente con dominios de error (para usarlo en regiones que no admitan zonas de disponibilidad).

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

En este ejemplo se usa [New-AzHostGroup](/powershell/module/az.compute/new-azhostgroup) para crear un grupo host en la zona 1 con dos dominios de error.


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


Agregue el parámetro `-SupportAutomaticPlacement true` para que las máquinas virtuales y las instancias del conjunto de escalado se coloquen automáticamente en los hosts, dentro de un grupo host. Para obtener más información, vea [Selección de ubicación manual frente a automática ](dedicated-hosts.md#manual-vs-automatic-placement).

---


## <a name="create-a-dedicated-host"></a>Creación de un host dedicado

Ahora crearemos un host dedicado en el grupo host. Además de un nombre para el host, se le pedirá que proporcione el SKU del host. El SKU del host registra la serie de máquinas virtuales admitidas, así como la generación de hardware del host dedicado.

Para más información sobre los precios y los SKU de host, consulte [Precios de hosts dedicados de Azure](https://aka.ms/ADHPricing).

Si establece un número de dominios de error para el grupo host, tendrá que especificar el dominio de error para el host.

### <a name="portal"></a>[Portal](#tab/portal)

1. Seleccione **Crear un recurso** en la esquina superior izquierda.
1. Busque un **host dedicado** y, a continuación, seleccione los **hosts dedicados** en los resultados.
1. En la página de **hosts dedicados**, seleccione **Crear**.
1. Seleccione la suscripción que quiere usar.
1. Seleccione *myDedicatedHostsRG* como **grupo de recursos**.
1. En los **detalles de la instancia**, escriba *myHost* en el **nombre** y seleccione *Este de EE. UU.* como ubicación.
1. En **Perfil de hardware**, seleccione *Familia Standard Es3: tipo 1* para **Familia de tamaños**, *myHostGroup* para **Grupo host** y luego *1* para **Dominio de error**. En el resto de los campos, deje los valores predeterminados.
1. Cuando termine, seleccione **Revisar y crear** y, a continuación, espere a la confirmación.
1. Cuando reciba el mensaje de **Validación superada**, seleccione **Crear** para crear el host.

### <a name="cli"></a>[CLI](#tab/cli)

Use [az vm host create](/cli/azure/vm/host#az_vm_host_create) para crear un host. Si establece un número de dominios de error para el grupo host, se le pedirá que especifique el dominio de error para su host.

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

En este ejemplo, se usa [New-AzHost](/powershell/module/az.compute/new-azhost) para crear un host y establecer el dominio de error en 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

---

## <a name="create-a-vm"></a>Crear una VM

Ahora cree una máquina virtual en el host.

### <a name="portal"></a>[Portal](#tab/portal)

1. Elija **Crear un recurso** en la esquina superior izquierda de Azure Portal.
1. En el cuadro de búsqueda que está encima de la lista de recursos de Azure Marketplace, busque y seleccione la imagen que desea utilizar y, a continuación, elija **Crear**.
1. En la pestaña **Aspectos básicos**, en **Detalles del proyecto**, asegúrese de que esté seleccionada la suscripción correcta y luego seleccione *myDedicatedHostsRG* como **grupo de recursos**.
1. En **Detalles de instancia**, escriba *myVM* en **Nombre de máquina virtual** y elija *Este de EE. UU.* como **Ubicación**.
1. En **Opciones de disponibilidad**, seleccione **Zona de disponibilidad** y seleccione *1* en la lista desplegable.
1. En cuanto al tamaño, seleccione **Cambiar tamaño**. En la lista de tamaños disponibles, elija uno de la serie Esv3, como **Standard E2s v3**. Es posible que tenga que borrar el filtro para poder ver todos los tamaños disponibles.
1. Complete el resto de los campos de la pestaña **Básico** según sea necesario.
1. Si desea especificar qué host utilizar para la máquina virtual, en la parte superior de la página, seleccione la pestaña **Opciones avanzadas** y, en la sección **Host**, seleccione *myHostGroup* en **Grupo host** y *myHost* en **Host**. Si no lo hace, la máquina virtual se colocará automáticamente en un host con capacidad.
    ![Selección del grupo host y el host](./media/dedicated-hosts-portal/advanced.png)
1. Deje los valores predeterminados restantes y luego seleccione el botón **Revisar + crear** en la parte inferior de la página.
1. Cuando vea el mensaje en el que se indica que la validación se ha realizado correctamente, seleccione **Crear**.

La implementación de la máquina virtual tardará unos minutos.


### <a name="cli"></a>[CLI](#tab/cli)

Cree una máquina virtual en un host dedicado mediante [az vm create](/cli/azure/vm#az_vm_create). Si especificó una zona de disponibilidad al crear el grupo host, debe usar la misma zona al crear la máquina virtual. Reemplace los valores de imagen y nombre del host por los suyos propios. Si va a crear una máquina virtual Windows, quite `--generate-ssh-keys` para que se le pida una contraseña.

```azurecli-interactive
az vm create \
   -n myVM \
   --image myImage \
   --host-group myHostGroup \
   --admin-username azureuser \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Para colocar la máquina virtual en un host concreto, use `--host` en lugar de especificar el grupo host con `--host-group`.

> [!WARNING]
> Si crea una máquina virtual en un host que no tenga suficientes recursos, la máquina virtual se creará en un estado de error.


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Cree una máquina virtual en el host mediante [New-AzVM](/powershell/module/az.compute/new-azvm). En este ejemplo, como el grupo host está en la zona 1, es preciso crear la máquina virtual en la zona 1.


```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image myImage `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Si crea una máquina virtual en un host que no tenga suficientes recursos, la máquina virtual se creará en un estado de error.

---

## <a name="create-a-scale-set"></a>Creación de un conjunto de escalado

También puede crear un conjunto de escalado en el host.

### <a name="portal"></a>[Portal](#tab/portal)

Cuando se implementa un conjunto de escalado, se especifica el grupo host.

1. Busque *Conjunto de escalado* y seleccione **Conjuntos de escalado de máquinas virtuales** de la lista.
1. Seleccione **Agregar** para crear un nuevo conjunto de escalado.
1. Complete los campos de la pestaña **Básico** como lo haría normalmente, pero asegúrese de seleccionar un tamaño de máquina virtual que sea de la serie que eligió para el host dedicado, como **E2s v3 Estándar**.
1. En la pestaña **Avanzado**, para **Algoritmo de propagación**, seleccione **Propagación máxima**.
1. En **Grupo host**, seleccione el grupo host en el menú desplegable. Si ha creado el grupo recientemente, puede que tarde un minuto en agregarse a la lista.


### <a name="cli"></a>[CLI](#tab/cli)

Cuando se implementa un conjunto de escalado mediante [az vmss create](/cli/azure/vmss#az_vmss_create), el grupo host se especifica mediante `--host-group`. En este ejemplo, se implementa la imagen más reciente de Ubuntu LTS. Para implementar una imagen de Windows, reemplace el valor de `--image` y quite `--generate-ssh-keys` para que se le pida una contraseña.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

Si desea elegir manualmente en qué host se va a implementar el conjunto de escalado, agregue `--host` y el nombre del host.


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Implemente un conjunto de escalado en el host mediante [New-AzVMSS](/powershell/module/az.compute/new-azvmss). Cuando se implementa un conjunto de escalado, se especifica el grupo host.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

Si desea elegir manualmente en qué host se va a implementar el conjunto de escalado, agregue `--host` y el nombre del host.

---

## <a name="add-an-existing-vm"></a>Incorporación de una máquina virtual existente

Puede agregar una máquina virtual existente a un host dedicado, pero antes es necesario detenerla o desasignarla. Antes de mover una máquina virtual a un host dedicado, asegúrese de que se admite su configuración:

- El tamaño de la máquina virtual debe estar en la misma familia de tamaños que el host dedicado. Por ejemplo, si el host dedicado es DSv3, el tamaño de la máquina virtual puede ser Standard_D4s_v3, pero no Standard_A4_v2.
- Es preciso que la máquina virtual se encuentre en la misma región que el host dedicado.
- La máquina virtual no puede formar parte de ningún grupo de ubicación por proximidad. Quite la máquina virtual del grupo de ubicación por proximidad antes de moverla a un host dedicado. Para más información, consulte [Traslado de una VM existente fuera de un grupo de selección de ubicación de proximidad](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- La máquina virtual no puede estar en un conjunto de disponibilidad.
- Si la máquina virtual está en una zona de disponibilidad, debe estar en la misma que el grupo host. Los valores de zona de disponibilidad de la máquina virtual y del grupo host deben coincidir.

### <a name="portal"></a>[Portal](#tab/portal2)

Mueva la máquina virtual a un host dedicado desde el [portal](https://portal.azure.com).

1. Abra la página de la máquina virtual.
1. Seleccione **Stop** (Detener) para detener o desasignar la máquina virtual.
1. Seleccione **Configuration** (Configuración) en el menú de la izquierda.
1. Seleccione un grupo host y un host en los menús desplegables.
1. Cuando haya terminado, seleccione **Save** (Guardar) en la parte superior de la página.
1. Una vez que la máquina virtual se haya agregado al host, seleccione **Overview** (Información general) en el menú de la izquierda.
1. En la parte superior de la página, seleccione **Start** (Iniciar) para reiniciar la máquina virtual.


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

Reemplace el valor de las variables por su propia información.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName

$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName

$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force

Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug

Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```

---


## <a name="check-the-status-of-the-host"></a>Comprobación del estado del host

Si necesita saber cuánta capacidad todavía hay disponible en un modo, puede comprobar el estado.

### <a name="portal"></a>[Portal](#tab/portal)

1. Busque y seleccione el host.
1. En la página **Información general** del host, desplácese hacia abajo para ver la lista de tamaños que aún están disponibles para el host. Debe tener un aspecto similar al siguiente:

:::image type="content" source="media/dedicated-hosts-portal/host-status.png" alt-text="Comprobación de la capacidad disponible del host en la página de información general del host.":::

### <a name="cli"></a>[CLI](#tab/cli)

[az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view) permite comprobar el estado de mantenimiento del host y el número de máquinas virtuales que todavía se pueden implementar.

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```

El resultado será similar al siguiente:

```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


Mediante [Get-AzHost](/powershell/module/az.compute/get-azhost) con el parámetro `-InstanceView` puede comprobar el estado de mantenimiento del host y el número de máquinas virtuales que aún se pueden implementar en el host.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

El resultado será similar al siguiente:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           :
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    :
    AllocatableVMs[0]  :
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  :
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  :
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  :
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  :
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  :
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  :
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  :
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  :
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  :
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          :
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          :
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    :
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

---

## <a name="deleting-hosts"></a>Eliminación de hosts 
Aunque no se implementen máquinas virtuales, se le cobrará por los hosts dedicados. Elimine los hosts que no use actualmente para ahorrar costos.

Solo se puede eliminar un host cuando no haya ninguna máquina virtual que lo use.

### <a name="portal"></a>[Portal](#tab/portal)

1. Busque y seleccione el host.
1. En el menú de la izquierda, seleccione **Instancias**.
1. Seleccione y elimine cada máquina virtual.
1. Cuando se hayan eliminado todas las máquinas virtuales, vuelva a la página **Información general** del host y seleccione **Eliminar** en el menú superior.
1. Una vez que elimine el host, abra la página del grupo host y seleccione **Eliminar grupo host**.

### <a name="cli"></a>[CLI](#tab/cli)

 Elimine las máquinas virtuales con [az vm delete](/cli/azure/vm#az_vm_delete).

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

Después de eliminar las máquinas virtuales, puede eliminar el host mediante [az vm host delete](/cli/azure/vm/host#az_vm_host_delete).

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost
```

Una vez que haya eliminado todos los hosts, puede eliminar el grupo host con [az vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete).

```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup
```

También puede eliminar todo el grupo de recursos con un solo comando. Se eliminarán todos los recursos creados en el grupo, lo que incluye las máquinas virtuales, los hosts y los grupos host.

```azurecli-interactive
az group delete -n myDHResourceGroup
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Elimine las máquinas virtuales mediante el comando[Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Después de eliminar las máquinas virtuales, puede eliminar el host mediante [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Una vez que haya eliminado todos los hosts, puede eliminar el grupo host mediante[Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

También puede eliminar todo el grupo de recursos con un solo comando mediante [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Se eliminarán todos los recursos creados en el grupo, lo que incluye las máquinas virtuales, los hosts y los grupos host.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```

---

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más detalles, consulte la introducción a los [hosts dedicados](dedicated-hosts.md).

- Hay una plantilla de ejemplo, disponible en las [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md), en la que se usan zonas y dominios de error para obtener la máxima resistencia en una región.

