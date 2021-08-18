---
title: Importación de máquinas virtuales de otro laboratorio de Azure DevTest Labs
description: En este artículo se describe cómo importar máquinas virtuales de otro laboratorio en el laboratorio actual de Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3e2cce7a364254798c88697d942041c1503af193
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300442"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importación de máquinas virtuales de otro laboratorio de Azure DevTest Labs
En este artículo se proporciona información sobre cómo importar máquinas virtuales de otro laboratorio al suyo.

## <a name="scenarios"></a>Escenarios
Estos son algunos escenarios en los que es necesario importar máquinas virtuales de un laboratorio a otro:

- Una persona del equipo se cambia a otro grupo dentro de la empresa y quiere tomar el escritorio del desarrollador al laboratorio DevTest del nuevo equipo.
- El grupo ha alcanzado una [cuota de nivel de suscripción](../azure-resource-manager/management/azure-subscription-service-limits.md) y quiere dividir los equipos en varias suscripciones.
- La empresa se cambia a Express Route (o a cualquier otra topología de redes nueva) y el equipo quiere mover las máquinas virtuales para que usen esta nueva infraestructura.

## <a name="solution-and-constraints"></a>Solución y restricciones
Esta característica le habilita para importar las máquinas virtuales de un laboratorio (origen) a otro laboratorio (destino). Durante el proceso, se puede proporcionar un nombre nuevo para la máquina virtual de destino. El proceso de importación incluye todas las dependencias, como discos, programaciones, configuración de red, etc.

El proceso tarda un rato en completarse y le afectan los siguientes factores:

- La cantidad y el tamaño de los discos que están adjuntados a la máquina de origen (ya que es una operación de copia y no una operación de movimiento).
- Distancia al destino (por ejemplo, de la región Este de EE. UU. a Sudeste de Asia).

Una vez completado el proceso, la máquina virtual de origen permanece apagada y la nueva se ejecuta en el laboratorio de destino.

Hay dos restricciones clave que deben tenerse en cuenta al planificar la importación de máquinas virtuales de un laboratorio a otro:

- Se admite la importación de máquinas virtuales a distintas suscripciones y regiones, pero las suscripciones deben estar asociadas al mismo inquilino de Azure Active Directory.
- Las máquinas virtuales no deben estar en un estado reclamable en el laboratorio de origen.
- Se es el propietario de la máquina virtual en el laboratorio de origen y en el de destino.
- Actualmente, esta característica solo se admite con PowerShell y API REST.

## <a name="use-powershell"></a>Uso de PowerShell
Descargue el archivo ImportVirtualMachines.ps1 de [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Puede usar el script para importar una máquina virtual o todas ellas del laboratorio de origen al de destino.

### <a name="use-powershell-to-import-a-single-vm"></a>Uso de PowerShell para importar una sola máquina virtual
La ejecución de este script de PowerShell requiere la identificación de la máquina virtual de origen y el laboratorio de destino y, opcionalmente, la especificación de un nombre nuevo que va a usar la máquina de destino:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Uso de PowerShell para importar todas las máquinas virtuales en el laboratorio de origen
Si no se especifica la máquina Virtual de origen, el script importará automáticamente todas las máquinas virtuales en los laboratorios DevTest.  Por ejemplo:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Uso de REST HTTP para importar una máquina virtual
La llamada a REST es sencilla. Se proporciona información suficiente para identificar los recursos de origen y destino. Recuerde que la operación se realiza en el recurso de laboratorio de destino.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Set policies for a lab](devtest-lab-set-lab-policy.md) (Definición de directivas para un laboratorio)
- [Preguntas más frecuentes](devtest-lab-faq.yml)
