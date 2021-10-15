---
title: Solución de problemas del conector DB2
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con el conector DB2 en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: f2a9e79fb3c01938eae52b6c34687943a9472024
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390423"
---
# <a name="troubleshoot-the-db2-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector DB2 en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de Azure Database for PostgreSQL en Azure Data Factory y Azure Synapse.

## <a name="error-code-db2driverrunfailed"></a>Código de error: DB2DriverRunFailed

- **Mensaje**: `Error thrown from driver. Sql code: '%code;'`

- **Causa**: si el mensaje de error contiene la cadena "SQLSTATE=51002 SQLCODE=-805", siga la sugerencia que se incluye en [Copia de datos de DB2](./connector-db2.md#linked-service-properties).

- **Recomendación:**  Intente establecer "NULLID" en la propiedad `packageCollection`.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
