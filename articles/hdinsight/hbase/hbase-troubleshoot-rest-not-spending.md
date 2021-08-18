---
title: REST de Apache HBase no responde a las solicitudes en Azure HDInsight
description: Resolución del problema con REST de Apache HBase que no responde a las solicitudes en Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 0315ced51a57224113e7af490f15bcf9db894738
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291320"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Escenario: REST de Apache HBase no responde a las solicitudes en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

El servicio REST de Apache HBase no responde a las solicitudes en Azure HDInsight.

## <a name="cause"></a>Causa

La causa posible es que el servicio REST de Apache HBase esté perdiendo sockets, lo que es especialmente común cuando el servicio se ha estado ejecutando durante mucho tiempo (por ejemplo, meses). En el SDK de cliente, es posible que vea un mensaje de error similar al siguiente:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Solución

Reinicie REST de HBase con el siguiente comando después de hacer SSH al host. También puede usar acciones de script para reiniciar este servicio en todos los nodos de trabajo:

```bash
sudo service hdinsight-hbrest restart
```

Este comando detendrá el servidor de regiones de HBase en el mismo host. Puede iniciar manualmente el servidor de regiones de HBase con Ambari o bien dejar que la funcionalidad de reinicio automático de Ambari lo recupere automáticamente.

Si el problema persiste, puede instalar el siguiente script de mitigación como un trabajo CRON que se ejecute cada cinco minutos en cada nodo de trabajo. Este script de mitigación hace ping al servicio REST y lo reinicia en caso de que no responda.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]