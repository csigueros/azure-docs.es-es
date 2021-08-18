---
title: 'No aparece el mensaje de error en la vista de Apache Hive: Azure HDInsight'
description: Se produce un error de consulta en la vista de Apache Hive sin ningún detalle en el clúster de Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2f80a966b3d326d367d376354bc83f7d1d935812
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290857"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Escenario: mensaje de error de consulta no mostrado en la vista de Apache Hive en Azure HDInsight

En este artículo se describen los pasos para la solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Interactive Query en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

El mensaje de error en la consulta de la vista de Apache Hive tendrá un aspecto similar al siguiente, sin más información:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Causa

A veces, el mensaje de error en la consulta puede ser demasiado grande para mostrarse en la página principal de la vista de Hive.

## <a name="resolution"></a>Solución

Consulte la pestaña Notificaciones en la esquina superior derecha de la vista de Hive para ver el mensaje de error y StackTrace completos.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]