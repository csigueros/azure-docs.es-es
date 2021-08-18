---
title: 'Se produce un error en el comando de importación o exportación de Sqoop para algunos usuarios de clústeres de ESP: Azure HDInsight'
description: 'El comando de importación o exportación de Apache Sqoop produce un error "Import Failed: java.io.IOException: The ownership on the staging directory /user/yourusername/.staging is not as expected" (Error de importación: java.io.IOException: La titularidad del directorio de almacenamiento provisional /user/sunombredeusuario/.staging no es la esperada) para algunos usuarios del clúster de ESP de Azure HDInsight.'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 2eba44d735b1b8ea9a88164ede0ca4860efcce8a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283940"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Escenario: Se produce un error en el comando de importación o exportación de Sqoop para los nombres de usuario con más de 20 caracteres en clústeres de ESP de Azure HDInsight

En este artículo se describe un problema conocido y una solución alternativa al usar los clústeres habilitados para ESP (Enterprise Security Package) de Azure HDInsight con la cuenta de almacenamiento de ADLS Gen2 (ABFS).

## <a name="issue"></a>Problema

Al ejecutar el comando de importación o exportación de Sqoop, algunos usuarios reciben el siguiente error:

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

En el ejemplo anterior, `/user/yourlongdomainuserna/.staging` muestra el nombre de usuario truncado a 20 caracteres para el nombre de usuario `yourlongdomainusername`.

## <a name="cause"></a>Causa

La longitud del nombre de usuario supera los 20 caracteres. 

Consulte [Procedimiento para sincronizar objetos y credenciales en un dominio administrado de Azure Active Directory Domain Services](../active-directory-domain-services/synchronization.md) para obtener más información.

## <a name="workaround"></a>Solución alternativa

Use un nombre de usuario con 20 caracteres o menos.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]
