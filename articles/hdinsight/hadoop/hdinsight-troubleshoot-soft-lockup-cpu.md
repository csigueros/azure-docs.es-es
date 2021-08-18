---
title: Error "watchdog BUG soft lockup - CPU" en un clúster de Azure HDInsight
description: El error "watchdog BUG soft lockup - CPU" aparece en registros de syslog del kernel desde un clúster de Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: d2deb421dce440c1c8f949c858269fed038ac3d5
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299760"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Escenario: error "watchdog BUG soft lockup - CPU" desde un clúster de Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Los registros de syslog del kernel contienen el mensaje de error: `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Causa

Un [error](https://bugzilla.kernel.org/show_bug.cgi?id=199437) en el kernel de Linux está provocando bloqueos de software de la CPU.

## <a name="resolution"></a>Solución

Aplique una revisión del kernel. El siguiente script actualiza el kernel de Linux y reinicia las máquinas en distintos momentos durante 24 horas. Ejecute la acción de script en dos lotes. El primer lote está en todos los nodos excepto en el nodo principal. El segundo lote está en el nodo principal. No ejecute lotes en el nodo principal y en otros nodos al mismo tiempo.

1. Vaya al clúster de HDInsight en Azure Portal.

1. Vaya a las acciones de script.

1. Seleccione **Enviar nuevo** y escriba la entrada como se indica a continuación.

    | Propiedad | Value |
    | --- | --- |
    | Tipo de script | -Custom |
    | Nombre |Fix for kernel soft lock issue |
    | URI de script de Bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipos de nodo |Worker, Zookeeper |
    | Parámetros |N/D |

    Seleccione **Conservar esta acción de script...** si desea que el script se ejecute cuando se agreguen nuevos nodos.

1. Seleccione **Crear**.

1. Espere a que la ejecución se realice correctamente.

1. Ejecute la acción de script en el nodo principal con los mismos datos que en el paso 3, pero esta vez especifique: Encabezado.

1. Espere a que la ejecución se realice correctamente.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]