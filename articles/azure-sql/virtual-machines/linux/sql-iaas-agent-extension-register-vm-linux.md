---
title: Registro con una extensión de agente IaaS de SQL (Linux)
description: Obtenga información sobre cómo registrar su VM de Azure con SQL Server en Linux con la extensión Agente de IaaS de SQL para habilitar las características de Azure, así como para el cumplimiento y la capacidad de administración mejorada.
services: virtual-machines-windows
documentationcenter: na
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-Linux-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 2cf9219f5afc6805c147243ec0157280c839dc87
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093632"
---
# <a name="register-linux-sql-server-vm-with-sql-iaas-agent-extension"></a>Registro de VM con SQL Server de Linux mediante la extensión Agente de IaaS de SQL 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-agent-extension-manually-register-single-vm.md)
> * [Linux](sql-iaas-agent-extension-register-vm-linux.md)


Registro de la VM con SQL Server con la [extensión Agente de IaaS de SQL](sql-server-iaas-agent-extension-linux.md) para desbloquear una gran cantidad de ventajas relacionadas con características para su VM de Azure con SQL Server en Linux.

## <a name="overview"></a>Información general

El registro con la [extensión Agente de IaaS de SQL Server](sql-server-iaas-agent-extension-linux.md) crea el _recurso_ de **VM con SQL** en su suscripción, que es un recurso _independiente_ del recurso de máquina virtual. La anulación del registro de la VM con SQL Server de la extensión elimina el _recurso_ de **máquina virtual con SQL**, pero no eliminará la máquina virtual real.

Para utilizar la extensión Agente de IaaS de SQL, primero debe [registrar la suscripción con el proveedor **Microsoft.SqlVirtualMachine**](#register-subscription-with-rp), lo que proporciona a la extensión IaaS de SQL la capacidad de crear recursos dentro de esa suscripción específica.

> [!IMPORTANT]
> La extensión del Agente de IaaS de SQL recopila datos con el fin de ofrecer a los clientes ventajas opcionales al usar SQL Server en Azure Virtual Machines. Microsoft no usará estos datos para auditorías de licencias sin el consentimiento previo del cliente. Para obtener más información, consulte [Complemento de privacidad de SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

## <a name="prerequisites"></a>Requisitos previos

Para registrar una máquina virtual con SQL Server con la extensión, necesita lo siguiente:

- Una [suscripción de Azure](https://azure.microsoft.com/free/).
- Una [máquina virtual Ubuntu Linux](../../../virtual-machines/linux/quick-create-portal.md) del modelo de recursos de Azure con [SQL Server 2017 (o superior)](https://www.microsoft.com/sql-server/sql-server-downloads) implementado en la nube pública o de Azure Government.
- La versión más reciente de la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell (5.0 como mínimo)](/powershell/azure/install-az-ps).

## <a name="register-subscription-with-rp"></a>Registro de la suscripción con el proveedor de recursos

Para registrar la máquina virtual con SQL Server con la extensión Agente de IaaS de SQL, debe registrar primero su suscripción con el proveedor de recursos **Microsoft.SqlVirtualMachine**. Esto permite a la extensión Agente de IaaS de SQL crear recursos en la suscripción. Para ello, puede usar Azure Portal, la CLI de Azure o Azure PowerShell.

### <a name="azure-portal"></a>Azure portal

Registre la suscripción con el proveedor de recursos mediante Azure Portal:

1. Abra Azure Portal y vaya a **Todos los servicios**.
1. Vaya a **Suscripciones** y seleccione la suscripción que le interese.
1. En la página **Suscripciones**, en **Configuración**, seleccione **Proveedores de recursos**.
1. Escriba **sql** en el filtro para mostrar los proveedores de recursos relacionados con SQL.
1. Seleccione **Registrar**, **Volver a registrar** o **Anular registro** para el proveedor **Microsoft.SqlVirtualMachine**, según la acción que quiera realizar.


![Modificación del proveedor](../windows/media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

### <a name="command-line"></a>Línea de comandos

Registre la suscripción de Azure con el proveedor **Microsoft.SqlVirtualMachine** mediante la CLI de Azure o Azure PowerShell.

# <a name="azure-cli"></a>[CLI de Azure](#tab/bash)

Registre la suscripción con el proveedor de recursos mediante la CLI de Azure: 

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registre la suscripción con el proveedor de recursos mediante Azure PowerShell: 

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-vm"></a>Registrar máquina virtual

La extensión de agente de IaaS de SQL en Linux solo está disponible en modo ligero, que solo admite el cambio del tipo de licencia y la edición de SQL Server. Use la CLI de Azure o Azure PowerShell para registrar su máquina virtual con SQL Server con la extensión en el modo ligero para obtener una funcionalidad limitada. 

Proporcione el tipo de licencia de SQL Server como pago por uso (`PAYG`) para pagar por uso, Ventaja híbrida de Azure (`AHUB`) para usar su propia licencia o recuperación ante desastres (`DR`) para activar [la licencia de réplica de recuperación ante desastres gratuita](../windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

# <a name="azure-cli"></a>[CLI de Azure](#tab/bash)

Registre una VM con SQL Server en modo ligero con la CLI de Azure:

```azurecli-interactive
# Register Enterprise or Standard self-installed VM in Lightweight mode
az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registre una VM con SQL Server en modo ligero con Azure PowerShell:

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType <license_type>  -SqlManagementType LightWeight  
```

---

## <a name="verify-registration-status"></a>Comprobación del estado de registro

Puede comprobar si su VM con SQL Server ya se ha registrado con la extensión Agente de IaaS de SQL mediante Azure Portal, la CLI de Azure o Azure PowerShell.


### <a name="azure-portal"></a>Azure portal

Compruebe el estado del registro mediante el Azure Portal: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Vaya al recurso Máquinas virtuales SQL.
1. Seleccione la máquina con SQL Server en la lista. Si la VM con SQL Server no aparece en la lista, es probable que no se haya registrado con la extensión Agente de IaaS de SQL.

### <a name="command-line"></a>Línea de comandos

Compruebe el estado actual de registro de la VM con SQL Server mediante la CLI de Azure o Azure PowerShell. `ProvisioningState` muestra `Succeeded` si el registro fue correcto.

# <a name="azure-cli"></a>[CLI de Azure](#tab/bash)

Compruebe el estado del registro mediante la CLI de Azure:

```azurecli-interactive
az sql vm show -n <vm_name> -g <resource_group>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Compruebe el estado del registro mediante el Azure PowerShell:

```powershell-interactive
Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
```

---

Un error indica que la VM con SQL Server no se ha registrado con la extensión.


## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos.

* [Introducción a SQL Server en máquinas virtuales Windows](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Preguntas más frecuentes de SQL Server en máquinas virtuales Windows](frequently-asked-questions-faq.yml)
* [Orientación de precios de SQL Server para máquinas virtuales de Azure](../windows/pricing-guidance.md)
* [Notas de la versión de SQL Server en Azure Virtual Machines](../windows/doc-changes-updates-release-notes.md)
