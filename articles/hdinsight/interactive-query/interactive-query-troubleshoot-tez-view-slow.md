---
title: Apache Ambari Tez View se carga lentamente en Azure HDInsight
description: Apache Ambari Tez View puede cargarse lentamente o no cargarse en absoluto en Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0078a01e691844e6ad2e3b4133fa4d0913b240db
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290582"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Escenario: Apache Ambari Tez View se carga lentamente en Azure HDInsight

En este artículo se describen los pasos para la solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Interactive Query en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Apache Ambari Tez View puede cargarse lentamente o no cargarse en absoluto. Al cargar Ambari Tez View, es posible que los procesos de nodos principales dejen de responder.

## <a name="cause"></a>Causa

El acceso a API de ATS para Yarn a veces puede tener un rendimiento deficiente en los clústeres creados antes de octubre de 2017, cuando el clúster tiene un gran número de trabajos de Hive ejecutados en él.

## <a name="resolution"></a>Solución

Se trata de un problema que se ha corregido en octubre de 2017. Si se vuelve a crear el clúster, el problema dejará de aparecer.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]