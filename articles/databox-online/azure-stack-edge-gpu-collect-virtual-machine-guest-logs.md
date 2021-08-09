---
title: Recopilación de registros de invitado de VM en una GPU de Azure Stack Edge Pro
description: Describe cómo crear un paquete de soporte técnico con registros de invitado para VM en un dispositivo GPU de Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/03/2021
ms.author: alkohli
ms.openlocfilehash: 7ffb96cdc3fbb561009b7f545a3b9a1da9eef729
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421011"
---
# <a name="collect-vm-guest-logs-on-an-azure-stack-edge-pro-gpu-device"></a>Recopilación de registros de invitado de VM en un dispositivo GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Para diagnosticar los errores de aprovisionamiento de VM en el dispositivo GPU de Azure Stack Edge Pro, revisará los registros de invitado de la máquina virtual con errores. En este artículo se describe cómo recopilar registros de invitado para las VM en un paquete de soporte técnico.

## <a name="collect-vm-guest-logs-in-support-package"></a>Recopilación de registros de invitado de máquina virtual en el paquete de soporte técnico

Para recopilar registros de invitado para máquinas virtuales con errores en un dispositivo GPU de Azure Stack Edge Pro, siga estos pasos:

1. [Conéctese a la interfaz de PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

2. Recopile los registros de invitado para las VM con errores e inclúyalos en un paquete de soporte técnico mediante la ejecución de los siguientes comandos:

   ```powershell
   Get-VMInGuestLogs -FailedVM
   Get-HcsNodeSupportPackage -Path “\\<network path>” -Include InGuestVMLogFiles -Credential “domain_name\user”
   ```

   Encontrará los registros en la carpeta `hcslogs\VmGuestLogs`.

3. Para obtener los detalles del historial de aprovisionamiento de VM, revise los registros siguientes:

   **VM de Linux:**
   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent.log

   **VM de Windows**:
   - C:\Windows\Azure\Panther\WaSetup.xml

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de aprovisionamiento de VM en un dispositivo GPU de Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)