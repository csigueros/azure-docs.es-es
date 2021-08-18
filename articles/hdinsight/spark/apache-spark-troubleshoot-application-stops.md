---
title: La aplicación Apache Spark Streaming se detiene después de 24 días en Azure HDInsight
description: Una aplicación Apache Spark Streaming se detiene después de ejecutarse durante 24 días y no hay errores en los archivos de registro.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 8d446063c7e8836a1163e0b6327539b055c41476
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288944"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Escenario: La aplicación Apache Spark Streaming se detiene después de ejecutarse durante 24 días en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Apache Spark en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Una aplicación Apache Spark Streaming se detiene después de ejecutarse durante 24 días y no hay errores en los archivos de registro.

## <a name="cause"></a>Causa

El valor `livy.server.session.timeout` controla el tiempo que Apache Livy debe esperar hasta que se complete una sesión. Cuando la duración de la sesión alcanza el valor de `session.timeout`, la sesión de Livy y la aplicación se terminan automáticamente.

## <a name="resolution"></a>Solución

Para los trabajos de larga duración, aumente el valor `livy.server.session.timeout` con la interfaz de usuario de Ambari. Puede acceder a la configuración de Livy desde la interfaz de usuario de Ambari mediante la dirección URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`.

Reemplace `<yourclustername>` por el nombre de su clúster de HDInsight que se muestra en el portal.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]