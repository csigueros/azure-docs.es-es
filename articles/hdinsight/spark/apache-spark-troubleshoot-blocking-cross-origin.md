---
title: 'Error 404 de Jupyter: "Blocking Cross Origin API": Azure HDInsight'
description: Error 404 "No encontrado" del servidor de Jupyter debido a "Cross Origin Blocking API" en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: dc64a375f9010e48403493c1b13efa1e4f551bce
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112286820"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Escenario: Error 404 "No encontrado" del servidor de Jupyter debido al bloqueo de la API entre orígenes en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Apache Spark en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Al acceder al servicio Jupyter en HDInsight, verá un cuadro de error que indica "No encontrado". Si comprueba los registros de Jupyter, verá algo parecido a esto:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

También puede ver una dirección IP en el campo "Origin" (Origen) en el registro de Jupyter.

## <a name="cause"></a>Causa

Este error puede deberse a un par de cosas:

- Si ha configurado reglas de grupo de seguridad de red (NSG) para restringir el acceso al clúster. Restringir el acceso con reglas de NSG aún le permitirá acceder directamente a Apache Ambari y a otros servicios mediante la dirección IP en lugar del nombre del clúster. Sin embargo, al acceder a Jupyter, podría ver un error 404 "No encontrado".

- Si ha dado a la puerta de enlace de HDInsight un nombre de DNS personalizado distinto del `xxx.azurehdinsight.net` estándar.

## <a name="resolution"></a>Solución

1. Modifique los archivos de jupyter.py en estos dos lugares:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Busque la línea que dice `NotebookApp.allow_origin='\"https://{2}.{3}\"'` y cámbiela por `NotebookApp.allow_origin='\"*\"'`.

1. Reinicie el servicio de Jupyter desde Ambari.

1. Al escribir `ps aux | grep jupyter` en el símbolo del sistema se debe mostrar que permite la conexión de cualquier dirección URL.

Esta es una opción menos segura que la configuración ya implementada. Pero se supone que el acceso al clúster está restringido y que alguien externo tiene permitido conectarse al clúster, ya que hemos implementado el NSG.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]