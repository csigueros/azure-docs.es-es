---
title: Referencia de PowerShell del marco de automatización de implementación de SAP | Microsoft Docs
description: Referencia de PowerShell del marco de automatización de implementación de SAP en Azure
services: virtual-machines-windows
author: kimforss
manager: kimforss
keywords: Azure, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.workload: infrastructure
ms.date: 11/17/2021
ms.author: kimforss
ms.openlocfilehash: baff0193394e64a4699e519565117e1dd3996afa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725279"
---
# <a name="using-powershell-in-sap-deployment-automation-framework"></a>Uso de PowerShell en el marco de automatización de la implementación de SAP

Puede implementar todos los componentes del [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) mediante Microsoft PowerShell.

## <a name="control-plane-operations"></a>Operaciones del plano de control

Puede implementar o actualizar el plano de control mediante el comando [New-SAPAutomationRegion](module/automation-new-sapautomationregion.md) de PowerShell.

Eliminación del plano de control mediante el comando [`Remove-SAPAutomationRegion`](module/automation-remove-sapautomationregion.md) de PowerShell.

Puede arrancar el implementador en el plano de control mediante el comando [New-SAPDeployer](module/automation-new-sapdeployer.md) de PowerShell.

Puede arrancar la biblioteca SAP en el plano de control mediante el comando [New-SAPLibrary](module/automation-new-saplibrary.md) de PowerShell.

## <a name="workload-zone-operations"></a>Operaciones de la zona de cargas de trabajo

Implemente o actualice la zona de cargas de trabajo mediante el comando [`New-SAPWorkloadZone`](module/automation-new-sapworkloadzone.md) de PowerShell.

Elimine la zona de cargas de trabajo mediante el comando [`Remove-SAPSystem`](module/automation-remove-sapsystem.md) de PowerShell.


## <a name="sap-system-operations"></a>Operaciones del sistema SAP

Implemente o actualice el sistema SAP mediante el comando [`New-SAPSystem`](module/automation-new-sapsystem.md) de PowerShell.

Elimine el sistema SAP mediante el comando [`Remove-SAPSystem`](module/automation-remove-sapsystem.md) de PowerShell.


## <a name="other-operations"></a>Otras operaciones

Establezca las credenciales de implementación mediante el comando [`Set-SAPSecrets`](module/automation-set-sapsecrets.md) de PowerShell.

Actualice el archivo de estados de Terraform mediante el comando [`Update-TFState`](module/automation-update-tfstate.md) de PowerShell.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación del plano de control mediante PowerShell](module/automation-new-sapautomationregion.md)




