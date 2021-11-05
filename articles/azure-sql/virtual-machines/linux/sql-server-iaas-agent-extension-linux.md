---
title: Extensión del agente de IaaS de SQL Server para Linux
description: En este artículo se describe cómo ayuda la extensión del Agente de IaaS de SQL Server a automatizar las tareas de administración específicas de SQL Server en las VM de Azure de Linux.
services: virtual-machines-windows
documentationcenter: ''
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.openlocfilehash: 98e448358d31fda34a8bb84024be6daf157d0f78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093631"
---
# <a name="sql-server-iaas-agent-extension-for-linux"></a>Extensión del agente de IaaS de SQL Server para Linux
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-iaas-agent-extension-automate-management.md)
> * [Linux](sql-server-iaas-agent-extension-linux.md)

La extensión del Agente de IaaS de SQL Server (SqlIaasExtension) se ejecuta en Azure Virtual Machines (VM) de SQL Server en Linux para automatizar las tareas de administración. 

En este artículo se proporciona información general de la extensión. Consulte [Registro con la extensión](sql-iaas-agent-extension-register-vm-linux.md) para obtener más información. 


## <a name="overview"></a>Información general

La extensión del agente de laaS de SQL Server habilita la integración con el Azure Portal y desbloquea las siguientes ventajas para las máquinas virtuales de Azure de SQL Server en Linux: 

- **Cumplimiento**: la extensión ofrece un método simplificado para satisfacer el requisito de notificar a Microsoft que se ha habilitado Ventaja híbrida de Azure, según se especifica en los términos del producto. Con este proceso no es necesario administrar formularios de registro de licencias para cada recurso.  

- **Administración de licencias simplificada**: la extensión simplifica la administración de licencias de SQL Server y permite identificar rápidamente las VM de SQL Server con Ventaja híbrida de Azure habilitado en Azure Portal, Azure PowerShell O la CLI de Azure: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---

- **Gratuito**: no hay ningún costo adicional asociado con la extensión. 



## <a name="installation"></a>Instalación

[Registre](sql-iaas-agent-extension-register-vm-linux.md) la máquina virtual con SQL Server con la extensión del Agente de IaaS de SQL Server para crear el _recurso_ de **máquina virtual de SQL** dentro de la suscripción, que es un recurso _independiente_ del recurso de máquina virtual. La anulación del registro de la VM con SQL Server de la extensión eliminará el **recurso** de _máquina virtual con SQL_ de su suscripción, pero no eliminará la máquina virtual real.

La extensión del agente laaS de SQL Server para Linux solo está disponible actualmente en modo ligero. 


## <a name="verify-extension-status"></a>Comprobación del estado de la extensión

Use Azure Portal o Azure PowerShell para comprobar el estado de la extensión. 

### <a name="azure-portal"></a>Azure portal

Compruebe que la extensión esté instalada mediante Azure Portal. 

Vaya al recurso **máquina virtual** en Azure Portal (no el recurso *máquinas virtuales con SQL*, sino el recurso de la máquina virtual). Seleccione **Extensiones** en **Configuración**. Debería aparecer la extensión **SqlIaasExtension**, como en el ejemplo siguiente: 

![Compruebe el estado de la extensión del agente laaS de SQL Server SqlIaaSExtension en Azure Portal](../windows/media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)




### <a name="azure-powershell"></a>Azure PowerShell

También puede usar el cmdlet de Azure PowerShell **Get-AzVMSqlServerExtension**:

```powershell-interactive
  Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
```

El comando anterior confirma que el agente está instalado y proporciona información de estado general. Puede obtener información de estado específica sobre Automated Backup y Automated Patching con los siguientes comandos:

```powershell-interactive
 $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
 $sqlext.AutoPatchingSettings
 $sqlext.AutoBackupSettings
```

## <a name="limitations"></a>Limitaciones

La extensión del agente IaaS de SQL de Linux tiene las siguientes limitaciones: 

- Solo se admiten las máquinas virtuales con SQL Server que se ejecutan en el sistema operativo Ubuntu Linux. Actualmente no se admiten otras distribuciones de Linux.
- No se admiten las máquinas virtuales con SQL Server que se ejecutan en el Ubuntu Linux Pro.
- No se admiten las máquinas virtuales con SQL Server que se ejecutan en imágenes generalizadas.
- Solo se admiten las máquinas virtuales con SQL Server implementadas mediante Azure Resource Manager. No se admiten las máquinas virtuales con SQL Server implementadas con el modelo clásico. 
- SQL Server con una sola instancia. No se admiten múltiples instancias. 

## <a name="privacy-statement"></a><a id="in-region-data-residency"></a> Declaración de privacidad

Al usar SQL Server en Azure Virtual Machines y la extensión IaaS de SQL, tenga en cuenta las siguientes declaraciones de privacidad: 

- **Recopilación de datos**: la extensión del agente IaaS de SQL recopila datos con el fin de ofrecer a los clientes ventajas opcionales al usar SQL Server en Azure Virtual Machines. Microsoft **no usará estos datos para auditorías de licencias** sin el consentimiento previo del cliente. Para obtener más información, consulte [Complemento de privacidad de SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

- **Residencia de datos en la región**: las SQL Server máquinas virtuales de Azure y la extensión del agente IaaS de SQL no mueven ni almacenan datos de clientes fuera de la región en la que se implementan las máquinas virtuales. 


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo ejecutar SQL Server en Azure Virtual Machines, consulte [¿Qué es SQL Server en Linux Virtual Machines de Azure?](sql-server-on-linux-vm-what-is-iaas-overview.md).

Para más información, consulte las [preguntas más frecuentes](frequently-asked-questions-faq.yml).
