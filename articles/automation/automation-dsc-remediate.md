---
title: Corrección de servidores de Azure Automation State Configuration no conformes
description: En este artículo se indica cómo volver a aplicar las configuraciones a petición en los servidores donde ha variado el estado de configuración.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: e7988f8b72a6621897c656e12595a3fe68665535
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429845"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Corrección de servidores de Azure Automation State Configuration no conformes

Cuando los servidores se registran con Azure Automation State Configuration, el modo de configuración se establece en `ApplyOnly`, `ApplyAndMonitor`o `ApplyAndAutoCorrect`. Si el modo no está establecido en `ApplyAndAutoCorrect`, los servidores que dejan de ser conformes permanecen en ese estado de no conformidad hasta que se corrigen manualmente.

Azure Compute ofrece una característica denominada "Ejecutar comando" que permite a los clientes ejecutar scripts dentro de las máquinas virtuales.
En este documento se proporcionan scripts de ejemplo de esta característica que se pueden aplicar para corregir manualmente la variación de la configuración.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Corrección de variación en máquinas virtuales Windows con PowerShell

Puede corregir el desfase de las máquinas virtuales de Windows mediante la característica de comando `Run`. Vea [Ejecución de scripts de PowerShell en la máquina virtual Windows con el comando Ejecutar](../virtual-machines/windows/run-command.md).

Para hacer que un nodo de Azure Automation State Configuration descargue la configuración más reciente y la aplique, use el cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Corrección de variación en máquinas virtuales Linux

En el caso de las máquinas virtuales Linux, no tiene la opción de usar el comando `Run`. Solo puede corregir el desfase de estas máquinas repitiendo el proceso de registro. 

En el caso de los nodos de Azure, puede corregir esta variación en Azure Portal o mediante los cmdlets del módulo Az. Los detalles sobre este proceso se documentan en [Habilitar una máquina virtual mediante Azure Portal](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

En el caso de los nodos híbridos, puede corregir este desfase con los scripts de Python incluidos. Vea [Realizar operaciones de DSC desde el equipo Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Pasos siguientes

- Para ver una referencia de los cmdlets de PowerShell, consulte [Az.Automation](/powershell/module/az.automation/#automation).
- Para ver un ejemplo del uso de Azure Automation State Configuration en una canalización de implementación continua, vea [Configuración de la implementación continua con Chocolatey](automation-dsc-cd-chocolatey.md).
