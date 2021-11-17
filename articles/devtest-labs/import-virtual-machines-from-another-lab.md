---
title: Importación de máquinas virtuales de otro laboratorio
description: Aprenda a importar máquinas virtuales de un laboratorio a otro en Azure DevTest Labs.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 50f17183caa7da86ec6704af35129ed8bd707d5a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059854"
---
# <a name="import-virtual-machines-from-one-lab-to-another"></a>Importación de máquinas virtuales de un laboratorio a otro

En este artículo se describe cómo importar máquinas virtuales de un laboratorio de DevTest Labs a otro.

## <a name="scenarios"></a>Escenarios
Estos son algunos escenarios en los que puede que sea necesario importar máquinas virtuales de un laboratorio a otro:

- Una persona pasa de un grupo a otro y quiere llevar su escritorio para desarrolladores al laboratorio del nuevo equipo.
- El grupo ha alcanzado una [cuota de nivel de suscripción](../azure-resource-manager/management/azure-subscription-service-limits.md) y quiere dividir los equipos en más de una suscripción de Azure.
- La empresa se cambia a Azure ExpressRoute, o a cualquier otra topología de redes nueva, y el equipo quiere mover las máquinas virtuales para que usen la nueva infraestructura.

## <a name="requirements-and-constraints"></a>Requisitos y restricciones

El proceso de importación importa las máquinas virtuales del laboratorio de origen en el laboratorio de destino. Si lo desea, puede cambiar el nombre de la máquina virtual en el proceso. El proceso de importación incluye todas las dependencias, como discos, programaciones y configuración de red.

El proceso es una operación de copia, no una operación de movimiento, y puede tardar mucho tiempo. El tiempo que dure la importación depende parcialmente de los siguientes factores:

- Número y tamaño de los discos conectados a la máquina de origen
- Distancia entre las regiones de origen y de destino

Cuando finaliza la importación, el proceso apaga la máquina virtual de origen e inicia la nueva máquina virtual, que se ejecuta en el laboratorio de destino.

Existen varios requisitos y restricciones para importar máquinas virtuales de un laboratorio a otro:

- Se pueden importar máquinas virtuales entre suscripciones y regiones, pero ambas suscripciones deben estar asociadas al mismo inquilino de Azure Active Directory.
- Las máquinas virtuales no pueden estar en un estado reclamable en el laboratorio de origen.
- Debe ser el propietario de la máquina virtual que se encuentra en el laboratorio de origen y el propietario del laboratorio de destino.
- Actualmente, esta característica solo se admite a través de PowerShell y la API REST.

## <a name="use-powershell-to-import-one-or-all-lab-vms"></a>Uso de PowerShell para importar un máquina virtual de laboratorio o todas ellas

Descargue y ejecute [ImportVirtualMachines.ps1](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Puede usar el script para importar una máquina virtual, o todas ellas, del laboratorio de origen al de destino.

Para ejecutar este script de PowerShell, identifique las suscripciones y laboratorios de origen y destino, así como la máquina virtual de origen. Opcionalmente, especifique un nuevo nombre para la máquina virtual de destino.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>"`
                            -SourceDevTestLabName "<Name of the source lab>"`
                            -SourceVirtualMachineName "<Name of the VM to import from the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contains the destination lab>"`
                            -DestinationDevTestLabName "<Name of the destination lab>"`
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```
Si no especifica una máquina virtual de origen, el script importa automáticamente todas las máquinas virtuales del laboratorio de origen.

## <a name="use-http-rest-to-import-a-vm"></a>Uso de REST HTTP para importar una máquina virtual

La llamada a REST es sencilla. Proporcione la información necesaria para identificar los recursos de origen y destino. La operación tiene lugar en el recurso de laboratorio de destino.

```http
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Pasos siguientes

- [Set policies for a lab](devtest-lab-set-lab-policy.md) (Definición de directivas para un laboratorio)
- [Preguntas frecuentes sobre DevTest Labs](devtest-lab-faq.yml)
