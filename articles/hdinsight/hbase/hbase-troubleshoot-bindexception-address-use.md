---
title: 'BindException: la dirección ya está en uso en Azure HDInsight'
description: 'BindException: la dirección ya está en uso en Azure HDInsight'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 720cdf4ddcb14351076d209011a087c3b30123e6
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369472"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Escenario: BindException: la dirección ya está en uso en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

La operación de reinicio en un servidor regional de Apache HBase no se completa. Desde `region-server.log` en el directorio `/var/log/hbase` en los nodos de trabajo en los que se produce un error al iniciar el servidor regional, es posible que vea un mensaje de error parecido al siguiente:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Causa

Reinicio de los servidores regionales de Apache HBase durante una actividad de carga de trabajo intensa. Esto es lo que ocurre en segundo plano cuando un usuario inicia la operación de reinicio en el servidor regional de HBase desde la interfaz de usuario de Apache Ambari:

1. El agente de Ambari enviará una solicitud de detención al servidor de regiones.

1. El agente de Ambari espera 30 segundos a que el servidor de regiones se apague correctamente.

1. Si su aplicación sigue conectándose con el servidor de regiones, el servidor no apagará de inmediato. El tiempo de expiración de 30 segundos expira antes de que produzca el apagado.

1. Después de 30 segundos, el agente de Ambari envía un comando force-kill (`kill -9`) al servidor de regiones.

1. Debido a este cierre inesperado, no se puede liberar el puerto asociado con el proceso, aunque se termine el proceso del servidor regional, lo cual conduce a `AddressBindException`.

## <a name="resolution"></a>Solución

Reduzca la carga en los servidores regionales de HBase antes de iniciar un reinicio. También es aconsejable vaciar primero todas las tablas. Para obtener una referencia acerca de cómo vaciar tablas, consulte [HDInsight HBase: How to improve the Apache HBase cluster restart time by flushing tables](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables) (HDInsight HBase: cómo mejorar el tiempo de reinicio de los clústeres de Apache HBase vaciando tablas).

Como alternativa, pruebe a reiniciar manualmente los servidores regionales en los nodos de trabajo con los siguientes comandos:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente. Esta cuenta pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

* Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para obtener información más detallada, revise [Creación de una solicitud de soporte técnico de Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).
