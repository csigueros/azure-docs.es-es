---
title: No se puede registrar en el clúster de Azure HDInsight
description: Solución de los problemas por los que no se puede iniciar sesión en el clúster de Apache Hadoop en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: eab5097ef27865b2d5d46370f60aebdac3739d24
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299688"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Escenario: no se puede registrar en el clúster de Azure HDInsight.

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

No se puede registrar en el clúster de Azure HDInsight.

## <a name="cause"></a>Causa

Los motivos pueden variar. Al iniciar sesión en los paneles de clúster o la aplicación, recuerde usar el "inicio de sesión de clúster" o las credenciales de HTTP. Cuando se conecte remotamente, use sus credenciales de Shell seguro (SSH) o del Escritorio remoto.

## <a name="resolution"></a>Solución

Para resolver problemas habituales, pruebe uno o varios de los pasos siguientes.

* Intente abrir el panel del clúster en una nueva pestaña del explorador en modo de privacidad.

* Para los clústeres Linux, si no puede recuperar las credenciales de SSH, puede [restablecer las credenciales en la interfaz de usuario de Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]