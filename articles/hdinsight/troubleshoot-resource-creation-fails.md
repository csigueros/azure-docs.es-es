---
title: Solución de problemas de creación de recursos en Azure HDInsight
description: En este artículo se indican errores comunes debidos a problemas de capacidad, así como técnicas para mitigarlos.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: a7594cd9badaaa4e81a28a0877bc8ad97e92cd08
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283904"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Solución de problemas de creación de recursos en Azure HDInsight

En este artículo se describen los pasos para solucionar los problemas que se producen al interactuar con Azure HDInsight y posibles soluciones.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Error: the deployment would exceed the quota of '800' (la implementación superaría la cuota de '800')

Azure tiene una cuota máxima de 800 implementaciones por grupo de recursos. Se aplican diferentes cuotas por grupo de recursos, suscripciones, cuentas u otros ámbitos. Por ejemplo, la suscripción puede configurarse para limitar el número de núcleos para una región. Al intentar implementar una máquina virtual con más núcleos de los permitidos, se genera un mensaje de error que indica que se superó la cuota.

Para resolver este problema, elimine las implementaciones que ya no son necesarias mediante Azure Portal, la CLI o PowerShell.

Para más información, consulte [Resolver errores de cuota de recursos](../azure-resource-manager/templates/error-resource-quota.md).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Error: the maximum node exceeded the available cores in this region (el nodo máximo superó los núcleos disponibles en esta región)

La suscripción puede configurarse para limitar el número de núcleos para una región. Al intentar implementar un recurso con más núcleos de los permitidos, se genera un mensaje de error que indica que se superó la cuota.

Para solicitar un aumento de la cuota, siga estos pasos:

1. Vaya a [Azure Portal](https://portal.azure.com) y seleccione **Ayuda y soporte técnico**.

1. Seleccione **Nueva solicitud de soporte técnico**.

1. En la página **Nueva solicitud de soporte técnico**, en la pestaña **Datos básicos**, proporcione la información siguiente:

   * **Tipo de problema**: Seleccione **Límites de servicio y suscripción (cuotas)** .
   * **Subscription** (Suscripción): Seleccione la suscripción que desea modificar.
   * **Tipo de cuota**: Seleccione **HDInsight**.

Para más información, consulte [Creación de una incidencia de soporte técnico para aumentar el núcleo](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]