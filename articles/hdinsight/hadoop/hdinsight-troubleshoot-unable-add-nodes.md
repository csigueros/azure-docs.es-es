---
title: No se pueden agregar nodos a un clúster Azure HDInsight
description: Solución de los problemas por los que no se pueden agregar nodos al clúster de Apache Hadoop en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: c6eba18156828892c70136474df5a937ef43f9a3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299742"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Escenario: no se pueden agregar nodos al clúster de Azure HDInsight.

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

No se pueden agregar nodos al clúster de Azure HDInsight.

## <a name="cause"></a>Causa

Los motivos pueden variar.

## <a name="resolution"></a>Solución

Mediante la característica [Tamaño del clúster](../hdinsight-scaling-best-practices.md), calcule el número de núcleos adicionales necesarios para el clúster. La cifra se basa en el número total de núcleos de los nuevos nodos de trabajo. Luego, pruebe uno o varios de los pasos siguientes:

* Compruebe si hay algún núcleo disponible en la ubicación del clúster.

* Compruebe los núcleos disponibles en otras ubicaciones. Considere la posibilidad de volver a crear el clúster en otra ubicación con suficientes núcleos disponibles.

* Si desea aumentar la cuota de núcleos para una determinada ubicación, registre una incidencia de soporte técnico para solicitar el aumento de la cuota de núcleos de HDInsight.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]