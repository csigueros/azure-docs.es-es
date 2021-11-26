---
title: Referencia de Bash del marco de automatización de implementación de SAP | Microsoft Docs
description: Referencia de Bash del marco de automatización de implementación de SAP en Azure
services: virtual-machines-windows
author: kimforss
manager: kimforss
keywords: Azure, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.workload: infrastructure
ms.date: 11/17/2021
ms.author: kimforss
ms.openlocfilehash: efb02895ef6d25bd94a1d0a8f8dcf11d2e0d5181
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725338"
---
# <a name="using-sap-deployment-automation-framework-shell-scripts"></a>Uso de scripts de shell del marco de automatización de la implementación de SAP

Puede implementar todos los componentes del [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) mediante scripts de shell.

## <a name="control-plane-operations"></a>Operaciones del plano de control

Puede implementar o actualizar el plano de control mediante el script de shell [prepare_region](bash/automation-prepare-region.md).

Elimine el plano de control mediante el script de shell [remove_region](bash/automation-remove-region.md).

Puede arrancar el implementador en el plano de control mediante el script de shell [install_deployer](bash/automation-install_deployer.md).

Puede arrancar la biblioteca de SAP en el plano de control mediante el script de shell [install_library](bash/automation-install_library.md).

## <a name="workload-zone-operations"></a>Operaciones de la zona de cargas de trabajo

Implemente o actualice la zona de cargas de trabajo mediante el script de shell [`install_workloadzone`](bash/automation-install_workloadzone.md).

Elimine la zona de cargas de trabajo mediante el script de shell [`remover`](bash/automation-remover.md).


## <a name="sap-system-operations"></a>Operaciones del sistema SAP

Implemente o actualice el sistema SAP mediante el script de shell [`installer`](bash/automation-installer.md).

Elimine el sistema SAP mediante el script de shell [`remover`](bash/automation-remover.md).


## <a name="other-operations"></a>Otras operaciones

Establezca las credenciales de implementación mediante el script de shell [`Set SPN secrets`](bash/automation-set-secrets.md).

Actualice el archivo de estados de Terraform mediante el script de shell [`Update Terraform state`](bash/automation-advanced_state_management.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación del plano de control mediante Bash](bash/automation-prepare-region.md)
