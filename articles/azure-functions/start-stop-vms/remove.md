---
title: Eliminar información general sobre iniciar/detener máquinas virtuales v2 (versión preliminar)
description: En este artículo se describe cómo quitar la característica iniciar/detener máquinas virtuales V2 (versión preliminar).
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 1a22faba480eb9c130cc7c883abfc3e53ef58fe5
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970164"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Cómo eliminar iniciar/detener máquinas virtuales v2 (versión preliminar)

Después de habilitar la característica iniciar/detener máquinas virtuales v2 (versión preliminar) para administrar el estado de ejecución de las máquinas virtuales de Azure, puede decidir dejar de usarla. La eliminación de esta característica puede realizarse eliminando el grupo de recursos dedicado para almacenar los siguientes recursos en la compatibilidad con las máquinas virtuales de inicio y detención V2 (versión preliminar):

- Las aplicaciones de Azure Functions
- Programación de Azure Logic Apps
- La instancia de Application Insights
- Cuenta de Azure Storage

> [!NOTE]
> Si tiene problemas durante la implementación, se produce un problema al usar Start/Stop VMs v2 (versión preliminar), o bien si tiene una pregunta relacionada, puede enviar una incidencia en [GitHub](https://github.com/microsoft/startstopv2-deployments/issues). La apertura de una incidencia de soporte técnico de Azure desde el [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) no está disponible para esta versión preliminar. 

## <a name="delete-the-dedicated-resource-group"></a>Eliminación del grupo de recursos dedicado

Para eliminar el grupo de recursos creado, siga los pasos descritos en el artículo [Eliminación de grupos de recursos y recursos en Azure Resource Manager](../../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Pasos siguientes

Para implementar esta característica, consulte [Implementación de iniciar/detener máquinas virtuales v2](deploy.md) (versión preliminar).