---
title: Solución de problemas del conector de Hive
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con el conector de Hive en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8412d5d49a0e457b42fce408440693fa5ffc6d4c
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067385"
---
# <a name="troubleshoot-the-hive-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de Hive en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de Hive en Azure Data Factory y Azure Synapse.

## <a name="the-performance-difference-between-the-odbc-connector-and-the-hive-connector"></a>Diferencia de rendimiento entre el conector ODBC y el conector de Hive

- **Síntomas**: La preocupación sobre la diferencia de rendimiento entre el conector ODBC y el conector de Hive. 

- **Causa**: El conector de ODBC necesita un controlador propio, lo que puede provocar la diferencia de rendimiento debido a la calidad del controlador de terceros.

- **Recomendación**: Use primero el conector de Hive. 


## <a name="unexpected-response-received-from-the-server-when-connecting-to-the-hive-server-via-odbc"></a>Respuesta inesperada recibida del servidor al conectarse al servidor de Hive a través de ODBC

- **Síntomas**: Al conectarse al servidor de Hive mediante el servicio vinculado de ODBC, recibió este mensaje de error: `ERROR [HY000] [Cloudera][DriverSupport] (1110) Unexpected response received from server. Please ensure the server host and port specified for the connection are correct and confirm if SSL should be enabled for the connection.`

- **Causa**: Utiliza la autenticación Kerberos que no es compatible con Azure Data Factory.

- **Recomendación**: Intente los pasos siguientes. Si no funcionan, revise el controlador proporcionado para resolver este problema.
    1. El archivo **kr5.ini** está en la carpeta **C:\Archivos de programa\MIT\Kerberos\bin**.
    2. También agregue `KRB5_CONFIG` y `KRB5CCNAME` a la variable del sistema.
    3. Edite el archivo **krb.ini**.
    4. Apague y reinicie la máquina virtual y el SHIR desde la máquina.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)